---
title: Pandeia for Roman
description: Exposure Time Calculator engine for the Nancy Grace Roman Space Telescope
tags: [simulation, ETC, exposure time, Roman, WFI, Pandeia]
---

# Pandeia for Roman

このページの内容は主に以下のソースを引用・参考にしています:

- [Roman WFI Exposure Time Calculator](https://roman-docs.stsci.edu/simulation-tools-handbook-home/roman-wfi-exposure-time-calculator)
- [Pandeia for Roman](https://roman-docs.stsci.edu/simulation-tools-handbook-home/roman-wfi-exposure-time-calculator/pandeia-for-roman)
- [Overview of Pandeia](https://roman-docs.stsci.edu/simulation-tools-handbook-home/roman-wfi-exposure-time-calculator/pandeia-for-roman/overview-of-pandeia)
- [Pandeia Tutorials](https://roman-docs.stsci.edu/simulation-tools-handbook-home/roman-wfi-exposure-time-calculator/pandeia-for-roman/pandeia-tutorials)
- [Caveats of using Pandeia for Roman](https://roman-docs.stsci.edu/simulation-tools-handbook-home/roman-wfi-exposure-time-calculator/pandeia-for-roman/caveats-of-using-pandeia-for-roman)

## 概要

Pandeia は Roman 宇宙望遠鏡 Wide Field Instrument (WFI) の露出時間計算と信号対雑音比推定を行う Python ベースのエンジンです. もともと JWST 用の ETC システムとして開発されましたが, Roman にも対応し, 小さく複雑な天体シーンを生成して様々な観測戦略での信号対雑音比を計算できます.

## 主要機能

### 露出時間計算能力
- **信号対雑音比計算**: 2 次元ピクセル毎の信号対雑音マップ生成
- **露出時間最適化**: 指定した S/N に対する最適露出時間の算出
- **Multi-Accumulation 対応**: WFI の MA テーブル読み出しパターンを完全サポート
- **現実的 PSF 処理**: STPSF で事前計算された PSF ライブラリを使用

### 天体シーン設定
- **点源・拡張源**: 任意数の天体ソース挿入
- **スペクトル設定**: 平坦スペクトル, Phoenix 恒星モデル, Brown 2014 銀河モデル等
- **拡張源形状**: Flat, Sérsic, Gaussian2D, Power Law プロファイル対応
- **シーン制限**: 25 arcsec 四方以下の視野での高精度計算に最適化

### WFI 観測設定
- **フィルター**: 全撮像フィルター (F062, F087, F106, F129, F146, F158, F184, F213) 対応
- **検出器**: 単一 SCA での詳細シミュレーション
- **背景モデル**: 黄道光, 望遠鏡自己放射を含む現実的背景推定

## インストールと設定

### Python 環境の準備
```bash
# 専用 Conda 環境の作成 (推奨)
conda env create -n pandeia_roman
conda activate pandeia_roman
```

### Pandeia エンジンのインストール
```bash
# 最新版のインストール (version 2025.5)
pip install pandeia.engine==2025.5
```

### 参照データの設定
Pandeia の実行には以下の参照データが必要です:

1. **Nancy Grace Roman Space Telescope データ**
2. **Synphot ベース参照アトラス**

環境変数の設定:
```bash
# ~/.bashrc または ~/.zshrc に追加
export pandeia_refdata="/path/to/pandeia/data/pandeia_data-CURRENT_VERSION-roman-engine/"
export PYSYN_CDBS="/path/to/synphot/data/"
```

### インストール確認
```python
import pandeia.engine
pandeia.engine.pandeia_version()

# 出力例:
# Pandeia Engine version: 2025.5
# Pandeia RefData version: 2025.5
# Pysynphot Data: /your/data/directory/for/synphot/grp/redcat/trds/
```

## 基本的な使用方法

### デフォルト設定での S/N 計算
```python
from pandeia.engine.perform_calculation import perform_calculation
from pandeia.engine.calc_utils import build_default_calc

# Roman WFI 撮像モードのデフォルト設定
calc = build_default_calc('roman', 'wfi', 'imaging')

# 観測設定
FILTER = 'f129'
mag = 25  # AB等級
nexp = 3  # 露出回数

# パラメータ設定
calc['scene'][0]['spectrum']['normalization']['norm_flux'] = mag
calc['scene'][0]['spectrum']['normalization']['norm_fluxunit'] = 'abmag'
calc['configuration']['detector']['nexp'] = nexp
calc['configuration']['instrument']['filter'] = FILTER

# 計算実行
report = perform_calculation(calc)
SNR = report['scalar']['sn']
print(f'Estimated S/N: {SNR:.2f}')
```

### 恒星スペクトルを使用した計算
```python
# Phoenix A0V 恒星モデルの使用
calc['scene'][0]['spectrum']['sed']['sed_type'] = 'phoenix'
calc['scene'][0]['spectrum']['sed']['key'] = 'a0v'

report = perform_calculation(calc)
SNR = report['scalar']['sn']
print(f'A0V Star S/N: {SNR:.2f}')
```

## 実用的なチュートリアル

### チュートリアル 1: 限界等級の計算
指定した S/N に対応する限界等級を計算する例:

```python
from scipy import interpolate
import numpy as np

def compute_limiting_magnitude(filter_name, target_sn, nexp, mag_range=(18, 30)):
    """指定した S/N での限界等級を計算"""
    calc = build_default_calc('roman', 'wfi', 'imaging')
    calc['configuration']['instrument']['filter'] = filter_name
    calc['configuration']['detector']['nexp'] = nexp
    calc['scene'][0]['spectrum']['normalization']['norm_fluxunit'] = 'abmag'

    magnitudes = np.arange(mag_range[0], mag_range[1]+1, 1)
    snr_values = []

    for mag in magnitudes:
        calc['scene'][0]['spectrum']['normalization']['norm_flux'] = mag
        report = perform_calculation(calc)
        snr_values.append(report['scalar']['sn'])

    # 補間による限界等級算出
    interpolator = interpolate.interp1d(snr_values, magnitudes)
    limiting_mag = interpolator(target_sn)

    return limiting_mag

# 使用例: F129 フィルター, S/N=5, 10 露出
limiting_mag = compute_limiting_magnitude('f129', 5.0, 10)
print(f'Limiting magnitude: {limiting_mag:.2f} AB mag')
```

### チュートリアル 2: 最適露出回数の算出
```python
from scipy.optimize import minimize_scalar

def compute_optimal_exposures(filter_name, target_sn, magnitude):
    """指定した等級と S/N に対する最適露出回数を計算"""
    calc = build_default_calc('roman', 'wfi', 'imaging')
    calc['configuration']['instrument']['filter'] = filter_name
    calc['scene'][0]['spectrum']['normalization']['norm_flux'] = magnitude
    calc['scene'][0]['spectrum']['normalization']['norm_fluxunit'] = 'abmag'

    def sn_difference(nexp):
        calc['configuration']['detector']['nexp'] = int(nexp)
        report = perform_calculation(calc)
        return (target_sn - report['scalar']['sn'])**2

    # 最適化実行
    result = minimize_scalar(sn_difference, bounds=(1, 1000), method='bounded')
    optimal_nexp = int(result['x'])

    # 最終確認
    calc['configuration']['detector']['nexp'] = optimal_nexp
    final_report = perform_calculation(calc)

    return optimal_nexp, final_report

# 使用例: 26 等級, S/N=20 達成に必要な露出回数
nexp, report = compute_optimal_exposures('f129', 20.0, 26.0)
print(f'Required exposures: {nexp}')
print(f'Achieved S/N: {report["scalar"]["sn"]:.2f}')
print(f'Total exposure time: {report["scalar"]["total_exposure_time"]:.2f} sec')
```

### チュートリアル 3: 拡張源の S/N 計算
```python
# Sérsic プロファイル銀河の設定
calc = build_default_calc('roman', 'wfi', 'imaging')

# 拡張源設定
calc['scene'][0]['shape']['geometry'] = 'sersic'
calc['scene'][0]['shape']['major'] = 2.0  # arcsec
calc['scene'][0]['shape']['minor'] = 1.5  # arcsec
calc['scene'][0]['shape']['sersic_index'] = 1.0  # 指数円盤銀河

# Brown 2014 銀河スペクトルの使用
calc['scene'][0]['spectrum']['sed']['sed_type'] = 'brown2014'
calc['scene'][0]['spectrum']['sed']['key'] = 'E'  # 楕円銀河

# 観測設定
calc['configuration']['instrument']['filter'] = 'f146'
calc['configuration']['detector']['nexp'] = 5

report = perform_calculation(calc)
print(f'Extended source S/N: {report["scalar"]["sn"]:.2f}')
```

## 出力データの理解

### Pandeia レポート構造
```python
report = perform_calculation(calc)

# 主要な出力パラメータ
print("=== スカラー値 ===")
print(f"信号対雑音比: {report['scalar']['sn']:.2f}")
print(f"総露出時間: {report['scalar']['total_exposure_time']:.2f} sec")
print(f"背景フラックス: {report['scalar']['bg_total']:.2e} e-/sec")

print("\n=== 2D データ ===")
print(f"S/N マップ形状: {report['2d']['sn'].shape}")
print(f"信号マップ形状: {report['2d']['detector'].shape}")

# 開口測光結果 (WFI 用)
if 'aperture_photometry' in report:
    print(f"\n=== 開口測光 ===")
    for ap in report['aperture_photometry']:
        print(f"開口半径 {ap['radius']:.1f} pix: S/N = {ap['sn']:.2f}")
```

## 制限事項と注意点

### 計算制限
- **視野サイズ**: 25 arcsec 四方以下での使用を推奨
- **PSF サイズ**: 3 arcsec × 3 arcsec の PSF スタンプ使用
- **検出器**: 単一 SCA のみサポート (マルチ検出器は STIPS 使用)
- **視野依存性**: PSF の視野依存性は考慮されない

### 背景モデルの制限
- **1.2 μm 以下**: 黄道光成分が過大評価される既知の問題
- **望遠鏡自己放射**: Roman 用に更新済みだが打ち上げ後に再校正予定
- **JWST 背景**: 一部の背景成分は JWST モデルを流用

### 推奨される代替ツール
大視野や複数検出器の計算には以下を使用:
- **STIPS**: 全 WFI 検出器アレイの撮像シミュレーション
- **Roman I-Sim**: より包括的な画像シミュレーター

## 他ツールとの連携

### STPSF との組み合わせ
```python
# STPSF で詳細 PSF を生成後, Pandeia で S/N 計算
import stpsf

# PSF 詳細解析用
wfi = stpsf.roman.WFI()
psf = wfi.calc_psf(filter='F129', fov_pixels=101)

# Pandeia で同条件の S/N 計算
calc = build_default_calc('roman', 'wfi', 'imaging')
calc['configuration']['instrument']['filter'] = 'f129'
report = perform_calculation(calc)
```

### STIPS との役割分担
- **Pandeia**: 小視野高精度計算, 露出時間最適化
- **STIPS**: 大視野全検出器シミュレーション, 深宇宙サーベイ

## バージョン情報と今後の開発

### 現在のバージョン
- **Pandeia Engine**: 2025.5 (2025年5月21日リリース)
- **Roman Module**: Phase C Cycle 9 装置参照に基づく
- **参照データ**: GSFC Instrument Reference Information

### 開発状況
Roman モジュールは活発に開発中で, 以下の改善が予定されています:
- 検出器特性のより正確なモデル化
- 背景モデルの精度向上
- 打ち上げ後の実測データによる校正

## リソースとサポート

### ドキュメント
- **Roman 用 Pandeia**: [STScI Roman Docs](https://roman-docs.stsci.edu/simulation-tools-handbook-home/roman-wfi-exposure-time-calculator/pandeia-for-roman)
- **JWST 用 Pandeia**: [JDox Pandeia Tutorial](https://jwst-docs.stsci.edu/jwst-exposure-time-calculator-overview/jwst-etc-pandeia-engine-tutorial)
- **設定辞書**: [Pandeia Configuration](https://jwst-docs.stsci.edu/jwst-exposure-time-calculator-overview/jwst-etc-pandeia-engine-tutorial/pandeia-configuration-dictionaries)

### ソフトウェア
- **Web インターフェース**: [Roman ETC](https://roman.etc.stsci.edu/)
- **インストールガイド**: [Pandeia Engine Installation](https://outerspace.stsci.edu/display/PEN/Pandeia+Engine+Installation)
- **既知の問題**: [Known Issues Documentation](https://stsci.service-now.com/roman?id=kb_category&kb_category=7e590661c3941a9047233afb05013100)

### サポート
- **Roman Help Desk**: [stsci.service-now.com/roman](https://stsci.service-now.com/roman)
- **GitHub Issues**: 各ツールの GitHub リポジトリ
- **STScI Roman Pages**: [stsci.edu/roman](https://www.stsci.edu/roman)

---

**更新情報**: Pandeia for Roman version 2025.5 および Roman Technical Information に基づく (2025年10月現在)
