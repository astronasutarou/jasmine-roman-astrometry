---
title: Roman CRDS と WFI 校正参照ファイル
description: Roman WFI の現行 CRDS context, 校正参照ファイル, 解析時の確認事項
tags: [Roman, WFI, CRDS, calibration, reference file, spectroscopy, BAM, distortion, astrometry, romancal]
---

# Roman CRDS と WFI 校正参照ファイル

このページの内容は主に以下のソースを引用・参考にしている:

- [CRDS for Reference Files](https://roman-docs.stsci.edu/data-handbook-home/accessing-wfi-data/crds-for-reference-files) (STScI, Publication: 2024-01-05, Latest Update: 2024-12-20)
- [`romancal` DMS Operational Build Versions](https://github.com/spacetelescope/romancal#dms-operational-build-versions) (STScI)
- [`romancal` 1.0.3 release](https://github.com/spacetelescope/romancal/releases/tag/1.0.3) (STScI, 2026-10-19)

## 概要

Calibration Reference Data System (CRDS) は, Roman WFI Data Pipelines が使用する校正参照ファイルと parameter file を管理する. exposure-level pipeline は, Level 1 (L1) の未校正データから Level 2 (L2) の校正済み rate image を生成する際に CRDS を利用する. Roman I-Sim などの simulation tool も一部の参照ファイルを利用する.

CRDS context は pipeline mapping (PMAP) ファイルで表され, 各参照ファイル種別に使用する reference mapping (RMAP) を指定する. RMAP は観測メタデータを参照ファイルへ対応付ける. `USEAFTER` は参照ファイルの適用開始時刻であり, CRDS は観測時刻以前で最も近い `USEAFTER` と, 参照ファイル種別ごとの追加条件を用いてファイルを選択する.

処理に使われた context 名はデータ製品の ASDF metadata に記録される. L2 ASDF では, 使用した各参照ファイル名も metadata の dictionary と処理 log から確認できる. 再現性を保つには, `romancal` version だけでなく context 名と参照ファイル名を記録する.

## 現行の校正状況

2026-10-14 時点の `latest` context は, 2026-10-13 に delivery および activation された `roman_0077.pmap` である. この context は, `roman_0076.pmap` までの imaging, detector, spectral calibration files を引き継ぎ, Boresight Alignment Matrix (BAM), detector distortion, pixel area map を更新している.

### Imaging および detector calibration

現行 context が引き継ぐ主な参照ファイルは次のとおりである.

| 種別 | ファイル数・適用範囲 | 公式に示された由来 |
|---|---|---|
| flat | 144 files: imaging の 18 detectors × 8 filters | TVAC および SCIPA ground testing |
| saturation, mask, linearity, inverse linearity, photom | 各 18 files: imaging および spectral modes | TVAC および SCIPA ground testing |

flat, saturation, mask, linearity, inverse linearity, photom は `2026-09-01 00:00:00` を `USEAFTER` とする detector 別ファイルを引き続き選択する. flat は imaging filter ごとに選択され, `GRISM`, `PRISM`, `DARK` には適用されない.

### 軌道上 SIAF に基づく distortion と pixel area

`roman_0077.pmap` の `roman_wfi_distortion_0006.rmap` と `roman_wfi_area_0006.rmap` は, 18 detectors すべてについて `2026-10-13 02:22:00` を `USEAFTER` とする新しい参照ファイルを選択する. 両 RMAP の change level は `SEVERE` である.

参照ファイルは, commissioning activity CAR-86.17 の軌道上観測に基づいて 2026-10-13 に生成された新しい SIAF から作成された. CRDS の delivery note は, この SIAF の release identifier を示していない. 一つ前の `roman_0076.pmap` は, CAR-50 Payload Focus and Alignment Campaign (PFAC) の反復後に実施された CAR-86.15 に基づく SIAF `PRDOPSRSOC-013` を用い, `2026-10-09 03:37:00` から適用する参照ファイルを選択していた. `roman_0075.pmap` は, PFAC 1 後の CAR-86.11 に基づく `PRDOPSRSOC-012` を用い, `2026-10-04 01:28:00` から適用する参照ファイルを選択していた. 0075 の delivery note は pixel scale の変更に加え, WFI-CEN aperture location に約 23 pixels の shift が見つかったと記録している.

この約 23 pixels は 0075 で報告された WFI-CEN aperture の参照位置の shift であり, 個々の天体の astrometric residual や sky coordinates 上の一様な shift を直接示す値ではない. 0077 の delivery note は detector ごとの係数差, 0076 からの shift, 天球上の residual を定量化していない. distortion は detector 座標から sky coordinates への WCS model に, pixel area map は歪みのある detector frame での測光補正に関係するため, context をまたいで位置・測光を比較する場合は実データで影響を評価する.

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

現行 context の `roman_wfi_bam_0008.rmap` は, `2026-10-13 02:22:00` 以降の exposure に `roman_wfi_bam_0007.asdf` を選択する. この BAM は, CAR-86.17 の軌道上観測に基づいて 2026-10-13 に生成された新しい SIAF から導出された. RMAP の change level は `SEVERE` である.

`2026-10-09 03:37:00` から `2026-10-13 02:22:00` より前の exposure には `roman_wfi_bam_0006.asdf` が選択される. この BAM は, CAR-50 PFAC の反復後に実施された CAR-86.15 の軌道上観測に基づく SIAF `PRDOPSRSOC-013` から導出された. `2026-10-04 01:28:00` から `2026-10-09 03:37:00` より前の exposure には, 最初の PFAC 後の commissioning で得た軌道上 quaternion 値を反映した `roman_wfi_bam_0005.asdf` が選択される. さらに, `2026-09-01 00:00:00` から `2026-10-04 01:28:00` より前の exposure には, 2026-09-18 から 2026-09-20 の CAR-86.4 および CAR-86.7 恒星観測から導出された `roman_wfi_bam_0004.asdf` が選択される.

BAM は WFI, observatory boresight, FGS の相対 alignment に関係する. CRDS では BAM と detector distortion が別の参照ファイル種別として管理されているため, 同じ SIAF delivery に基づく更新であっても, BAM と detector distortion model の役割を同一視してはならない. 0077 の delivery note は quaternion の数値差や astrometric shift の大きさを示していない.

## ローカル環境の設定

`romancal` から Roman CRDS へ接続する基本設定は次のとおり.

```bash
export CRDS_SERVER_URL=https://roman-crds.stsci.edu
export CRDS_PATH=$HOME/data/crds_cache/
```

特定の context で再処理する場合は `CRDS_CONTEXT` を明示する.

```bash
export CRDS_CONTEXT=roman_0077.pmap
```

`latest` context は更新され得る. 既存結果を再現する場合は, 処理時点の具体的な PMAP 名を指定し, pipeline release に対応する検証済み context も確認する. 公式 `romancal` release table では, 2026-10-19 公開の release 1.0.3 (26Q3_B22.6) に `roman_0077.pmap`, release 1.0.2 (26Q3_B22.3) に `roman_0065.pmap` が対応付けられている. release と対応 context の組は, CRDS server 上の `latest` context と必ずしも同一ではない. `1.0.3` は Build 22 系の patch release であり, Build 23 の `1.1.0` より新しい運用系列を意味しない.

### WFI18 first-read transient の変更

`romancal` 1.0.3 は, WFI18 の最初の resultant に現れる transient signal を補正する `wfi18_transient` step を更新した. 4 resultants の exposure では, 従来の affected rows の masking への fallback に代えて, 最後の 2 resultants から弱く照射された pixels を選び, transient を fit して減算する. さらに, 弱照射 pixels の選択基準が各 column の中央値と比較されていた実装誤りを修正し, 意図どおり各 row の中央値と比較するようになった. この処理は WFI imaging, prism, grism の WFI18 data に適用される.

この変更は CRDS reference file の追加・差し替えではなく, 同じ `roman_0077.pmap` に対応する pipeline code の変更である. WFI18 の結果を比較・再現する場合は, CRDS context だけでなく `romancal` version も確認する.

## 解析時の確認事項

- archive product では ASDF metadata と処理 log から CRDS context と参照ファイル名を確認する.
- ローカル再処理では `romancal` version, `CRDS_CONTEXT`, parameter file, 実行時刻を記録する.
- 異なる context の結果を比較する場合は PMAP 全体の差分を確認し, BAM や distortion だけが異なると仮定しない.
- WFI spectral mode の処理では, `relflux` と `sflat` が commissioning data に基づく更新前の dummy file であることを確認する.
- 過去の data release や operational build は, server 上の `latest` ではなく指定された検証済み context を用いる場合がある.
- BAM や distortion 更新の astrometry への影響を報告する場合は, CRDS の説明と実データで測定した結果を区別する.

## Context 更新履歴

[Roman CRDS](https://roman-crds.stsci.edu/) (STScI, 2026-10-14 閲覧) の主な context 更新を次にまとめる. RMAP のリンクは, 各 context で更新された参照ファイル種別を示す.

| Context | 日付 | 更新内容 |
|---|---|---|
| [`roman_0077.pmap`](https://roman-crds.stsci.edu/context_table/roman_0077.pmap) | Delivery/Activation: 2026-10-13 | CAR-86.17 の軌道上観測に基づいて 2026-10-13 に生成された SIAF を反映した [BAM](https://roman-crds.stsci.edu/browse/roman_wfi_bam_0008.rmap), [distortion](https://roman-crds.stsci.edu/browse/roman_wfi_distortion_0006.rmap), [pixel area map](https://roman-crds.stsci.edu/browse/roman_wfi_area_0006.rmap) |
| [`roman_0076.pmap`](https://roman-crds.stsci.edu/context_table/roman_0076.pmap) | Delivery/Activation: 2026-10-09 | CAR-86.15 に基づく SIAF `PRDOPSRSOC-013` を反映した [BAM](https://roman-crds.stsci.edu/browse/roman_wfi_bam_0007.rmap), [distortion](https://roman-crds.stsci.edu/browse/roman_wfi_distortion_0005.rmap), [pixel area map](https://roman-crds.stsci.edu/browse/roman_wfi_area_0005.rmap) |
| [`roman_0075.pmap`](https://roman-crds.stsci.edu/context_table/roman_0075.pmap) | Delivery/Activation: 2026-10-08 | CAR-86.11 に基づく SIAF `PRDOPSRSOC-012` を反映した [distortion](https://roman-crds.stsci.edu/browse/roman_wfi_distortion_0004.rmap) と [pixel area map](https://roman-crds.stsci.edu/browse/roman_wfi_area_0004.rmap) |
| [`roman_0074.pmap`](https://roman-crds.stsci.edu/context_table/roman_0074.pmap) | Delivery: 2026-10-05 | 最初の PFAC 後の軌道上 quaternion 値を反映した [BAM](https://roman-crds.stsci.edu/browse/roman_wfi_bam_0006.rmap) |
| [`roman_0073.pmap`](https://roman-crds.stsci.edu/context_table/roman_0073.pmap) | Activation: 2026-10-02 | grism/prism 用の初期 [optical model](https://roman-crds.stsci.edu/browse/roman_wfi_optmodel_0002.rmap), [absolute flux calibration](https://roman-crds.stsci.edu/browse/roman_wfi_absflux_0002.rmap), [spectral PSF](https://roman-crds.stsci.edu/browse/roman_wfi_specpsf_0002.rmap), [relative flux calibration](https://roman-crds.stsci.edu/browse/roman_wfi_relflux_0002.rmap), [small-scale flat-field](https://roman-crds.stsci.edu/browse/roman_wfi_sflat_0002.rmap) |
| [`roman_0072.pmap`](https://roman-crds.stsci.edu/context_table/roman_0072.pmap) | Activation: 2026-09-24 | [flat](https://roman-crds.stsci.edu/browse/roman_wfi_flat_0008.rmap), [pixel area map](https://roman-crds.stsci.edu/browse/roman_wfi_area_0003.rmap), [distortion](https://roman-crds.stsci.edu/browse/roman_wfi_distortion_0003.rmap), [mask](https://roman-crds.stsci.edu/browse/roman_wfi_mask_0005.rmap), [photom](https://roman-crds.stsci.edu/browse/roman_wfi_photom_0006.rmap), [linearity](https://roman-crds.stsci.edu/browse/roman_wfi_linearity_0006.rmap), [inverse linearity](https://roman-crds.stsci.edu/browse/roman_wfi_inverselinearity_0006.rmap), [saturation](https://roman-crds.stsci.edu/browse/roman_wfi_saturation_0004.rmap) |
| [`roman_0067.pmap`](https://roman-crds.stsci.edu/context_table/roman_0067.pmap) | Activation: 2026-09-21 | CAR-86.4/86.7 の恒星観測に基づく [BAM](https://roman-crds.stsci.edu/browse/roman_wfi_bam_0005.rmap); `roman_wfi_bam_0004.asdf` を追加 |
| [`roman_0066.pmap`](https://roman-crds.stsci.edu/context_table/roman_0066.pmap) | Activation: 2026-09-19 | CAR-86.4 の恒星観測に基づく BAM |
| [`roman_0065.pmap`](https://roman-crds.stsci.edu/context_table/roman_0065.pmap) | Activation: 2026-09-16 | commissioning で測定した軌道上 BAM を初めて追加 |

注: `roman_0074.pmap` は context history に delivery 日付のみが表示されるため, 表では delivery 日付を示した.
