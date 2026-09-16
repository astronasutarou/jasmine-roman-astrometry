---
title: WFI データ品質 (DQ) フラグ
description: Roman WFI データ製品の Data Quality 配列と解析時の扱い
tags: [Roman, WFI, data quality, DQ, romancal, CRDS, calibration]
---

# WFI データ品質 (DQ) フラグ

このページの内容は主に以下のソースを引用・参考にしている:

- [Data Quality Information](https://roman-docs.stsci.edu/data-handbook/wfi-data-levels-and-products/data-quality-information) (STScI, Publication: 2026-07-16)
- [Roman data quality flag definitions](https://github.com/spacetelescope/roman_datamodels/blob/main/src/roman_datamodels/dqflags.py) (`roman_datamodels`, STScI)

## 概要

Data Quality (DQ) フラグは, 画素の既知の異常特性や個々の露光で検出された事象をビットで表す. 複数の条件がある画素では, 各フラグをビット単位 OR で結合した整数値が DQ 配列に格納される.

DQ 値が 0 でないという理由だけで, 画素を一律に除外してはならない. フラグの意味と科学目的を照合して判断する. 一方, `DO_NOT_USE` (bit 0, 値 1) を含む画素は, 科学解析から除外することが公式文書で推奨されている. ほかのフラグと同時に立っていても, bit 0 の有無を検査する必要がある.

## DQ フラグの由来

### 校正参照ファイル

Bad Pixel Mask (BPM) 参照ファイルは, 装置特性評価で既知となった画素挙動を記録する. `romancal.dq_init.DQInitStep` が BPM の情報をデータモデルの DQ 配列へコピーし, 後続ステップがそこへフラグを追加する.

BPM には, dead, low quantum efficiency (low QE), resistor-capacitor (RC)/inverse RC, telegraph, open/adjacent-to-open などの画素クラスが含まれる. dark current や non-linearity に由来する情報は, 対応する別の参照ファイルから加わる.

参照ファイルは Roman Calibration Reference Data System (CRDS) で管理される. commissioning と運用で軌道上データが蓄積されるにつれ, BPM や `DO_NOT_USE` の対象クラスは更新される見込みである.

### 校正パイプライン

`romancal` の各処理ステップも, 露光ごとの状態を既存の DQ 値へビット単位 OR で追加する. 代表例は次のとおり.

| 条件 | フラグ | 主な処理 |
|---|---|---|
| 飽和 | `SATURATED` | saturation |
| A/D floor 未満 | `AD_FLOOR` | saturation |
| cosmic ray などの jump | `JUMP_DET` | ramp fitting / jump detection |
| guide window 読み出しの影響 | `GW_AFFECTED_DATA` | DQ initialization |
| 外れ値 | `OUTLIER` | higher-level processing |

`GW_AFFECTED_DATA`, `AD_FLOOR`, `OUTLIER` などは `DO_NOT_USE` と同時に設定される場合がある. この対象は軌道上での知見に応じて変わり得る.

## データ製品ごとの格納場所

### Level 2

Level 2 (L2) の校正済み露光には, science data と uncertainty arrays と同じ画素寸法の DQ 配列がある. ここには参照ファイル由来の既知の画素特性と, `romancal` が露光ごとに検出した条件が統合されている.

### Level 3 mosaic

Level 3 (L3) mosaic は複数の L2 画像を結合するため, 既定では DQ 配列を持たない. `DO_NOT_USE` を含む入力画素は co-add から除外される. どの L2 画像が出力位置へ寄与したかは `context` 配列で確認し, 必要に応じて元の L2 DQ 配列を調べる.

Mosaic Pipeline を手動実行する場合は `propagate_dq=True` により, 入力 L2 の DQ 情報を出力 mosaic と同じ寸法の追加 DQ 配列へ伝播できる. 解析では, 使用した `romancal` バージョンとパラメータも記録する.

Level 4 (L4) catalog の品質フラグについては, RDox で詳細が今後追加される予定である.

## 解析でのビット判定

`DO_NOT_USE` を含む画素を判定する例:

```python
from romancal.lib import dqflags

do_not_use = dqflags.pixel["DO_NOT_USE"]
bad_mask = (dq & do_not_use) != 0
good_mask = (dq & do_not_use) == 0
```

特定フラグについても同じ方法で判定できる.

```python
jump_mask = (dq & dqflags.pixel["JUMP_DET"]) != 0
saturated_mask = (dq & dqflags.pixel["SATURATED"]) != 0
```

フラグの名称と値はソフトウェア開発に伴い変化し得るため, 整数値を解析コードへ直接埋め込まず, 使用中の `roman_datamodels` が提供する定義を参照する. 結果の再現性のため, データ製品の pipeline/CRDS context とローカル環境のパッケージバージョンを記録することが望ましい.

## 注意事項

- 本ページは WFI imaging の STScI Science Operations Center (SOC) 製品を中心に扱う.
- WFI spectroscopy および microlensing の高次製品は Science Support Center (SSC) の文書を参照する.
- commissioning と初期運用では, BPM, DQ 定義, pipeline behavior が更新される可能性がある. 解析時点の RDox, `romancal`, `roman_datamodels`, CRDS context を確認する.
