---
title: Roman CRDS と WFI 校正参照ファイル
description: Roman WFI の校正参照ファイル選択と BAM context 更新の確認方法
tags: [Roman, WFI, CRDS, calibration, reference file, BAM, astrometry, romancal]
---

# Roman CRDS と WFI 校正参照ファイル

このページの内容は主に以下のソースを引用・参考にしている:

- [CRDS for Reference Files](https://roman-docs.stsci.edu/data-handbook-home/accessing-wfi-data/crds-for-reference-files) (STScI, Publication: 2024-01-05, Latest Update: 2024-12-20)
- [Roman CRDS](https://roman-crds.stsci.edu/) (STScI, 2026-09-22 閲覧)
- [`roman_0067.pmap`](https://roman-crds.stsci.edu/context_table/roman_0067.pmap) (STScI, Activation: 2026-09-21)
- [`roman_wfi_bam_0005.rmap`](https://roman-crds.stsci.edu/browse/roman_wfi_bam_0005.rmap) (STScI, Delivery/Activation: 2026-09-21)
- [`romancal` DMS Operational Build Versions](https://github.com/spacetelescope/romancal#dms-operational-build-versions) (STScI)

## 概要

Calibration Reference Data System (CRDS) は, Roman WFI Data Pipelines が使用する校正参照ファイルと parameter file を管理する. exposure-level pipeline は, Level 1 (L1) の未校正データから Level 2 (L2) の校正済み rate image を生成する際に CRDS を利用する. Roman I-Sim などの simulation tool も一部の参照ファイルを利用する.

CRDS context は pipeline mapping (PMAP) ファイルで表され, 各参照ファイル種別に使用する reference mapping (RMAP) を指定する. RMAP は観測メタデータを参照ファイルへ対応付ける. `USEAFTER` は参照ファイルの適用開始時刻であり, CRDS は観測時刻以前で最も近い `USEAFTER` と, 参照ファイル種別ごとの追加条件を用いてファイルを選択する.

処理に使われた context 名はデータ製品の ASDF metadata に記録される. L2 ASDF では, 使用した各参照ファイル名も metadata の dictionary と処理 log から確認できる. 再現性を保つには, `romancal` version だけでなく context 名と参照ファイル名を記録する.

## ローカル環境の設定

`romancal` から Roman CRDS へ接続する基本設定は次のとおり.

```bash
export CRDS_SERVER_URL=https://roman-crds.stsci.edu
export CRDS_PATH=$HOME/data/crds_cache/
```

特定の context で再処理する場合は `CRDS_CONTEXT` を明示する.

```bash
export CRDS_CONTEXT=roman_0067.pmap
```

`latest` context は更新され得る. 既存結果を再現する場合は, 処理時点の具体的な PMAP 名を指定し, pipeline release に対応する検証済み context も確認する. 公式 `romancal` release table では, release 1.0.2 (26Q3_B22.3) に `roman_0065.pmap` が対応付けられている. release と対応 context の組は, CRDS server 上の `latest` context と必ずしも同一ではない.

## 2026 年 9 月の BAM 更新

Roman CRDS では 2026-09-16 から 2026-09-21 にかけて, WFI Boresight Alignment Matrix (BAM) が軌道上 commissioning データに基づいて更新された.

| Context | Activation | `2026-09-01` 以降に選択される BAM | 公式説明 |
|---|---|---|---|
| `roman_0065.pmap` | 2026-09-16 | `roman_wfi_bam_0002.asdf` | commissioning で測定した軌道上値へ alignment matrix を更新 |
| `roman_0066.pmap` | 2026-09-19 | `roman_wfi_bam_0003.asdf` | 2026-09-18 の CAR-86.4 恒星観測から導出し, CAR-86.1 に基づく直前の BAM から小さく更新 |
| `roman_0067.pmap` | 2026-09-21 | `roman_wfi_bam_0004.asdf` | 2026-09-18 から 2026-09-20 の CAR-86.4 および CAR-86.7 恒星観測から導出し, 直前の BAM から小さく更新 |

2026-09-22 時点では `roman_0067.pmap` が `latest` であり, BAM の RMAP は `roman_wfi_bam_0005.rmap` である. この RMAP の `USEAFTER` は `2026-09-01 00:00:00` で, それ以降の exposure に `roman_wfi_bam_0004.asdf` を選択する. CRDS はこれらの BAM 更新の change level を `SEVERE` としている.

BAM は WFI と observatory boresight の相対 alignment を表す. CRDS では BAM と detector distortion が別の参照ファイル種別として管理されているため, BAM 更新を detector distortion model の更新と同一視してはならない. また, 公式 delivery note は astrometric shift の大きさを数値で示していない. 解析への影響は, 使用した context と BAM を特定したうえで評価する必要がある.

## 解析時の確認事項

- archive product では ASDF metadata と処理 log から CRDS context と参照ファイル名を確認する.
- ローカル再処理では `romancal` version, `CRDS_CONTEXT`, parameter file, 実行時刻を記録する.
- 異なる context の結果を比較する場合は, PMAP 全体の差分を確認し, BAM だけが異なると仮定しない.
- `latest` の変更後も, 過去の data release や operational build は指定された検証済み context を用いる場合がある.
- BAM 更新の astrometry への影響を報告する場合は, CRDS の説明と実データで測定した結果を区別する.
