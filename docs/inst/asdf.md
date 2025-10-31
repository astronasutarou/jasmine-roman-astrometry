---
title: ASDF for Roman
description: Advanced Scientific Data Format for the Nancy Grace Roman Space Telescope
tags: [Roman, WFI, ASDF, datamodels, format]
---

# ASDF for Roman

このページの内容は主に以下のソースを引用・参考にしています:

- [Introduction to ASDF](https://roman-docs.stsci.edu/data-handbook-home/wfi-data-format/introduction-to-asdf)
- [ASDF standard](https://www.asdf-format.org/projects/asdf/en/latest/)
- [ASDF Python API](https://www.asdf-format.org/projects/asdf/en/latest/asdf/overview.html)
- [ASDF: A new data format for astronomy](https://www.sciencedirect.com/science/article/pii/S2213133715000645)
- [Roman datamodels](https://roman-datamodels.readthedocs.io/en/latest/)


Advanced Scientific Data Format (ASDF) は、Nancy Grace Roman Space Telescope の主要なデータフォーマットとして採用された次世代の天文データ形式です。ASDF は FITS の後継として設計され、階層構造のメタデータ、効率的なバイナリデータ圧縮、スキーマ検証などの先進的な機能を提供します。

## 概要

### ASDF とは

ASDF (Advanced Scientific Data Format; "AZ-diff" と発音) は、天文学データのアーカイブのための次世代交換フォーマットです。ASDF ファイルは人間が読み取り可能なテキストベースのメタデータとバイナリデータから構成されます。

### 主要な特徴

- **階層構造メタデータ**: YAML 形式による人間が読み取り可能な階層メタデータ
- **効率的なバイナリストレージ**: メモリマッピングと柔軟な圧縮サポート
- **スキーマ検証**: JSON Schema によるコンテンツ検証
- **Python オブジェクトサポート**: 基本的な Python データ型のネイティブサポートと拡張 API
- **バージョン管理**: ファイル形式とその機能の明示的なバージョン管理
- **ストリーミング**: ファイルストリーミングのネイティブサポート

### FITS からの進歩

ASDF は FITS の制限を克服するために設計されています：

- **属性名サイズ制限なし**: FITS キーワード名の制限を解除
- **値の型・サイズ制限なし**: 属性値の制限を撤廃
- **明示的階層関係**: 構文に階層関係を明示的に表現
- **バイナリデータ内蔵**: ファイル内にバイナリ形式でデータを格納
- **遅延読み込み**: 必要な部分のみメモリに読み込む効率的なリソース管理

## Roman での ASDF 採用

### Roman データパイプライン

Roman 望遠鏡のキャリブレーションパイプライン (`romancal`) は ASDF データ製品を専用に処理するよう設計されています。JWST、Daniel K. Inouye Solar Telescope (DKIST) とともに、STScI が活発に開発・保守を行っています。

### Roman Datamodels パッケージ

`roman_datamodels` パッケージは Roman WFI ASDF ファイルの読み書き機能を提供し、ドット記法によるファイル内容アクセスなどの便利な機能を追加します。

### スキーマ検証

Roman ASDF データ製品の構造と内容を定義・検証するスキーマは、Roman Attribute Dictionary (RAD) GitHub リポジトリにホストされています。

## インストールと設定

### ASDF Python パッケージ

```bash
# pip でインストール
pip install asdf

# conda でインストール
conda install -c conda-forge asdf
```

### Roman Datamodels パッケージ

```bash
pip install roman-datamodels
```

## 基本的な使用方法

### Roman Datamodels を使用したファイル読み込み

```python
import roman_datamodels as rdd

# ファイルを開く
file = rdd.open('r0000101001001001001_01101_0001_WFI01_cal.asdf')

# データ品質配列を読み込む
dq = file.dq

# メタデータを取得
meta = file.meta

# 露光開始・終了時刻を取得
start_time = meta.exposure.start_time
end_time = meta.exposure.end_time
print(start_time, end_time)

# 観測メタデータをすべて表示
meta.observation
```

### ASDF パッケージによるファイル読み込み

```python
import asdf

# ASDF ファイルを開く
file = asdf.open('r0000101001001001001_01101_0001_WFI01_cal.asdf')
roman_tree = file.tree['roman']

# 露光開始時刻を取得
start_time = roman_tree['meta']['exposure']['start_time']

# データ配列を読み込む
data = roman_tree['data']
print(data.shape)  # (4088, 4088)
```

## 高度な機能

### 圧縮サポート

ASDF は複数の無損失圧縮アルゴリズムをサポートします：

- **bzip2**: 高圧縮率
- **zlib**: バランス型圧縮
- **LZ4**: 高速圧縮

### メモリ効率

- **遅延読み込み**: ファイル全体をメモリに読み込まずに特定部分のみアクセス
- **メモリマッピング**: バイナリデータのメモリマッピングサポート
- **Zarr 統合**: チャンク化・圧縮された N 次元配列のサポート

### 高度なデータ型

- **Astropy オブジェクト**: 時刻オブジェクトなどのプログラミングオブジェクト
- **GWCS**: Generalized World Coordinate System による複雑な歪み補正モデル
- **カスタムオブジェクト**: 拡張可能なフォーマットによるユーザー定義型

## Roman での実装例

### 基本的なデータアクセス

```python
import roman_datamodels as rdd

# Roman キャリブレーション済みファイルを開く
with rdd.open('roman_calibrated_image.asdf') as model:
    # 科学データを取得
    science_data = model.data

    # データ品質フラグを確認
    dq_flags = model.dq

    # エラー配列を取得
    error_data = model.err

    # WCS 情報にアクセス
    wcs = model.meta.wcs

    # フィルター情報を取得
    filter_name = model.meta.instrument.optical_element
```

### メタデータ操作

```python
# 観測情報の詳細表示
obs_info = model.meta.observation
print(f"Program ID: {obs_info.program}")
print(f"Observation ID: {obs_info.obs_id}")
print(f"Visit ID: {obs_info.visit_id}")

# 露光パラメータ
exp_info = model.meta.exposure
print(f"Exposure time: {exp_info.duration}")
print(f"Start time: {exp_info.start_time}")
print(f"End time: {exp_info.end_time}")

# 装置設定
inst_info = model.meta.instrument
print(f"Instrument: {inst_info.name}")
print(f"Detector: {inst_info.detector}")
print(f"Filter: {inst_info.optical_element}")
```

## 制限事項と注意点

### パフォーマンス考慮事項

- **大容量ファイル**: 4 GB 以上のファイルでは 32 ビットシステムでメモリ制限の可能性
- **並列アクセス**: 複数プロセスからの同時読み書きには注意が必要
- **ネットワークストレージ**: NFS 等のネットワークファイルシステムでは性能低下の可能性

### 互換性

- **Python 実装**: 完全な実装は Python のみ
- **他言語サポート**: Java (asdf-java)、C (libasdf) は開発中
- **IDL サポート**: IDL 8.9 以降で部分サポート

### Roman 特有の制限

- **WFI 専用**: `roman_datamodels` は WFI スキーマベース
- **シミュレーションデータ**: Roman I-Sim 等の追加製品には直接の `asdf` パッケージ使用を推奨
- **スキーマ依存**: Roman スキーマが環境に必要

## 比較：ASDF vs FITS

| 特徴 | ASDF | FITS |
|------|------|------|
| メタデータ形式 | YAML (階層構造) | キーワード=値ペア |
| 属性名長制限 | なし | 8 文字 |
| 値サイズ制限 | なし | 70 文字 |
| バイナリ圧縮 | 内蔵サポート | 外部ツール必要 |
| スキーマ検証 | 自動検証 | 手動チェック |
| 階層データ | ネイティブサポート | 拡張必要 |
| Python 統合 | 深い統合 | 基本サポート |

## 関連ツールとの統合

### STPSF との連携

```python
# STPSF で生成された PSF を ASDF ファイルに保存
import roman_datamodels as rdd
from stpsf import roman

# PSF を計算
psf = roman.Roman()
psf_array = psf.calc_psf(filter='F158', detector='WFI01')

# ASDF ファイルとして保存
model = rdd.ImageModel()
model.data = psf_array
model.save('psf_data.asdf')
```

### STIPS との統合

```python
# STIPS シミュレーション結果を ASDF で管理
from stips.observation_module import ObservationModule

# 観測をシミュレーション
obs = ObservationModule()
obs.finalize(mosaic=True)

# 結果を ASDF ファイルとして保存
result_model = rdd.ImageModel()
result_model.data = obs.result
result_model.meta.observation.program = obs.program_id
result_model.save('stips_simulation.asdf')
```

## トラブルシューティング

### よくある問題

1. **スキーマ検証エラー**
   ```python
   # スキーマ検証を無効化
   with rdd.open('file.asdf', strict_validation=False) as model:
       data = model.data
   ```

2. **メモリ不足**
   ```python
   # 遅延読み込みを活用
   with rdd.open('large_file.asdf') as model:
       # 必要な部分のみアクセス
       subset = model.data[1000:2000, 1000:2000]
   ```

3. **バージョン非互換**
   ```python
   # ASDF バージョンを確認
   import asdf
   print(f"ASDF version: {asdf.__version__}")

   # ファイルのバージョンを確認
   with asdf.open('file.asdf') as af:
       print(f"File ASDF version: {af.version}")
   ```
