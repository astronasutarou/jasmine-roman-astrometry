---
title: An overview of STPSF for Roman
description: Point spread function simulation tool for the Nancy Grace Roman Space Telescope
tags: [simulation, psf, point-spread function, Roman, WFI]
---

# STPSF for Roman

## 概要

STPSF は JWST と Roman 宇宙望遠鏡の点広がり関数 (Point Spread Function, PSF) をシミュレーションするための Python パッケージです。効率的な計算手法と最新のミッション固有参照データを使用し、幅広い観測モードと科学シナリオに対応します。

## 対応機能

### Wide Field Instrument (WFI) - 完全対応
- **撮像モード**: 全フィルターでの撮像 PSF 計算
- **分光モード**: Prism と Grism モード (分散なし PSF)
- **視野依存収差**: 18 検出器 × 5 視野点での Zernike 係数補間

### Coronograph Instrument - 制限あり
- **現状**: Shaped Pupil Coronagraph (SPC) のプロトタイプのみ
- **注意**: 2017 年以降更新されておらず、実装は不完全

## インストールと基本使用

### インストール
```bash
pip install stpsf
```

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
fov = 3  # DET_SAMP extension のピクセル数
psf_default = wfi.calc_psf(oversample=oversample, fov_pixels=fov)

# ピクセル中心に配置した均等オーバーサンプリング PSF
wfi.pixelscale /= oversample
over = 1
fov = 25  # 全エクステンションのピクセル数
psf_centered = wfi.calc_psf(oversample=over, fov_pixels=fov)
```

## Roman 固有の入力データ

### データソース
[Cycle 9 装置参照情報](https://roman.gsfc.nasa.gov/science/Roman_Reference_Information.html) (NASA GSFC Roman チーム提供) に基づく:

- **フィルター透過曲線**: WFI の各フィルターの詳細な透過特性
- **瞳マスク**: 検出器とフィルターごとに変化する入射瞳の形状
- **視野依存収差**: 18 検出器 × 5 視野点での Noll Zernike 係数 (Z4-Z22)

### 波面誤差補間
STPSF は Zernike 係数を位置と波長で補間し、任意の検出器位置での総波面誤差を計算します。

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

### 検出器レベル効果 (非対応)
STPSF では以下の効果は **モデル化されません**:
- 幾何学的歪み
- ピクセル MTF (Modulation Transfer Function)
- ピクセル内感度変化
- ピクセル間容量結合
- ノイズ源

これらの効果が必要な場合は **STIPS** などの他ツールを使用してください。

### 光学系の近似
- **主鏡研磨誤差**: Hubble Space Telescope (HST) の光学路差マップを近似として使用
- **将来更新予定**: Roman 固有データが利用可能になり次第更新

### 分散 PSF の制限
- **非対応**: 分散分光 PSF (Prism/Grism の波長分散効果) は計算されません
- **推奨手法**: 単色 PSF を複数計算し、適切な分散モデルと組み合わせ

### エイリアシング
- **大視野**: 700-800 ピクセルの大視野でエイリアシングアーティファクトが発生する可能性
- **緩和策**: より大きなピクセル数の瞳画像使用 (現在未サポート)

### PSF サンプリング
- **均等オーバーサンプリング**: PSF 中心が 4 ピクセルの交点に配置され、ピクセル中心ではない
- **回避策**: オーバーサンプリングではなく WFI のピクセルスケール変更を使用

### Roman Coronagraph の制限
- **SPC のみ**: Shaped Pupil Coronagraph 観測モードのみ
- **静的シミュレーション**: 収差なし静的波面、リレー光学系なし、DM 制御なし

## リソースとサポート

### 追加チュートリアル
- **GitHub リポジトリ**: [STPSF チュートリアル](https://github.com/spacetelescope/stpsf/tree/develop/notebooks)
- **Roman 専用ノートブック**: [Roman Tutorial](https://github.com/spacetelescope/stpsf/blob/develop/notebooks/STPSF-Roman_Tutorial.ipynb)
- **ReadTheDocs**: [Roman Instrument Model](https://stpsf.readthedocs.io/en/latest/roman.html)

### インストール手順
詳細は [ReadTheDocs インストールガイド](https://stpsf.readthedocs.io/en/latest/installation.html) を参照してください。

### サポート
- **Roman Help Desk**: [stsci.service-now.com/roman](https://stsci.service-now.com/roman)
- **GitHub Issues**: [github.com/spacetelescope/stpsf](https://github.com/spacetelescope/stpsf)
- **ReadTheDocs API**: [stpsf.readthedocs.io](https://stpsf.readthedocs.io/en/latest/api_reference.html)
