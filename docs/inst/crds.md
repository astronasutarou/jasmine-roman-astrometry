---
title: Roman CRDS と WFI 校正参照ファイル
description: Roman WFI の現行 CRDS context, 校正参照ファイル, 解析時の確認事項
tags: [Roman, WFI, CRDS, calibration, reference file, spectroscopy, BAM, distortion, astrometry, romancal]
---

# Roman CRDS と WFI 校正参照ファイル

このページの内容は主に以下のソースを引用・参考にしている:

- [CRDS for Reference Files](https://roman-docs.stsci.edu/data-handbook-home/accessing-wfi-data/crds-for-reference-files) (STScI, Publication: 2024-01-05, Latest Update: 2024-12-20)
- [`romancal` DMS Operational Build Versions](https://github.com/spacetelescope/romancal#dms-operational-build-versions) (STScI)

## 概要

Calibration Reference Data System (CRDS) は, Roman WFI Data Pipelines が使用する校正参照ファイルと parameter file を管理する. exposure-level pipeline は, Level 1 (L1) の未校正データから Level 2 (L2) の校正済み rate image を生成する際に CRDS を利用する. Roman I-Sim などの simulation tool も一部の参照ファイルを利用する.

CRDS context は pipeline mapping (PMAP) ファイルで表され, 各参照ファイル種別に使用する reference mapping (RMAP) を指定する. RMAP は観測メタデータを参照ファイルへ対応付ける. `USEAFTER` は参照ファイルの適用開始時刻であり, CRDS は観測時刻以前で最も近い `USEAFTER` と, 参照ファイル種別ごとの追加条件を用いてファイルを選択する.

処理に使われた context 名はデータ製品の ASDF metadata に記録される. L2 ASDF では, 使用した各参照ファイル名も metadata の dictionary と処理 log から確認できる. 再現性を保つには, `romancal` version だけでなく context 名と参照ファイル名を記録する.

## 現行の校正状況

2026-10-10 時点の `latest` context は, 2026-10-08 に delivery および activation された `roman_0075.pmap` である. この context は, `roman_0074.pmap` までの imaging, detector, spectral calibration files と Boresight Alignment Matrix (BAM) を引き継ぎ, detector distortion と pixel area map を更新している.

### Imaging および detector calibration

現行 context が引き継ぐ主な参照ファイルは次のとおりである.

| 種別 | ファイル数・適用範囲 | 公式に示された由来 |
|---|---|---|
| flat | 144 files: imaging の 18 detectors × 8 filters | TVAC および SCIPA ground testing |
| saturation, mask, linearity, inverse linearity, photom | 各 18 files: imaging および spectral modes | TVAC および SCIPA ground testing |
| pixel area, distortion | 各 18 files: imaging および spectral modes | CAR-86.11 の軌道上観測に基づく SIAF `PRDOPSRSOC-012` |

flat, saturation, mask, linearity, inverse linearity, photom は `2026-09-01 00:00:00` を `USEAFTER` とする detector 別ファイルを引き続き選択する. flat は imaging filter ごとに選択され, `GRISM`, `PRISM`, `DARK` には適用されない.

### 軌道上 SIAF に基づく distortion と pixel area

`roman_0075.pmap` の `roman_wfi_distortion_0004.rmap` と `roman_wfi_area_0004.rmap` は, 18 detectors すべてについて `2026-10-04 01:28:00` を `USEAFTER` とする新しい参照ファイルを選択する. 両 RMAP の change level は `SEVERE` である.

参照ファイルは, 最初の Payload Focus and Alignment Campaign (PFAC 1) 後に実施された commissioning activity CAR-86.11 の軌道上観測に基づく Science Instrument Aperture File (SIAF) `PRDOPSRSOC-012` を用いて作成された. CRDS の delivery note は pixel scale の変更に加え, WFI-CEN aperture location に約 23 pixels の shift が見つかったと記録している.

この約 23 pixels は WFI-CEN aperture の参照位置の shift であり, 個々の天体の astrometric residual や sky coordinates 上の一様な shift を直接示す値ではない. CRDS は detector ごとの係数差や天球上の residual を公開していない. distortion は detector 座標から sky coordinates への WCS model に, pixel area map は歪みのある detector frame での測光補正に関係するため, context をまたいで位置・測光を比較する場合は実データで影響を評価する.

### Spectral calibration

`roman_0073.pmap` は, grism と prism について次の 5 種別を各 1 file, 合計 10 files の初期参照ファイルとして選択する.

| 種別 | 内容 | 現在の状態・由来 |
|---|---|---|
| `optmodel` | direct image と dispersed image の対応, trace, wavelength solution を含む optical model | TVAC ground testing で更新された WFI as-built design model から導出 |
| `absflux` | absolute flux calibration | TVAC ground testing で更新された WFI as-built design model から導出 |
| `specpsf` | spectral point-spread function (PSF) | TVAC ground testing で更新された WFI as-built design model から導出 |
| `relflux` | relative flux calibration | unity を設定した dummy file |
| `sflat` | pixel-level small-scale flat-field | unity を設定した dummy file |

各 RMAP は `GRISM` と `PRISM` を分け, `2020-01-01 00:00:00` を `USEAFTER` として対応するファイルを選択する. change level は `SEVERE` である.

`relflux` と `sflat` は in-flight commissioning data による更新が予定されている. したがって, 現在の処理は波長依存の相対感度や画素ごとの小スケール分光 flat を実測値で補正したものではない. 分光データの定量解析では, dummy file の制約を明示する必要がある. `optmodel`, `absflux`, `specpsf` も ground model に基づく初期値であり, commissioning 後に更新される可能性がある.

### Boresight alignment

現行 context の `roman_wfi_bam_0006.rmap` は, `2026-10-04 01:28:00` 以降の exposure に `roman_wfi_bam_0005.asdf` を選択する. この BAM は Roman observatory boresight と Fine Guidance System (FGS) の alignment matrix の quaternion を, commissioning で得た軌道上値に更新したものであり, 最初の Payload Focus and Alignment Campaign (PFAC) 後の観測に適用される. RMAP の change level は `SEVERE` である.

それ以前については, `2026-09-01 00:00:00` から `2026-10-04 01:28:00` より前の exposure に `roman_wfi_bam_0004.asdf` が選択される. この BAM は, 2026-09-18 から 2026-09-20 の CAR-86.4 および CAR-86.7 恒星観測から導出された.

BAM は WFI, observatory boresight, FGS の相対 alignment に関係する. CRDS では BAM と detector distortion が別の参照ファイル種別として管理されているため, BAM 更新を detector distortion model の更新と同一視してはならない. 公式 delivery note は quaternion の数値差や astrometric shift の大きさを示していない.

## ローカル環境の設定

`romancal` から Roman CRDS へ接続する基本設定は次のとおり.

```bash
export CRDS_SERVER_URL=https://roman-crds.stsci.edu
export CRDS_PATH=$HOME/data/crds_cache/
```

特定の context で再処理する場合は `CRDS_CONTEXT` を明示する.

```bash
export CRDS_CONTEXT=roman_0075.pmap
```

`latest` context は更新され得る. 既存結果を再現する場合は, 処理時点の具体的な PMAP 名を指定し, pipeline release に対応する検証済み context も確認する. 公式 `romancal` release table では, release 1.0.2 (26Q3_B22.3) に `roman_0065.pmap` が対応付けられている. release と対応 context の組は, CRDS server 上の `latest` context と必ずしも同一ではない.

## 解析時の確認事項

- archive product では ASDF metadata と処理 log から CRDS context と参照ファイル名を確認する.
- ローカル再処理では `romancal` version, `CRDS_CONTEXT`, parameter file, 実行時刻を記録する.
- 異なる context の結果を比較する場合は PMAP 全体の差分を確認し, BAM や distortion だけが異なると仮定しない.
- WFI spectral mode の処理では, `relflux` と `sflat` が commissioning data に基づく更新前の dummy file であることを確認する.
- 過去の data release や operational build は, server 上の `latest` ではなく指定された検証済み context を用いる場合がある.
- BAM や distortion 更新の astrometry への影響を報告する場合は, CRDS の説明と実データで測定した結果を区別する.

## Context 更新履歴

| Context | Activation | 主な変更 |
|---|---|---|
| `roman_0075.pmap` | 2026-10-08 | CAR-86.11 に基づく SIAF `PRDOPSRSOC-012` を用いて distortion と pixel area map を更新 |
| `roman_0074.pmap` | 2026-10-05 (delivery) | 最初の PFAC 後に適用する軌道上 quaternion 値で BAM を更新 |
| `roman_0073.pmap` | 2026-10-02 | grism/prism 用の初期 `optmodel`, `absflux`, `relflux`, `sflat`, `specpsf` を追加 |
| `roman_0072.pmap` | 2026-09-24 | flat, saturation, mask, linearity, inverse linearity, photom, pixel area, distortion を更新 |
| `roman_0067.pmap` | 2026-09-21 | CAR-86.4/86.7 の恒星観測に基づく `roman_wfi_bam_0004.asdf` を追加 |
| `roman_0066.pmap` | 2026-09-19 | CAR-86.4 の恒星観測に基づく BAM を追加 |
| `roman_0065.pmap` | 2026-09-16 | commissioning で測定した軌道上 BAM を初めて追加 |

注: `roman_0074.pmap` は context history に delivery 日付のみが表示されるため, 表では delivery 日付を示した.

## CRDS mapping 参照情報

[Roman CRDS](https://roman-crds.stsci.edu/) (STScI, 2026-10-10 閲覧) の context と RMAP を次にまとめる.

| Context | 日付 | 参照した RMAP |
|---|---|---|
| [`roman_0075.pmap`](https://roman-crds.stsci.edu/context_table/roman_0075.pmap) | Delivery/Activation: 2026-10-08 | [`area_0004`](https://roman-crds.stsci.edu/browse/roman_wfi_area_0004.rmap), [`distortion_0004`](https://roman-crds.stsci.edu/browse/roman_wfi_distortion_0004.rmap) |
| [`roman_0074.pmap`](https://roman-crds.stsci.edu/context_table/roman_0074.pmap) | Delivery: 2026-10-05 | [`bam_0006`](https://roman-crds.stsci.edu/browse/roman_wfi_bam_0006.rmap) |
| [`roman_0073.pmap`](https://roman-crds.stsci.edu/context_table/roman_0073.pmap) | Activation: 2026-10-02 | [`optmodel_0002`](https://roman-crds.stsci.edu/browse/roman_wfi_optmodel_0002.rmap), [`absflux_0002`](https://roman-crds.stsci.edu/browse/roman_wfi_absflux_0002.rmap), [`specpsf_0002`](https://roman-crds.stsci.edu/browse/roman_wfi_specpsf_0002.rmap), [`relflux_0002`](https://roman-crds.stsci.edu/browse/roman_wfi_relflux_0002.rmap), [`sflat_0002`](https://roman-crds.stsci.edu/browse/roman_wfi_sflat_0002.rmap) |
| [`roman_0072.pmap`](https://roman-crds.stsci.edu/context_table/roman_0072.pmap) | Activation: 2026-09-24 | [`flat_0008`](https://roman-crds.stsci.edu/browse/roman_wfi_flat_0008.rmap), [`area_0003`](https://roman-crds.stsci.edu/browse/roman_wfi_area_0003.rmap), [`distortion_0003`](https://roman-crds.stsci.edu/browse/roman_wfi_distortion_0003.rmap), [`mask_0005`](https://roman-crds.stsci.edu/browse/roman_wfi_mask_0005.rmap), [`photom_0006`](https://roman-crds.stsci.edu/browse/roman_wfi_photom_0006.rmap), [`linearity_0006`](https://roman-crds.stsci.edu/browse/roman_wfi_linearity_0006.rmap), [`inverselinearity_0006`](https://roman-crds.stsci.edu/browse/roman_wfi_inverselinearity_0006.rmap), [`saturation_0004`](https://roman-crds.stsci.edu/browse/roman_wfi_saturation_0004.rmap) |
| [`roman_0067.pmap`](https://roman-crds.stsci.edu/context_table/roman_0067.pmap) | Activation: 2026-09-21 | [`bam_0005`](https://roman-crds.stsci.edu/browse/roman_wfi_bam_0005.rmap) |
| [`roman_0066.pmap`](https://roman-crds.stsci.edu/context_table/roman_0066.pmap) | Activation: 2026-09-19 | BAM |
| [`roman_0065.pmap`](https://roman-crds.stsci.edu/context_table/roman_0065.pmap) | Activation: 2026-09-16 | BAM |
