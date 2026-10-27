---
title: An overview of STPSF for Roman
description: Point spread function simulation tool for the Nancy Grace Roman Space Telescope
tags: [simulation, psf, point-spread function, Roman, WFI]
---

# STPSF for Roman

このページの内容は主に以下のソースを引用・参考にしている:

- [STPSF for Roman](https://roman-docs.stsci.edu/simulation-tools-handbook-home/stpsf-for-roman)
- [Overview of STPSF](https://roman-docs.stsci.edu/simulation-tools-handbook-home/stpsf-for-roman/overview-of-stpsf)
- [STPSF Tutorials](https://roman-docs.stsci.edu/simulation-tools-handbook-home/stpsf-for-roman/stpsf-tutorials)
- [Caveats of using STPSF for Roman](https://roman-docs.stsci.edu/simulation-tools-handbook-home/stpsf-for-roman/caveats-of-using-stpsf-for-roman)
- [STPSF on readthedocs](https://stpsf.readthedocs.io/en/latest/index.html)
- [STPSF v2.3.0 release](https://github.com/spacetelescope/stpsf/releases/tag/2.3.0) (2026年10月27日)


## 概要

STPSF は JWST と Roman 宇宙望遠鏡の点広がり関数 (Point Spread Function, PSF) をシミュレーションするための Python パッケージである. 効率的な計算手法と最新のミッション固有参照データを使用し, 幅広い観測モードと科学シナリオに対応する.

## STPSF 2.3.0 (2026年10月27日)

STPSF 2.3.0 では, NASA GSFC が2026年2月から3月に計算した最終打上げ前の Phase C WFI 光学モデルが導入された. 撮像フィルターについては各検出器25視野点の Zernike 係数, WFI 固有の検出器別高空間周波数光路差 (Optical Path Difference, OPD) マップ, 検出器内の視野点に依存する瞳像を用いる. また, ピクセル間容量結合 (Interpixel Capacitance, IPC) が既定で適用される. この版には対応する新しい参照データパッケージが必要であり, 旧版のデータをそのまま使用できない.

## 対応機能

### Wide Field Instrument (WFI) - 完全対応
- **撮像モード**: 全フィルターでの撮像 PSF 計算
- **分光モード**: Prism と Grism モード (分散なし PSF)
- **視野依存収差**: 撮像は18検出器 × 25視野点, Prism と Grism は18検出器 × 5視野点での Zernike 係数補間
- **検出器効果**: 電荷拡散と IPC を畳み込みとして適用

### Coronagraph Instrument - 非対応

旧版に含まれていた初期の Roman Coronagraph 試作モデルは廃止され, STPSF 2.3.0 で削除された. Coronagraph のシミュレーションには Roman Coronagraph Community Participation Program が開発する [corgisim](https://github.com/roman-corgi/corgisim) を参照する.

## インストールと基本使用

### インストール
```bash
pip install stpsf
```

STPSF 2.3.0 には同じ版の新しい参照データが必要である. 初回実行時の既定の自動取得先は `$HOME/data/stpsf-data` であり, 別の場所に配置する場合は環境変数 `STPSF_PATH` を設定する.

### 基本的な PSF 計算
```python
import stpsf
import matplotlib.pyplot as plt

# 設定
plt.rcParams['figure.figsize'] = (8, 10)
plt.rcParams['image.interpolation'] = 'nearest'

# WFI インスタンス作成
wfi = stpsf.roman.WFI()

# デフォルト設定での PSF 計算 (F062 フィルター)
default_psf = wfi.calc_psf(display=True)
```

### パラメータ指定での PSF 計算
```python
wfi = stpsf.roman.WFI()

# パラメータ設定
wfi.filter = 'GRISM1'
wfi.detector = 'SCA14'
wfi.detector_position = (1024, 1024)

# G0V 星のスペクトルを使用
src = stpsf.specFromSpectralType('G0V', catalog='phoenix')
psf = wfi.calc_psf(source=src, nlambda=10, display=True)
```

## PSF 解析とデータ出力

### PSF プロファイルと Encircled Energy
```python
# PSF の径方向プロファイルと Encircled Energy の表示
stpsf.display_profiles(psf)

# PSF データの保存
psf.writeto('./roman_psf.fits', overwrite=True)

# 検出器ピクセルでの表示
stpsf.display_psf(psf, ext='DET_SAMP')
```

### 利用可能な属性の確認
```python
wfi = stpsf.roman.WFI()

# フィルター一覧
print("WFI Filter List:")
print(wfi.filter_list)

# 検出器一覧
print("WFI Detector List:")
print(wfi.detector_list)

# 瞳マスク一覧
print("WFI Pupil Mask List:")
print(wfi.pupil_mask_list)
```

### オーバーサンプリングと PSF 位置調整
```python
# デフォルトの均等オーバーサンプリング PSF
wfi = stpsf.roman.WFI()
wfi.filter = 'F062'
wfi.detector = 'SCA18'
wfi.detector_position = (2044, 2044)

oversample = 8
fov = 3 # DET_SAMP extension のピクセル数
psf_default = wfi.calc_psf(oversample=oversample, fov_pixels=fov)

# ピクセル中心に配置した均等オーバーサンプリング PSF
wfi.pixelscale /= oversample
over = 1
fov = 25 # 全エクステンションのピクセル数
psf_centered = wfi.calc_psf(oversample=over, fov_pixels=fov)
```

## Roman 固有の入力データ

### データソース

STPSF 2.3.0 の WFI モデルは NASA GSFC が2026年春に作成した Phase C 光学モデルに基づく:

- **フィルター透過曲線**: WFI の各フィルターの詳細な透過特性
- **瞳マスク**: 撮像では各フィルター・検出器・視野点に対応する瞳像
- **視野依存収差**: 撮像では18検出器 × 25視野点, Prism と Grism では18検出器 × 5視野点
- **高空間周波数成分**: WFI 固有の検出器別 OPD マップ

### 波面誤差補間

STPSF は位置と波長について Zernike 係数を補間し, 任意の有効な検出器位置と波長で PSF を計算する. 撮像モードでは与えられた Zernike 係数 Z1-Z45 から Z46-Z210 とそれより高次の総寄与を再構成し, 旧版で使用していた HST 近似ではなく WFI 固有の OPD マップを用いる.

## 高度な機能

### PSF グリッド (GriddedPSFModel)
```python
# 効率的な多点 PSF 生成のための補間グリッド
# 詳細は readthedocs の PSF Grids ドキュメントを参照
```

### PSF 特性計算ツール
- **FWHM** (Full Width at Half Maximum)
- **Encircled Energy**
- **Strehl 比**
- **径方向プロファイル**
- **重心位置**

## 使用上の注意点と制限事項

### 検出器レベル効果

STPSF 2.3.0 は電荷拡散と IPC を PSF に畳み込む. IPC は既定で有効であり, `wfi.options['add_ipc']` で切り替えられる. 一方, ピクセル内感度変化, 飽和, 非線形性, 宇宙線, 読み出しノイズなどを含む完全な検出器画像は生成しない. 画像レベルの効果が必要な場合は **STIPS** または **Roman I-Sim** を使用する.

### 幾何学的歪み

WFI の瞳像には幾何学的歪みが含まれるため, Roman WFI PSF には既定で歪みが適用され, 無効化できない.

### 分散 PSF の制限
- **非対応**: 分散分光 PSF (Prism/Grism の波長分散効果) は計算されません
- **推奨手法**: 単色 PSF を複数計算し, 適切な分散モデルと組み合わせ

### エイリアシング
- **大視野**: 700-800 ピクセルの大視野でエイリアシングアーティファクトが発生する可能性
- **緩和策**: より大きなピクセル数の瞳画像使用 (現在未サポート)

### PSF サンプリング
- **均等オーバーサンプリング**: PSF 中心が 4 ピクセルの交点に配置され, ピクセル中心ではない
- **回避策**: オーバーサンプリングではなく WFI のピクセルスケール変更を使用

### Roman Coronagraph

Roman Coronagraph は STPSF のモデル対象外である. 旧試作モデルは廃止後に削除されており, Coronagraph の解析では `corgisim` など専用の公式コミュニティツールを使用する.

## リソースとサポート

### 追加チュートリアル
- **GitHub リポジトリ**: [STPSF チュートリアル](https://github.com/spacetelescope/stpsf/tree/develop/notebooks)
- **Roman 専用ノートブック**: [Roman Tutorial](https://github.com/spacetelescope/stpsf/blob/develop/notebooks/STPSF-Roman_Tutorial.ipynb)
- **ReadTheDocs**: [Roman Instrument Model](https://stpsf.readthedocs.io/en/latest/roman.html)

### インストール手順
詳細は [ReadTheDocs インストールガイド](https://stpsf.readthedocs.io/en/latest/installation.html) を参照してください.

### サポート
- **Roman Help Desk**: [stsci.service-now.com/roman](https://stsci.service-now.com/roman)
- **GitHub Issues**: [github.com/spacetelescope/stpsf](https://github.com/spacetelescope/stpsf)
- **ReadTheDocs API**: [stpsf.readthedocs.io](https://stpsf.readthedocs.io/en/latest/api_reference.html)
