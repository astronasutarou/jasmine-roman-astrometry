---
title: An overview of STIPS for Roman
description: Space Telescope Imaging Product Simulator for the Nancy Grace Roman Space Telescope
tags: [simulation, imaging, Roman, WFI, STIPS]
---

# STIPS for Roman

## 概要

STIPS (Space Telescope Imaging Product Simulator) は Roman 宇宙望遠鏡 Wide Field Instrument (WFI) の全検出器アレイの画像を高速生成する仕組みツールです. STIPS は Pandeia 露出時間計算機と STPSF 点広がり関数ジェネレータに依存して観測シミュレーションを実行します.

## 主要機能

### 観測シミュレーション能力
- **検出器範囲**: 1-18 検出器の任意選択
- **フィルター対応**: 全 WFI 撮像フィルター (F062, F087, F106, F129, F146, F158, F184, F213)
- **天体ソース**: 点源・拡張源の任意数挿入
- **背景設定**: 天空背景推定値の指定
- **出力**: Level 2 (L2) データプロダクト相当を直接生成

### ワークフロー
1. **天体シーン作成**: ユーザー定義カタログまたはシミュレートカタログ
2. **観測設計**: 装置パラメータ, 露出時間, オフセット設定
3. **観測実行**: PSF 適用, ノイズ追加, FITS ファイル出力

## インストールと設定

### 必要条件
```bash
# Pandeia のインストール (version 3.1 以上必要)
pip install pandeia.engine==2025.5

# STIPS のインストール (Conda 環境推奨)
# 詳細は ReadTheDocs を参照
```

### 参照データ
STIPS, Pandeia, STPSF の実行には複数の参照データセットが必要です. 詳細なダウンロード手順は [ReadTheDocs インストールガイド](https://stsci-stips.readthedocs.io/en/stable/installation.html#downloading-required-reference-data) を参照してください.

### インストール確認
```python
import stips
print(stips.__env__report__)

# 出力例:
# STIPS Version x.y.z with Data Version x.y.z at /path/to/stips_data
# Pandeia version a.b.c with Data Version a.b.c at /path/to/pandeia_refdata
# stpsf Version d.e.f with Data Version d.e.f at /path/to/stpsf_data_path
```

## 基本的な使い方

### Tutorial 1: 基本的な STIPS 使用

#### A. 天体シーンの生成
```python
from astropy.io import fits

# 2 つの点源を含むカタログ作成
cols = []
cols.append(fits.Column(name='id',   array=[1, 2],         format='K'))  # Object ID
cols.append(fits.Column(name='ra',   array=[90.02, 90.03], format='D'))  # RA (度)
cols.append(fits.Column(name='dec',  array=[29.98, 29.97], format='D'))  # DEC (度)
cols.append(fits.Column(name='flux', array=[0.001, 0.005], format='D'))  # Flux (Jy)

hdu = fits.BinTableHDU.from_columns(cols)
hdu.writeto('catalog.fits', overwrite=True)
```

#### B. 観測設定
```python
from stips.observation_module import ObservationModule

# 観測パラメータ辞書
obs = {
    'instrument': 'WFI',
    'filters': ['F129'],
    'detectors': 1,
    'background': 'pandeia',
    'observations_id': 42,
    'exptime': 300,
    'offsets': [{'offset_id': 1,
                 'offset_centre': False,
                 'offset_ra': 0.0,
                 'offset_dec': 0.0,
                 'offset_pa': 0.0}],
    'residuals': [{'residual_flat': True,
                   'residual_dark': True,
                   'residual_cosmic': False,
                   'residual_poisson': True,
                   'residual_readnoise': True}]
}

# 観測オブジェクト作成
obm = ObservationModule(obs,
                       ra=90, dec=30, pa=0,
                       seed=42, cores=6)
```

#### C. シミュレーション実行
```python
import numpy as np
import matplotlib.pyplot as plt

# 観測初期化
obm.nextObservation()

# カタログ追加
cat_name = obm.addCatalogue('catalog.fits')

# エラー残差追加
psf_file = obm.addError()

# 観測完了
fits_file, mosaic_file, params = obm.finalize(mosaic=False)
print(f"Output FITS file is {fits_file}")

# 結果表示
data = fits.open(fits_file)[1].data
plt.imshow(data, origin='lower', vmin=np.percentile(data, 1),
vmax=np.percentile(data, 99))
plt.colorbar()
plt.show()
```

### Tutorial 2: シミュレートカタログの生成

#### 恒星個体群の生成
```python
from stips.scene_module import SceneModule

obs_prefix = 'simulation_example'
obs_ra = 150.0
obs_dec = -2.5

scm = SceneModule(out_prefix=obs_prefix, ra=obs_ra, dec=obs_dec)

# 恒星個体群パラメータ
stellar_parameters = {
    'n_stars': 100,
    'age_low': 7.5e12,
    'age_high': 7.5e12,
    'z_low': -2.0,
    'z_high': -2.0,
    'imf': 'kroupa',
    'binary_fraction': 0.1,
    'distribution': 'uniform',
    'clustered': False,
    'radius': 200,      # arcsec
    'distance_low': 20000,
    'distance_high': 20000,
    'offset_ra': 0.0,
    'offset_dec': 0.0
}

stellar_cat_file = scm.CreatePopulation(stellar_parameters)
```

#### 銀河個体群の生成
```python
# 銀河個体群パラメータ
galaxy_parameters = {
    'n_gals': 10,
    'z_low': 0.0,
    'z_high': 0.2,
    'rad_low': 0.01,
    'rad_high': 2.0,
    'sb_v_low': 30.0,
    'sb_v_high': 25.0,
    'distribution': 'uniform',
    'clustered': False,
    'radius': 200,      # arcsec
    'offset_ra': 0.0,
    'offset_dec': 0.0
}

galaxy_cat_file = scm.CreateGalaxies(galaxy_parameters)
```

### Tutorial 3: 観測パラメータの変更

```python
# 異なるオフセットとノイズ残差での観測
offset_2 = {
    'offset_id': 2,
    'offset_centre': False,
    'offset_ra': 10.0,
    'offset_dec': 0.0,
    'offset_pa': 27
}

residuals_2 = {
    'residual_flat': True,
    'residual_dark': False,
    'residual_cosmic': False,
    'residual_poisson': False,
    'residual_readnoise': True
}

# 新しい観測の実行
obm.nextObservation()  # 必須: 次の観測に移動
# カタログ追加とシミュレーション実行...
```

### Tutorial 4: PSF と人工源の追加

#### PSF ライブラリの使用
```python
import stips
from astropy.io import fits

# PSF ライブラリファイルの読み込み
with fits.open('psf_WFI_2.0.0_F129_wfi01.fits') as hdul:
    test_psf = stips.utilities.makePSF.make_epsf(hdul[0].data[0])

# PSF の表示
vmin = np.percentile(test_psf, 5)
vmax = np.percentile(test_psf, 95)
plt.imshow(test_psf, origin='lower', vmin=vmin, vmax=vmax)
plt.xlabel('X [pixels]')
plt.ylabel('Y [pixels]')
plt.show()
```

#### 人工点源の追加
```python
import matplotlib.patches as patches

# 既存画像に PSF を追加
PSF_UPSCALE = 4            # STIPS のデフォルト upscaling 係数

# PSF 中心とサイズの計算
psf_middle = len(test_psf) / 2.0
psf_size = len(test_psf) / PSF_UPSCALE

# 指定位置に PSF を挿入
location_px = (1000, 1500) # ピクセル座標
flux_jy = 1e-3             # Jansky 単位

# PSF を画像に追加 (詳細な実装は ReadTheDocs を参照)
```

## 観測辞書の詳細設定

| パラメータ | 型 | 説明 | 選択肢/範囲 |
|------------|----|----- |-------------|
| `instrument` | String | 装置名 | 'WFI' |
| `filters` | List | フィルター名 | F062, F087, F106, F129, F146, F158, F184, F213 |
| `detectors` | Integer | 検出器数 | 1-18 |
| `observations_id` | Integer | 観測 ID | 任意の整数 |
| `exptime` | Float | 露出時間 (秒) | 任意の浮動小数点数 |
| `distortion` | Boolean | 幾何歪みの適用 | True/False |
| `background` | String | 背景モデル | 'pandeia', 'none', 'min', 'max', 'med', カスタム値 |
| `offsets` | List | オフセット設定 | 辞書のリスト |
| `residuals` | List | ノイズ残差設定 | 辞書のリスト |

### オフセット辞書
```python
offset = {
    'offset_id':     1,       # オフセット ID
    'offset_centre': False,   # 中心化フラグ
    'offset_ra':     2.0,     # RA オフセット (度)
    'offset_dec':    0.0,     # DEC オフセット (度)
    'offset_pa':     0.5      # 位置角オフセット (度)
}
```

### 残差辞書
```python
residuals = {
    'residual_flat':    True,    # フラットフィールド残差
    'residual_dark':    True,    # ダーク残差
    'residual_cosmic':  False,   # 宇宙線除去残差
    'residual_poisson': True,    # ポアソンノイズ残差
    'residual_readnoise': True   # 読み出しノイズ残差
}
```

## STIPS 実行プロセス

### 内部処理手順
1. **観測初期化**: 指定検出器・フィルターでの観測設定
2. **PSF ライブラリ読み込み**: 各検出器の 3×3 グリッド PSF (4 倍オーバーサンプリング)
3. **ソースカタログ処理**:
   - 双線形補間による最適 PSF 形状生成
   - 双三次補間による PSF 中心配置
4. **ノイズ・背景追加**: 指定されたノイズ残差と背景レベルの追加
5. **FITS ファイル出力**: シミュレーション画像の保存

### PSF 補間
- **第 1 段階**: 3×3 PSF ライブラリの双線形補間 (整数ピクセル位置)
- **第 2 段階**: オーバーサンプルピクセルグリッドの双三次補間 (サブピクセル位置)

### 出力ファイル
- **シミュレーション画像**: FITS 形式
- **観測カタログ**: テキスト形式
- **PSF 画像**: FITS 形式
- **クイックルック画像**: PNG 形式

## 使用上の制限事項と注意点

### Level 2 データ出力の制限
- **出力レベル**: Level 2 (L2) データプロダクト相当のみ
- **理由**: 高速シミュレーションのため
- **代替**: Level 1 (L1) データが必要な場合は **Roman I-Sim** を使用

### 明るい天体の PSF 制限
STIPS の PSF モデル半径は標準で 22 WFI ピクセルに制限されています:

| 設定 | 説明 | 半径 (ピクセル) | 半径 (arcsec) |
|------|------|----------------|----------------|
| `default` | 全天体に適用される標準 PSF 半径 | 22 | 2.42 |
| `psf_bright_limit` | この等級より明るい星は大半径で計算 | 44 | 4.84 |
| `psf_xbright_limit` | この等級より明るい星は特大半径で計算 | 88 | 9.68 |

**注意**: 明るい天体の計算時間は標準の 4 倍 (bright) から 16 倍 (extra-bright) に増加します.

### その他の制限事項
- **ピクセル飽和**: 現在サポートされていません
- **非線形性残差**: 現在サポートされていません
- **MA テーブル**: Multi Accumulation テーブル非対応 (固定露出時間のみ)

## カタログ形式

### サポートされる形式
- **IPAC テキスト**: [IPAC Table 形式](https://irsa.ipac.caltech.edu/applications/DDGEN/Doc/ipac_tbl.html)
- **FITS BinTable**: [Astropy Table](https://docs.astropy.org/en/stable/table/) API 対応

### 必須カラム
- `id`: オブジェクト ID
- `ra`: 赤経 (度)
- `dec`: 赤緯 (度)
- `flux`: フラックス (Jansky)

## リソースとサポート

### ドキュメント
- **ReadTheDocs**: [stsci-stips.readthedocs.io](https://stsci-stips.readthedocs.io/en/stable/)
- **GitHub リポジトリ**: [github.com/spacetelescope/STScI-STIPS](https://github.com/spacetelescope/STScI-STIPS)
- **既知の問題**: [ReadTheDocs バグリスト](https://stsci-stips.readthedocs.io/en/stable/bugs.html)

### サポート
- **Roman Help Desk**: [stsci.service-now.com/roman](https://stsci.service-now.com/roman)
- **バージョン**: STIPS version 2.3.0 (2025年6月27日リリース)

### 引用文献
STIPS を使用した研究では以下の論文の引用を推奨します:

STIPS Development Team et al 2024, "[STIPS: The Nancy Grace Roman Space Telescope Imaging Product Simulator](https://ui.adsabs.harvard.edu/abs/2024arXiv241111978S/abstract)", PASP 136 124502
