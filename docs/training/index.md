---
title: Roman Training Series
description: Hands-on training series for the Nancy Grace Roman Space Telescope
tags: [Roman, training, tutorial, presentation]
---

# Roman Training Series

このページの内容は主に以下のソースを引用・参考にしている:

- [Roman Training Series](https://outerspace.stsci.edu/spaces/RTS/overview)
- [Webinar 1: Core Comunity Surveys and Related Tools](https://outerspace.stsci.edu/spaces/RTS/pages/356647063/Webinar+1+Core+Community+Surveys+and+Related+Tools)


## Overview

Ready, Set, Roman! Training Series は, STScI と IPAC の Roman Science Centers が主催する実践的なトレーニングシリーズである. Nancy Grace Roman Space Telescope の2026年秋の打ち上げと2025年秋の最初の提案募集に向けて, コミュニティが Roman の Core Community Surveys (CCS) と General Astrophysics Surveys (GAS) を Wide Field Instrument (WFI) で最大限活用するための知識とスキルの構築を支援する.

Roman は community-driven, survey-focused で完全にオープンアクセスの天文台である. 個別の観測プログラムのみに依存するのではなく, 多くの科学成果は Roman の豊富なアーカイブを分析するチームから生まれる予定である. Ready, Set, Roman! Training Series では, 提案準備, データアクセス, キャリブレーション, 解析に必要なツール, データ, ワークフローの実践的な経験を提供する.

### トレーニングシリーズの特徴

- **1時間のセッション**: Roman のデータを使用する準備に役立つツールとリソースを探索
- **ライブデモ**: Roman ツールやシミュレーションデータセットの実践的なデモンストレーション
- **充実したQ&A**: Roman スタッフとの質疑応答時間を十分に確保
- **初心者歓迎**: すべてのレベルの参加者を歓迎, 学生の参加を特に奨励

### 参加方法

- **メーリングリスト登録**: 通知とリマインダーを受け取るため [ROMANTRAINING-subscribe-request@maillist.stsci.edu](mailto:ROMANTRAINING-subscribe-request@maillist.stsci.edu) にメール
- **Roman Events Calendar**: [Roman Events Calendar](https://outerspace.stsci.edu/x/BYYDDw) で今後のウェビナーを追跡
- **Roman Forum 参加**: [Roman Forum](https://outerspace.stsci.edu/spaces/RSWGS/overview) に参加してコミュニティに接続

## Core Community Surveys and Related Tools

**開催日**: 2024年11月4日 (火) 1:00pm ET / 10:00am PT
**形式**: ライブウェビナー (録画視聴可能)

### セッション録画とリソース

- **録画視聴**: [Ready, Set, Roman! Training Series Home](https://outerspace.stsci.edu/spaces/RTS/overview)
- **プレゼンテーション資料**: [webinar1.pptx](https://outerspace.stsci.edu/download/attachments/352355880/webinar1.pptx)

### セッション概要

このトレーニングシリーズのキックオフセッションが開催され, Training Series の紹介と, Roman の3つの Core Community Surveys の科学ポテンシャルをより良く理解するための様々な Roman ツール, ドキュメント, データセットのツアーが提供された.

Roman の3つの主要なコミュニティサーベイについて詳しく解説:

1. **High-Latitude Wide-Area Survey (HLWAS)**
   - 広範囲の高銀緯領域をカバー
   - ダークエネルギーとダークマター研究の主力
   - 銀河形成と大規模構造の探査

2. **High-Latitude Time Domain Survey (HLTDS)**
   - 時間変動天体の系統的探査
   - 超新星, マイクロレンジング, AGN の研究
   - 過渡現象の発見と特性解明

3. **Galactic Bulge Time-Domain Survey (GBTDS)**
   - 銀河中心バルジ領域の時間変動監視
   - マイクロレンジング現象による惑星探査
   - 恒星種族と銀河構造の研究

### セッション内容

第 1 回セッションでは以下の内容が扱われた:

- **Ready, Set, Roman! Training Series へようこそ**: シリーズの目的と構成が紹介された
- **Roman ツール・ドキュメント エコシステム**: 利用可能なリソースの包括的なツアーが実施された
- **Roman Survey Science Priorities Poll 結果**: コミュニティの科学的優先順位に関するアンケート結果が発表された
- **Roman's Community Defined Surveys の理解**: 各サーベイの詳細な解説が行われた
- **Roman Footprint Tool デモ**: 実際のツールを用いたライブデモンストレーションが実演された

### Roman Footprint Tool

Roman の観測フットプリントを可視化・解析するための強力なツールである:

#### 主要機能
- **観測領域の可視化**: Core Community Surveys の観測予定領域を地図上に表示
- **座標系変換**: 様々な座標系での観測計画の確認
- **重複領域の解析**: 複数のサーベイ間の重複領域の特定
- **観測戦略の最適化**: 効率的な観測計画の策定支援

#### インストールと使用法

```bash
# 専用環境の作成
conda create -n footprint python=3.11
conda activate footprint

# Roman notebooks リポジトリのクローン
git clone https://github.com/spacetelescope/roman_notebooks
git fetch origin pull/135/head:footprint_viz
git checkout footprint_viz

# 必要なパッケージのインストール
pip install astropy healpy healsparse skyproj jupyter pysiaf
python -m pip install "dask[distributed]" --upgrade
pip install ipympl jupyterlab --upgrade

# ノートブックの起動
cd roman_notebooks/content/notebooks/footprint_visualization/
jupyter notebook footprint_visualization.ipynb
```

## Simulating New and Accessing Simulated Data Sets

**開催予定日**: 2024年11月18日 (火) 1:00pm ET / 10:00am PT
**形式**: ライブウェビナー + 実践的チュートリアル

### セッション概要

このセッションでは, Roman のシミュレーションツールと既存のシミュレーションデータセットの包括的な紹介を提供する. Roman シミュレーションの独自性, 利用可能な様々なツール, そして Roman I-Sim の実践的なチュートリアルを通じて理解を深める.
