---
title: I-Sim for Roman
description: GalSim-based high-fidelity image simulator for the Nancy Grace Roman Space Telescope
tags: [simulation, imaging, Roman, WFI, I-Sim, GalSim]
---

# Roman I-Sim &ndash; The Roman Image Simulator

このページの内容は主に以下のソースを引用・参考にしている:

- [Roman I-Sim - The Roman Image Simulator](https://roman-docs.stsci.edu/simulation-tools-handbook-home/roman-i-sim-the-roman-image-simulator)
- [Overview of Roman I-Sim](https://roman-docs.stsci.edu/simulation-tools-handbook-home/roman-i-sim-the-roman-image-simulator/overview-of-roman-i-sim)
- [Roman I-Sim Tutorials](https://roman-docs.stsci.edu/simulation-tools-handbook-home/roman-i-sim-the-roman-image-simulator/roman-i-sim-tutorials)
- [Caveats of using Roman I-Sim](https://roman-docs.stsci.edu/simulation-tools-handbook-home/roman-i-sim-the-roman-image-simulator/caveats-of-using-roman-i-sim)


## 概要

Roman I-Sim は Roman 宇宙望遠鏡 Wide Field Instrument (WFI) 専用に設計された GalSim ベースの高精度画像シミュレーターである. 公式校正参照ファイル (CRDS) と STPSF 点広がり関数モデルを使用して, 現実的な Level-1 (L1) および Level-2 (L2) WFI データプロダクトを ASDF 形式で生成する.

## 主要機能

### シミュレーション能力
- **全検出器対応**: 18 個の SCA (Sensor Chip Assembly) すべてをサポート
- **全光学素子対応**: WFI 撮像フィルター全種 (F062-F213) での観測シミュレーション
- **データレベル**: Level-1 (未校正 ramp) および Level-2 (校正済み rate) 両方に対応
- **高精度レンダリング**: GalSim による精密シーン描画と光子レベル計算

### データ製品の特徴
- **ASDF フォーマット**: Roman データモデル準拠の公式データ形式
- **Multi-Accumulation**: Roman 固有の MA テーブル読み出しパターンを完全実装
- **現実的ノイズ**: 検出器効果とノイズ源を詳細に再現
- **パイプライン互換**: STScI Roman パイプラインでの後処理に対応

### 他ツールとの比較
| ツール | 視野サイズ | 精度 | 検出器数 | データ形式 | 用途 |
|--------|------------|------|----------|------------|------|
| Roman I-Sim | SCA サイズ | 最高 | 1-18 個 | ASDF (L1/L2) | 高精度データプロダクト |
| STIPS | 全視野 | 高 | 1-18 個 | FITS | 高速サーベイシミュレーション |
| Pandeia | 25" 四方 | 最高 | 単一 SCA | 辞書形式 | 露出時間計算 |

## インストールと設定

### Step 1: 専用 Conda 環境の作成
```bash
# Python 3.12 環境の作成 (推奨)
conda create -n romanisim python=3.12
conda activate romanisim
```

### Step 2: FFTW ライブラリの設定
GalSim が依存する FFTW ライブラリの設定:

```bash
# FFTW のダウンロードとインストール
# https://www.fftw.org/ からダウンロード

# 環境変数の設定
export FFTW_DIR='/path/to/fftw/install/directory'

# ~/.bashrc または ~/.bash_profile に追記
echo 'export FFTW_DIR="/path/to/fftw/install/directory"' >> ~/.bashrc
```

**重要**: FFTW_DIR が設定されていないと GalSim (および Roman I-Sim) は正常に動作しない.

### Step 3: Roman I-Sim のインストール
```bash
# PyPI からのインストール
pip install romanisim
```

### Step 4: STPSF と CRDS の設定
高精度シミュレーションのための必須設定:

#### STPSF の設定
```bash
# STPSF のインストール (STPSF for Roman 参照)
pip install stpsf

# 環境変数設定
export STPSF_PATH='/path/to/stpsf/data'
echo 'export STPSF_PATH="/path/to/stpsf/data"' >> ~/.bashrc
```

#### CRDS の設定
```bash
# CRDS のインストール
pip install crds

# Roman CRDS 環境変数設定
export CRDS_SERVER_URL='https://roman-crds.stsci.edu'
export CRDS_CONTEXT='roman_00XX.pmap'  # 最新のコンテキストを指定

# ~/.bashrc に追記
echo 'export CRDS_SERVER_URL="https://roman-crds.stsci.edu"' >> ~/.bashrc
echo 'export CRDS_CONTEXT="roman_00XX.pmap"' >> ~/.bashrc
```

**注意**: [Roman CRDS ウェブサイト](https://roman-crds.stsci.edu/) で最新のコンテキスト番号を確認すること.

### インストール確認
```bash
# Roman I-Sim のヘルプ表示
romanisim-make-image --help
romanisim-make-catalog --help

# Python での動作確認
python -c "import romanisim; print('Roman I-Sim installed successfully')"
```

## 基本的な使用方法

### 動作モード
Roman I-Sim は 2 つの方法で動作可能:

1. **コマンドライン実行**: `romanisim-make-image` 実行ファイル使用
2. **Python スクリプト**: 関数を直接呼び出し

## 実用的なチュートリアル

### チュートリアル 1: Gaia カタログからのソース生成
```python
import argparse
from astroquery.gaia import Gaia
from astropy.coordinates import SkyCoord
from astropy.time import Time
from astropy.table import vstack
import galsim
import numpy as np
from romanisim import gaia, bandpass, catalog, wcs, persistence, parameters, ris_make_utils as ris

# 観測パラメータ設定
ra = 80.0     # 赤経 (度)
dec = 30.0    # 赤緯 (度)
radius = 1.0  # 検索半径 (度)

# Gaia DR3 カタログクエリ
query = f'SELECT * FROM gaiadr3.gaia_source WHERE distance({ra}, {dec}, ra, dec) < {radius}'
job = Gaia.launch_job_async(query)
result = job.get_results()

# Roman I-Sim 形式への変換
obs_time = '2026-10-31T00:00:00'
gaia_catalog = gaia.gaia2romanisimcat(
    result,
    Time(obs_time),
    fluxfields=set(bandpass.galsim2roman_bandpass.values())
)

# 不正ソースの除去
bandpass_filters = [f for f in gaia_catalog.dtype.names if f[0] == 'F']
bad = np.zeros(len(gaia_catalog), dtype='bool')
for b in bandpass_filters:
    bad |= ~np.isfinite(gaia_catalog[b])
    if hasattr(gaia_catalog[b], 'mask'):
        bad |= gaia_catalog[b].mask

gaia_catalog = gaia_catalog[~bad]
gaia_catalog = gaia_catalog[np.isfinite(gaia_catalog['ra'])]

# ECSV 形式で保存
gaia_catalog.write('gaia_catalog.ecsv', overwrite=True)
```

#### コマンドライン版
```bash
# Gaia カタログ生成
romanisim-make-catalog 80.0 30.0 1.0 \
  --time 2026-10-31T00:00:00 \
  --output gaia_sources
```

### チュートリアル 2: パラメトリックカタログの生成
```python
from astropy.coordinates import SkyCoord
from astropy.table import vstack
from romanisim import catalog

# 銀河カタログパラメータ
ra = 80.0                   # 中心赤経 (度)
dec = 30.0                  # 中心赤緯 (度)
radius = 0.4                # カタログ半径 (度)
n_gal = 10_000              # 銀河数
n_star = 30_000             # 恒星数
faint_mag = 22              # 暗い限界等級
hlight_radius = 0.3         # 暗い限界での半光半径 (arcsec)
optical_element = ['F106']  # 光学素子
seed = 5346                 # ランダムシード

# 銀河カタログ生成
galaxy_cat = catalog.make_galaxies(
    SkyCoord(ra, dec, unit='deg'),
    n_gal,
    radius=radius,
    index=0.4,
    faintmag=faint_mag,
    hlr_at_faintmag=hlight_radius,
    bandpasses=optical_element,
    rng=None,
    seed=seed
)

# 恒星カタログ生成
star_cat = catalog.make_stars(
    SkyCoord(ra, dec, unit='deg'),
    n_star,
    radius=radius,
    index=5/3.,
    faintmag=faint_mag,
    truncation_radius=None,
    bandpasses=optical_element,
    rng=None,
    seed=seed
)

# カタログ統合と保存
full_catalog = vstack([galaxy_cat, star_cat])
full_catalog.write('parametric_catalog.ecsv', format='ascii.ecsv', overwrite=True)
```

### チュートリアル 3: 画像シミュレーション実行
```python
import argparse
import galsim
from astropy.coordinates import SkyCoord
from romanisim import wcs, persistence, parameters, ris_make_utils as ris

# シミュレーションパラメータ
ra = 80.0                         # 中心赤経 (度)
dec = 30.0                        # 中心赤緯 (度)
obs_date = '2026-10-31T00:00:00'  # 観測日時
sca = 1                           # SCA 番号 (1-18)
optical_element = 'F106'          # 光学素子
ma_table_number = 4               # MA テーブル番号 (3-17)
seed = 7                          # GalSim ランダムシード
level = 2                         # データレベル (1=L1, 2=L2)

# ファイル名設定
cal_level = 'cal' if level == 2 else 'uncal'
filename = f'r0003201001001001004_01101_0001_WFI{sca:02d}_{cal_level}.asdf'

# 引数設定
parser = argparse.ArgumentParser()
parser.set_defaults(
    usecrds=True,
    webbpsf=True,
    level=level,
    filename=filename,
    drop_extra_dq=True
)
args = parser.parse_args([])

# 参照ファイルを CRDS 用に None に設定
for k in parameters.reference_data:
    parameters.reference_data[k] = None

# GalSim RNG オブジェクト設定
rng = galsim.UniformDeviate(seed)

# デフォルト持続性情報設定
persist = persistence.Persistence()

# メタデータ設定
metadata = ris.set_metadata(
    date=obs_date,
    bandpass=optical_element,
    sca=sca,
    ma_table_number=ma_table_number
)

# WCS 情報更新
wcs.fill_in_parameters(
    metadata,
    SkyCoord(ra, dec, unit='deg', frame='icrs'),
    boresight=False,
    pa_aper=0.0
)

# シミュレーション実行
ris.simulate_image_file(args, metadata, full_catalog, rng, persist)
```

#### コマンドライン版
```bash
# L2 画像シミュレーション
romanisim-make-image \
  --bandpass F106 \
  --catalog parametric_catalog.ecsv \
  --date 2026-10-31T00:00:00 \
  --level 2 \
  --ma_table_number 4 \
  --radec 80.0 30.0 \
  --roll 0 \
  --sca 1 \
  --usecrds \
  --webbpsf \
  --rng_seed 7 \
  ./r0003201001001001004_01101_0001_WFI01_cal.asdf
```

### チュートリアル 4: シミュレーション結果の表示
```python
import roman_datamodels as rdm
import matplotlib.pyplot as plt
import matplotlib.colors as colors
from matplotlib import colormaps as cm
from astropy import visualization
from astropy import units as u

# ASDF ファイルを開く
filename = 'r0003201001001001004_01101_0001_WFI01_cal.asdf'
file = rdm.open(filename)
wfi = file.meta.instrument.detector

# カラーマップとノーマライゼーション設定
cmap = cm['RdYlBu_r']
norm = visualization.ImageNormalize(
    file.data,
    interval=visualization.ZScaleInterval(contrast=1),
    stretch=visualization.AsinhStretch(a=1)
)

# プロット作成
fig, ax = plt.subplots(figsize=(8, 6), subplot_kw={'projection': file.meta.wcs})

plot = ax.imshow(file.data, norm=norm, cmap=cmap, origin='lower')

# 座標グリッド設定
ax.coords.grid(linestyle='dashed', color='grey', alpha=0.5)

lon = ax.coords[0]
lat = ax.coords[1]

lon.set_axislabel('Right Ascension')
lat.set_axislabel('Declination')

lon.set_major_formatter('d.dd')
lat.set_major_formatter('d.dd')

lon.set_ticks(spacing=2 * u.arcminute)
lat.set_ticks(spacing=2 * u.arcminute)

plt.title(f"Roman I-Sim {wfi}")

# カラーバー追加
cax = plt.axes([0.825, 0.11, 0.025, 0.77])
plt.colorbar(mappable=plot, cax=cax)

file.close()
plt.show()
```

## 全フレーム画像の生成

### 18 SCA 全体のシミュレーション
```bash
# 全 SCA を一度に生成 (-1 指定)
romanisim-make-image \
  --bandpass F158 \
  --catalog galaxy_field_catalog.ecsv \
  --date 2026-10-31T00:00:00 \
  --level 2 \
  --ma_table_number 4 \
  --radec 80.0 30.0 \
  --roll 0 \
  --sca -1 \
  --usecrds \
  --webbpsf \
  --rng_seed 42 \
  ./full_frame_F158_cal

# または個別実行 (並列処理可能)
for sca in {1..18}; do
  romanisim-make-image \
    --bandpass F158 \
    --catalog galaxy_field_catalog.ecsv \
    --date 2026-10-31T00:00:00 \
    --level 2 \
    --ma_table_number 4 \
    --radec 80.0 30.0 \
    --roll 0 \
    --sca $sca \
    --usecrds \
    --webbpsf \
    --rng_seed 42 \
    ./r0003201001001001004_01101_0001_WFI$(printf "%02d" $sca)_cal.asdf &
done
wait
```

## 高度な機能

### Multi-Accumulation テーブル
Roman I-Sim は Roman 固有の MA テーブル (3-17) をサポート:

```python
# MA テーブル情報の取得
from romanisim import parameters

# 利用可能な MA テーブル一覧
print("Available MA tables:", list(parameters.ma_table_pars.keys()))

# 特定 MA テーブルの詳細
ma_table_4 = parameters.ma_table_pars[4]
print(f"MA Table 4: {ma_table_4['n_resultants']} resultants, "
      f"{ma_table_4['total_exposure_time']:.1f} sec")
```

### カスタム WCS 設定
```python
# カスタム座標系設定
from romanisim import wcs

# Boresight 指向での観測
metadata = ris.set_metadata(
    date='2026-10-31T00:00:00',
    bandpass='F146',
    sca=12,
    ma_table_number=6
)

wcs.fill_in_parameters(
    metadata,
    SkyCoord(270.0, -30.0, unit='deg', frame='icrs'),
    boresight=True,  # Boresight 指向
    pa_aper=45.0     # 45度回転
)
```

### 持続性効果の設定
```python
# 前露出からの持続性効果
from romanisim import persistence

# カスタム持続性設定
persist = persistence.Persistence()
persist.add_exposure(previous_image_data, exposure_time=100.0)

# シミュレーションで使用
ris.simulate_image_file(args, metadata, catalog, rng, persist)
```

## 制限事項と注意点

### 検出器効果の制限
- **未実装効果**: 1/f ノイズ, snowball アーティファクト
- **参照ピクセル**: L1 データの 4 ピクセル境界領域は未シミュレーション
- **ガイドウィンドウ**: 高速読み出し領域は現在未対応

### 明るいソースの制限
- **PSF サイズ**: 44×44 ピクセル (4.85" × 4.85") に制限
- **明るい星**: PSF の翼が切り取られる
- **回転角使用時**: R.A., Dec. とピクセル空間で二重に切り取り発生

### CRDS 使用時の注意点
- **未使用参照ファイル**: photom, IPC 参照ファイルは未対応
- **座標系問題**: `--usecrds` なしでは東西が逆転する問題

### パフォーマンス制限
- **大規模カタログ**: 数百万ソースの ASCII カタログは読み込みが非常に遅い
- **推奨フォーマット**: FITS バイナリテーブル使用を強く推奨
- **L1/L2 同時出力**: 同一実行では不可, 別々に実行が必要
