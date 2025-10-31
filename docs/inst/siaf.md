---
title: SIAF for Roman
description: Science Instrument Aperture File coordinate transformation tool for the Nancy Grace Roman Space Telescope
tags: [SIAF, coordinate, transformation, Roman, WFI, pysiaf, astrometry]
---

# SIAF for Roman

このページの内容は主に以下のソースを引用・参考にしています:

- [PySIAF for Roman](https://roman-docs.stsci.edu/simulation-tools-handbook-home/simulation-development-utilities/pysiaf-for-roman)
- [PySIAF on readthedocs](https://pysiaf.readthedocs.io/en/latest/)


## 概要

PySIAF は Roman 宇宙望遠鏡の Science Instrument Aperture File (SIAF) を読み込み, Wide Field Instrument (WFI) の検出器座標系, 宇宙船座標系, 天球座標系間の座標変換を実行する Python パッケージです. SIAF には WFI の幾何学的歪み補正モデルと検出器配置情報が含まれており, 精密なアストロメトリ解析に不可欠なツールです.

## 主要機能

### 座標変換能力
- **多座標系サポート**: Detector, Science, Ideal, Telescope (V-frame), Sky 座標系
- **幾何歪み補正**: SCA 毎の詳細な歪み補正モデル
- **双方向変換**: 任意の座標系間での相互変換
- **姿勢行列対応**: 宇宙船姿勢を考慮した天球座標変換

### SIAF 座標系の詳細
| 座標系 | 略記 | 単位 | 説明 |
|--------|------|------|------|
| Detector | det | pixel | 検出器読み出し電子回路ベースの座標系 |
| Science | sci | pixel | 焦点面統一配置での座標系 (4088×4088 有効画素) |
| Ideal | idl | arcsec | 幾何歪み補正済み座標系 (SCA 中心原点) |
| Telescope | tel | arcsec | 望遠鏡主鏡頂点基準座標系 (V-frame) |
| Sky | sky | degree | 天球座標系 (赤経・赤緯) |

### アパーチャ情報
- **WFI_CEN**: WFI 全体の幾何学的中心 (指向用)
- **WFI01_FULL ~ WFI18_FULL**: 各 SCA の詳細歪み補正用
- **CGI_CEN**: コロナグラフ指向用参照点

## インストールと設定

### 基本インストール
```bash
# Conda 環境での推奨インストール
conda create -n pysiaf_env python
conda activate pysiaf_env
pip install pysiaf
```

### インストール確認
```python
import pysiaf
print(f"PySIAF version: {pysiaf.__version__}")

# Roman SIAF の読み込み確認
rsiaf = pysiaf.Siaf('Roman')
print(f"Roman SIAF loaded: {len(rsiaf.apertures)} apertures")
```

## 基本的な使用方法

### Roman SIAF の読み込み
```python
import matplotlib.pyplot as plt
import pysiaf
from pysiaf.utils.rotations import attitude

# Roman SIAF を Python クラスに読み込み
rsiaf = pysiaf.Siaf('Roman')

# WFI01_FULL アパーチャの参照情報表示
wfi01 = rsiaf['WFI01_FULL']
print(f'WFI01 Xsci Ref: {wfi01.XSciRef}')     # 2044.5
print(f'WFI01 Ysci Ref: {wfi01.YSciRef}')     # 2044.5
print(f'WFI01 V2 Ref: {wfi01.V2Ref}')         # 1312.9491452484797
print(f'WFI01 V3 Ref: {wfi01.V3Ref}')         # -1040.7853726755036
```

### 座標系間の変換
```python
# WFI01 Science (1, 1) から他座標系への変換
det_xy = wfi01.sci_to_det(1, 1)    # Detector 座標
idl_xy = wfi01.sci_to_idl(1, 1)    # Ideal 座標
tel_xy = wfi01.sci_to_tel(1, 1)    # Telescope 座標

print('WFI01 Science (x, y) = (1, 1)')
print(f'Detector (x, y) = ({det_xy[0]:.1f}, {det_xy[1]:.1f}) pixels')
print(f'Ideal (x, y) = ({idl_xy[0]:.5f}, {idl_xy[1]:.5f}) arcseconds')
print(f'Telescope (x, y) = ({tel_xy[0]:.5f}, {tel_xy[1]:.5f}) arcseconds')

# 出力例:
# WFI01 Science (x, y) = (1, 1)
# Detector (x, y) = (5.0, 4092.0) pixels
# Ideal (x, y) = (-225.69567, -220.75499) arcseconds
# Telescope (x, y) = (1616.97641, -955.70469) arcseconds
```

## 実用的なチュートリアル

### チュートリアル 1: WFI 検出器配置の可視化
```python
# 全 WFI 検出器と CGI を V-frame で表示
roman_apertures = [f'WFI{i + 1:02}_FULL' for i in range(18)]
roman_apertures.append('CGI_CEN')

fig, ax = plt.subplots(figsize=(10, 8))

for rap in roman_apertures:
    color = 'salmon' if 'WFI' in rap else 'cyan'
    rsiaf[rap].plot(color=color, ax=ax)

# Boresight 基準線の追加
(V2, V3) = (0, 0)
ylim = ax.get_ylim()
xlim = ax.get_xlim()
ax.plot([0, 0], ylim, color='black', linestyle=':', alpha=0.3)
ax.plot(xlim, [0, 0], color='black', linestyle=':', alpha=0.3)

# V2 が左向き正となるよう X 軸を反転
ax.set_xlim(xlim)
ax.set_ylim(ylim)
ax.invert_xaxis()

ax.set_xlabel('V2 (arcsec)')
ax.set_ylabel('V3 (arcsec)')
ax.set_title('Roman WFI and CGI Apertures in Telescope Frame')
ax.grid(True, alpha=0.3)

plt.tight_layout()
plt.savefig('roman_apertures_v_frame.png', dpi=150)
plt.show()
```

### チュートリアル 2: 天球座標変換
```python
# 宇宙船姿勢の設定
boresight_ra = 93.485      # Boresight 赤経 (度)
boresight_dec = -10.22385  # Boresight 赤緯 (度)
pa_v3 = 172.935           # V3 軸位置角 (度, 北から反時計回り)

# 姿勢行列の生成
att = attitude(0, 0, boresight_ra, boresight_dec, pa_v3)

# WFI_CEN 参照位置の天球座標計算
wfi_cen = rsiaf['WFI_CEN']
wfi_cen.set_attitude_matrix(att)
wfi_ra, wfi_dec = wfi_cen.idl_to_sky(0, 0)
print(f'WFI_CEN RA = {wfi_ra:.5f} deg, Dec = {wfi_dec:.5f} deg')
# 出力例: WFI_CEN RA = 93.02112 deg, Dec = -10.03024 deg

# WFI18 の Science (1, 1) ピクセルの天球座標
wfi18 = rsiaf['WFI18_FULL']
wfi18.set_attitude_matrix(att)
wfi18_ra, wfi18_dec = wfi18.sci_to_sky(1, 1)
print(f'WFI18 (Xsci, Ysci) = (1, 1) → (RA, Dec) = ({wfi18_ra:.8f}, {wfi18_dec:.8f}) deg')
# 出力例: WFI18 (Xsci, Ysci) = (1, 1) → (RA, Dec) = (92.66926077, -10.31726044) deg
```

### チュートリアル 3: 逆変換 (天球 → ピクセル座標)
```python
# WFI 中心を特定天球位置に配置
target_ra = 80.0   # 目標赤経 (度)
target_dec = 30.0  # 目標赤緯 (度)
pa_v3 = 0.0       # V3 軸位置角 (度)

# WFI_CEN を目標位置に配置する姿勢行列
att = attitude(wfi_cen.V2Ref, wfi_cen.V3Ref, target_ra, target_dec, pa_v3)
wfi01.set_attitude_matrix(att)

# 特定天球座標での WFI01 ピクセル位置計算
source_ra = 79.91629
source_dec = 30.00603
x, y = wfi01.sky_to_sci(source_ra, source_dec)
print(f'Source (RA, Dec) = ({source_ra}, {source_dec}) → WFI01 (Xsci, Ysci) = ({x:.3f}, {y:.3f})')
# 出力例: Source (RA, Dec) = (79.91629, 30.00603) → WFI01 (Xsci, Ysci) = (835.452, 3049.258)
```

### チュートリアル 4: 複数 SCA での座標変換
```python
import numpy as np

# 複数 SCA での同一天球座標の変換
source_ra = 80.0
source_dec = 30.1

# 全 SCA に対する姿勢行列設定
sca_positions = []
for i in range(1, 19):
    sca_name = f'WFI{i:02}_FULL'
    sca = rsiaf[sca_name]
    sca.set_attitude_matrix(att)

    try:
        x, y = sca.sky_to_sci(source_ra, source_dec)
        # 有効画素範囲内チェック
        if 1 <= x <= 4088 and 1 <= y <= 4088:
            sca_positions.append({
                'sca': sca_name,
                'x': x,
                'y': y,
                'valid': True
            })
        else:
            sca_positions.append({
                'sca': sca_name,
                'x': x,
                'y': y,
                'valid': False
            })
    except:
        sca_positions.append({
            'sca': sca_name,
            'x': np.nan,
            'y': np.nan,
            'valid': False
        })

# 有効な SCA での位置表示
print(f"Source position (RA, Dec) = ({source_ra}, {source_dec})")
print("Valid SCA positions:")
for pos in sca_positions:
    if pos['valid']:
        print(f"  {pos['sca']}: (x, y) = ({pos['x']:.1f}, {pos['y']:.1f})")
```

## 高度な機能

### カスタムアパーチャの作成
```python
# カスタム領域での座標変換
def create_custom_region(sca_name, x_center, y_center, size=100):
    """指定 SCA の特定領域での座標変換マトリックス"""
    sca = rsiaf[sca_name]

    # 領域のピクセル座標グリッド
    x_range = np.arange(x_center - size//2, x_center + size//2)
    y_range = np.arange(y_center - size//2, y_center + size//2)

    # 天球座標への変換
    ra_grid = np.zeros((len(y_range), len(x_range)))
    dec_grid = np.zeros((len(y_range), len(x_range)))

    for i, y in enumerate(y_range):
        for j, x in enumerate(x_range):
            if 1 <= x <= 4088 and 1 <= y <= 4088:
                try:
                    ra, dec = sca.sci_to_sky(x, y)
                    ra_grid[i, j] = ra
                    dec_grid[i, j] = dec
                except:
                    ra_grid[i, j] = np.nan
                    dec_grid[i, j] = np.nan
            else:
                ra_grid[i, j] = np.nan
                dec_grid[i, j] = np.nan

    return x_range, y_range, ra_grid, dec_grid

# 使用例: WFI01 中心部の座標マッピング
att = attitude(wfi_cen.V2Ref, wfi_cen.V3Ref, 80.0, 30.0, 0.0)
rsiaf['WFI01_FULL'].set_attitude_matrix(att)

x_range, y_range, ra_grid, dec_grid = create_custom_region('WFI01_FULL', 2044, 2044, 200)

# 可視化
fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(12, 5))

im1 = ax1.imshow(ra_grid, extent=[x_range[0], x_range[-1], y_range[0], y_range[-1]], origin='lower')
ax1.set_title('RA (degrees)')
ax1.set_xlabel('Science X (pixels)')
ax1.set_ylabel('Science Y (pixels)')
plt.colorbar(im1, ax=ax1)

im2 = ax2.imshow(dec_grid, extent=[x_range[0], x_range[-1], y_range[0], y_range[-1]], origin='lower')
ax2.set_title('Dec (degrees)')
ax2.set_xlabel('Science X (pixels)')
ax2.set_ylabel('Science Y (pixels)')
plt.colorbar(im2, ax=ax2)

plt.tight_layout()
plt.show()
```

### 歪み補正の可視化
```python
def plot_distortion_map(sca_name, grid_size=50):
    """SCA の幾何歪みマップを表示"""
    sca = rsiaf[sca_name]

    # Science 座標でのグリッド生成
    x_sci = np.linspace(1, 4088, grid_size)
    y_sci = np.linspace(1, 4088, grid_size)
    X_sci, Y_sci = np.meshgrid(x_sci, y_sci)

    # Ideal 座標への変換
    X_idl = np.zeros_like(X_sci)
    Y_idl = np.zeros_like(Y_sci)

    for i in range(grid_size):
        for j in range(grid_size):
            x_idl, y_idl = sca.sci_to_idl(X_sci[i, j], Y_sci[i, j])
            X_idl[i, j] = x_idl
            Y_idl[i, j] = y_idl

    # 歪み量計算 (理想格子からのずれ)
    x_idl_ideal = (X_sci - 2044.5) * 0.11  # 0.11 arcsec/pixel
    y_idl_ideal = (Y_sci - 2044.5) * 0.11

    distortion_x = X_idl - x_idl_ideal
    distortion_y = Y_idl - y_idl_ideal
    distortion_mag = np.sqrt(distortion_x**2 + distortion_y**2)

    # 可視化
    fig, ((ax1, ax2), (ax3, ax4)) = plt.subplots(2, 2, figsize=(12, 10))

    # X 方向歪み
    im1 = ax1.imshow(distortion_x, extent=[1, 4088, 1, 4088], origin='lower', cmap='RdBu_r')
    ax1.set_title(f'{sca_name} X Distortion (arcsec)')
    ax1.set_xlabel('Science X (pixels)')
    ax1.set_ylabel('Science Y (pixels)')
    plt.colorbar(im1, ax=ax1)

    # Y 方向歪み
    im2 = ax2.imshow(distortion_y, extent=[1, 4088, 1, 4088], origin='lower', cmap='RdBu_r')
    ax2.set_title(f'{sca_name} Y Distortion (arcsec)')
    ax2.set_xlabel('Science X (pixels)')
    ax2.set_ylabel('Science Y (pixels)')
    plt.colorbar(im2, ax=ax2)

    # 歪み大きさ
    im3 = ax3.imshow(distortion_mag, extent=[1, 4088, 1, 4088], origin='lower', cmap='viridis')
    ax3.set_title(f'{sca_name} Distortion Magnitude (arcsec)')
    ax3.set_xlabel('Science X (pixels)')
    ax3.set_ylabel('Science Y (pixels)')
    plt.colorbar(im3, ax=ax3)

    # ベクトル場
    step = grid_size // 10
    ax4.quiver(X_sci[::step, ::step], Y_sci[::step, ::step],
               distortion_x[::step, ::step], distortion_y[::step, ::step],
               scale=1, scale_units='xy', angles='xy')
    ax4.set_title(f'{sca_name} Distortion Vectors')
    ax4.set_xlabel('Science X (pixels)')
    ax4.set_ylabel('Science Y (pixels)')
    ax4.set_aspect('equal')

    plt.tight_layout()
    plt.show()

    return distortion_mag.max()

# 使用例
max_distortion = plot_distortion_map('WFI01_FULL')
print(f"Maximum distortion: {max_distortion:.3f} arcsec")
```

## 座標系の詳細仕様

### Detector 座標系
- **原点**: 高速・低速読み出し電子回路の交点
- **方向**: X 軸は高速スキャナ方向, Y 軸は低速スキャナ方向
- **単位**: ピクセル (1-indexed)
- **範囲**: (1, 1) から (4096, 4096)
- **特徴**: WFI03, 06, 09, 12, 15, 18 は他 SCA と異なる向き

### Science 座標系
- **原点**: 焦点面統一配置での左下角 (WFI16 が左上, WFI07 が右上の向き)
- **方向**: X 軸右向き, Y 軸上向き
- **単位**: ピクセル (1-indexed)
- **範囲**: (1, 1) から (4088, 4088) - 有効画素のみ
- **用途**: 科学観測データの標準座標系

### Ideal 座標系
- **原点**: SCA 中心 (2044.5, 2044.5)
- **方向**: Y 軸は Science 座標系と平行, X 軸は歪み補正により若干非平行
- **単位**: 秒角 (arcsec)
- **用途**: 幾何歪み補正済み座標での計算

### Telescope (V-frame) 座標系
- **原点**: 望遠鏡 Boresight
- **方向**: V1 軸は主鏡法線方向, V2 軸は右手系完成方向, V3 軸はサンシェード法線
- **単位**: 秒角 (arcsec)
- **特徴**: V2 軸は投影図で左向きが正 (右手系のため)

### Sky 座標系
- **原点**: 天球上の任意点
- **方向**: 赤経・赤緯による球面座標
- **単位**: 度 (degree)
- **必要情報**: 宇宙船姿勢行列 (Boresight 位置 + V3 軸位置角)

## アプリケーション例

### 星図作成ツール
```python
def create_star_chart(ra_center, dec_center, pa_v3, magnitude_limit=20):
    """指定領域の星図作成"""
    from astroquery.vizier import Vizier
    from astropy.coordinates import SkyCoord
    import astropy.units as u

    # Gaia カタログクエリ
    search_radius = 1.0 * u.deg  # WFI 視野より大きめに設定
    coord = SkyCoord(ra=ra_center*u.deg, dec=dec_center*u.deg)

    v = Vizier(columns=['RAJ2000', 'DEJ2000', 'Gmag'],
               column_filters={'Gmag': f'<{magnitude_limit}'})
    result = v.query_region(coord, radius=search_radius, catalog='I/355/gaiadr3')

    if len(result) == 0:
        print("No stars found in the region")
        return

    stars = result[0]

    # WFI 姿勢設定
    att = attitude(wfi_cen.V2Ref, wfi_cen.V3Ref, ra_center, dec_center, pa_v3)

    # 各 SCA での星の位置計算
    fig, ax = plt.subplots(figsize=(12, 8))

    for i in range(1, 19):
        sca_name = f'WFI{i:02}_FULL'
        sca = rsiaf[sca_name]
        sca.set_attitude_matrix(att)

        # SCA 境界描画
        corners_x = [1, 4088, 4088, 1, 1]
        corners_y = [1, 1, 4088, 4088, 1]
        corners_ra = []
        corners_dec = []

        for x, y in zip(corners_x, corners_y):
            ra, dec = sca.sci_to_sky(x, y)
            corners_ra.append(ra)
            corners_dec.append(dec)

        ax.plot(corners_ra, corners_dec, 'k-', alpha=0.5)
        ax.text(np.mean(corners_ra), np.mean(corners_dec), f'WFI{i:02}',
                ha='center', va='center', fontsize=8)

        # SCA 内の星を表示
        for star in stars:
            star_ra = star['RAJ2000']
            star_dec = star['DEJ2000']
            star_mag = star['Gmag']

            try:
                x, y = sca.sky_to_sci(star_ra, star_dec)
                if 1 <= x <= 4088 and 1 <= y <= 4088:
                    # 等級に応じたサイズ
                    size = max(1, 15 - star_mag)
                    ax.scatter(star_ra, star_dec, s=size, c='red', alpha=0.7)
            except:
                continue

    ax.set_xlabel('RA (degrees)')
    ax.set_ylabel('Dec (degrees)')
    ax.set_title(f'Roman WFI Star Chart (RA={ra_center}, Dec={dec_center}, PA={pa_v3})')
    ax.grid(True, alpha=0.3)
    ax.invert_xaxis()  # RA は右から左へ増加
    plt.tight_layout()
    plt.show()

# 使用例
create_star_chart(270.0, -30.0, 45.0, magnitude_limit=18)
```

### 精密測位解析
```python
def astrometric_precision_analysis(sca_name, n_samples=1000):
    """SCA 内での測位精度解析"""
    sca = rsiaf[sca_name]

    # ランダムなピクセル位置での変換精度テスト
    np.random.seed(42)
    x_samples = np.random.uniform(100, 3988, n_samples)  # 端部を避ける
    y_samples = np.random.uniform(100, 3988, n_samples)

    # 往復変換での誤差評価
    x_errors = []
    y_errors = []

    for x_orig, y_orig in zip(x_samples, y_samples):
        # sci → idl → sci の往復変換
        x_idl, y_idl = sca.sci_to_idl(x_orig, y_orig)
        x_back, y_back = sca.idl_to_sci(x_idl, y_idl)

        x_errors.append(abs(x_back - x_orig))
        y_errors.append(abs(y_back - y_orig))

    x_errors = np.array(x_errors)
    y_errors = np.array(y_errors)
    total_errors = np.sqrt(x_errors**2 + y_errors**2)

    # 統計情報
    print(f"=== {sca_name} Astrometric Precision Analysis ===")
    print(f"Samples: {n_samples}")
    print(f"X error (pixels): mean={np.mean(x_errors):.6f}, std={np.std(x_errors):.6f}, max={np.max(x_errors):.6f}")
    print(f"Y error (pixels): mean={np.mean(y_errors):.6f}, std={np.std(y_errors):.6f}, max={np.max(y_errors):.6f}")
    print(f"Total error (pixels): mean={np.mean(total_errors):.6f}, std={np.std(total_errors):.6f}, max={np.max(total_errors):.6f}")
    print(f"Total error (mas): mean={np.mean(total_errors)*110:.3f}, max={np.max(total_errors)*110:.3f}")

    # 誤差分布の可視化
    fig, ((ax1, ax2), (ax3, ax4)) = plt.subplots(2, 2, figsize=(12, 10))

    ax1.hist(x_errors * 110, bins=50, alpha=0.7, edgecolor='black')
    ax1.set_xlabel('X Error (mas)')
    ax1.set_ylabel('Frequency')
    ax1.set_title('X Position Error Distribution')

    ax2.hist(y_errors * 110, bins=50, alpha=0.7, edgecolor='black')
    ax2.set_xlabel('Y Error (mas)')
    ax2.set_ylabel('Frequency')
    ax2.set_title('Y Position Error Distribution')

    ax3.hist(total_errors * 110, bins=50, alpha=0.7, edgecolor='black')
    ax3.set_xlabel('Total Error (mas)')
    ax3.set_ylabel('Frequency')
    ax3.set_title('Total Position Error Distribution')

    ax4.scatter(x_samples, y_samples, c=total_errors*110, cmap='viridis', s=1)
    ax4.set_xlabel('Science X (pixels)')
    ax4.set_ylabel('Science Y (pixels)')
    ax4.set_title('Spatial Distribution of Errors')
    cbar = plt.colorbar(ax4.collections[0], ax=ax4)
    cbar.set_label('Total Error (mas)')

    plt.tight_layout()
    plt.show()

    return np.mean(total_errors), np.max(total_errors)

# 全 SCA での精度解析
print("Roman WFI Astrometric Precision Analysis")
print("=" * 50)
mean_errors = []
max_errors = []

for i in range(1, 19):
    sca_name = f'WFI{i:02}_FULL'
    mean_err, max_err = astrometric_precision_analysis(sca_name, n_samples=1000)
    mean_errors.append(mean_err)
    max_errors.append(max_err)
    print()

print("Summary:")
print(f"Overall mean error: {np.mean(mean_errors)*110:.3f} mas")
print(f"Overall max error: {np.max(max_errors)*110:.3f} mas")
```

## 制限事項と注意点

### 座標変換の注意点
- **SCA 選択**: 天球→ピクセル変換時は正しい SCA を選択する必要
- **歪み補正**: 各 SCA 固有の歪み補正モデルを使用すること
- **座標系統一**: Detector と Science 座標系の向きの違いに注意
- **境界処理**: 有効画素範囲外での変換結果は信頼性が低い

### 精度制限
- **浮動小数点誤差**: 往復変換での微小誤差 (< 0.001 pixel)
- **歪み補正モデル**: 高次多項式近似による制限
- **姿勢行列**: 宇宙船姿勢の不確定性による影響
- **更新頻度**: SIAF は定期的に更新されるため最新版の使用が重要

### 計算コスト
- **大量座標変換**: 数万点以上の変換では処理時間に注意
- **反復計算**: sky_to_sci 変換は反復解法のため sci_to_sky より遅い
- **メモリ使用量**: 大規模グリッド計算時のメモリ消費

## バージョン管理とアップデート

### 最新版の確認
```python
import pysiaf

# 現在のバージョン確認
print(f"PySIAF version: {pysiaf.__version__}")

# SIAF データの更新日確認
rsiaf = pysiaf.Siaf('Roman')
print(f"Roman SIAF last updated: {rsiaf.creation_date}")

# アパーチャ一覧の確認
print(f"Available apertures: {len(rsiaf.apertures)}")
for aperture_name in sorted(rsiaf.apertures.keys())[:10]:  # 最初の10個表示
    print(f"  {aperture_name}")
```

### アップデート手順
```bash
# PySIAF の更新
pip install --upgrade pysiaf

# 開発版のインストール (必要に応じて)
pip install git+https://github.com/spacetelescope/pysiaf.git
```

## リソースとサポート

### ドキュメント
- **ReadTheDocs**: [pysiaf.readthedocs.io](https://pysiaf.readthedocs.io/en/latest/)
- **GitHub リポジトリ**: [github.com/spacetelescope/pysiaf](https://github.com/spacetelescope/pysiaf)
- **API ドキュメント**: 全関数・クラスの詳細仕様

### 関連ツール
- **Roman I-Sim**: PySIAF を使用した座標変換
- **JWST Pipeline**: 類似の座標変換システム
- **APT (Astronomer's Proposal Tool)**: 観測計画での座標設定

### サポート
- **Roman Help Desk**: [stsci.service-now.com/roman](https://stsci.service-now.com/roman)
- **GitHub Issues**: バグ報告・機能要望
- **STScI Roman Pages**: [stsci.edu/roman](https://www.stsci.edu/roman)

### 参考文献
- Sahlmann, J., et al. 2019, "PySIAF: A Python Package for SIAF Files", DOI: [10.5281/zenodo.3516964](https://zenodo.org/record/3516964)

---

**更新情報**: PySIAF 最新版および Roman SIAF データに基づく (2025年10月現在)
