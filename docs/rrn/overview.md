---
title: Overview of Roman Research Nexus
description: Overview of the Roman Research Nexus (RRN), a cloud-based computing environment optimized for analyzing data from the Nancy Grace Roman Space Telescope.
tags: [Roman Research Nexus, RRN]
---
# Overview


このページの内容は主に以下のソースを引用・参考にしている:

- [Roman Research Nexus - STScI](https://roman-docs.stsci.edu/data-handbook/roman-research-nexus)

Roman Research Nexus (RRN) は, Space Telescope Science Institute (STScI) がミッションパートナーと協力して開発したクラウド型データ解析基盤であり, Roman ミッション運用期と同様のソフトウェアスタック・シミュレーション環境・共有計算資源をコミュニティに提供する. 2025 年 12 月中旬から Early Access フェーズに入り, 模擬データを用いたワークフロー開発と協調作業の準備が行えるようになっている.

- [Roman Research Nexus (Nexus Hub)](https://roman.science.stsci.edu/hub/)


## Platform Overview

RRN は Roman のキャリブレーションパイプライン, 解析ツール群, シミュレーションコード, 支援データセット, およびスケーラブルなクラウド計算インフラを単一のプラットフォームに統合する. Early Access では, 擬似観測データとサンプルワークフローを通じて Roman データ構造やクラウドネイティブ解析パターンを事前に習得できる. ミッション運用期においてもこの環境がコミュニティの中核的な作業空間として継続提供される計画である.

## Early Access Phase

- 2025 年 12 月中旬から Early Access を開始し, シミュレーテッド Roman データや解析ノートブックを自由に試行できる.
- チームスペース, 一貫したソフトウェアイメージ, リアルタイム共同編集サーバーにより, 研究グループ単位でのワークフロー開発を支援する.
- 今後も機能拡張を継続し, Roman 観測データの公開と歩調を合わせて中央クラウドハブとして進化させる計画である.

## Key Features

- **Pre-configured Software**: Roman キャリブレーション/解析パイプライン, シミュレーションパッケージ, Python ライブラリを予め整備し, ローカル環境構築を不要化.
- **Simulated Data Access**: 多様な模擬データセットを活用し, 観測開始前からワークフロー, I/O, データ構造を検証可能.
- **Integrated Compute**: CPU・メモリサイズを選択できるクラウドサーバー上でセッションを実行し, ユーザー/チーム領域のファイルはセッション間で永続化.
- **Collaboration Spaces**: チームディレクトリと共有ソフトウェア環境により, コードやデータを共同管理. Real-Time Collaboration Server を用いると複数メンバーが同一セッションを同時編集できる.
- **Tutorials and Workflows**: 個別タスク向けチュートリアルと, それらを組み合わせたワークフロー教材を提供し, 実践的なユースケースを段階的に再現できる.

## Accounts and Credits

### Individual Accounts

- MyST アカウントを保有していれば誰でも個人アカウントでセッションを起動できる.
- 個人アカウントは学習・試行用途として設計され, 既定で 30 クレジットが割り当てられる. 残高は 1 日あたり 1 クレジットずつ自動補充され, 上限 30 クレジットまで回復する.
- 計算資源は小規模構成 (Small) までに限定されるが, チーム領域のファイルにも読み書き可能. クレジット追跡のみが個人単位になる.
- ストレージは Amazon EFS 上に確保され, セッションを跨いでホームディレクトリが保持される.

### Team Accounts

- 研究グループや TAC 承認プログラム向けに, 共有クレジットと大規模サーバーを備えたチームアカウントが提供される.
- Early Access ではデフォルトで 500 クレジットが付与され, 最大 2,000 クレジットまで追加申請が可能. 2026 年 10 月以降の初年度運用では年間 4,000 クレジットを想定し, TAC 承認プログラムには上限内で自動配分される.
- クレジット残高が負になると計算が停止するため, 追加配分の審査 (通常 2 週間程度) を Roman Help Desk から申請する必要がある.
- チームディレクトリは共有 EFS 上で提供され, 将来的に専用 Amazon S3 バケット (プライベート) も導入予定である.

### Credit Usage and Resource Management

- クレジットは計算 (インスタンス時間・CPU 時間), ストレージ (EFS/S3), および egress に対して消費される. 現行レートでは egress 1 TB あたり 84 クレジットが目安である.
- 参考値として, 10 クレジットでは Small サーバーで約 79 インスタンス時間 (159 CPU 時間), Large (CPU-Optimized) で約 7 インスタンス時間 (236 CPU 時間) を利用できる. 1,000 クレジットではそれぞれ 7,900 / 700 インスタンス時間が目安となる.
- ストレージでは 10 クレジットで EFS 標準が約 1.0 TB-days, S3 標準が約 14 TB-days, 1,000 クレジットでそれぞれ約 105 TB-days と 1,400 TB-days の容量に相当する.
- コストモニター・ダッシュボードで計算/ストレージ/エグレスの内訳と最近のサーバー活動を追跡できる. UI は自動更新されるがリアルタイムではないため, 長時間ジョブ後は余裕を持って確認する.
- クレジット効率を高めるため, Small サーバーで開発を開始し必要に応じてスケールアップする, 使っていないセッションを停止する, 共有ディレクトリで重複データを避ける, 重要な成果物はバージョン管理する, などの運用が推奨されている.

## Environment Usage

Nexus にサインインすると, セッション構成を決める 3 つの選択肢を順番に設定する:

1. **Selecting an Account**: 個人またはチームアカウントを選択し, クレジット追跡先を指定する. ファイル可視性はアカウント選択に依存せず, 権限のあるディレクトリへアクセスできる.
2. **Selecting a Platform Image**: 最新のプラットフォームイメージには最新版の Roman パイプライン, 解析/シミュレーションツール, Python 環境, サンプルデータが含まれる. 過去バージョンも互換性維持のために残されるが, サポートは限定的な場合がある.
3. **Selecting a Server Configuration**: Small から Large (CPU/Mem optimized) までのサーバーサイズを選べる. Large 構成はチームアカウント専用で, 長時間ジョブや大規模データ解析向けに提供される.

チームアカウントでは Real-Time Collaboration Server を通じて, 複数メンバーが同一の JupyterLab セッションを同時操作できる. ただし RTC セッションはバージョン管理されないため, 編集内容はリポジトリへ速やかに反映させることが推奨される. サーバー起動には最大 15 分程度要するが, Small サーバーの一部はウォームスタートでより短時間で起動する場合がある.

## Support and Future Development

- Roman Help Desk ("Roman Research Nexus" カテゴリ) から, ログイン・サーバー起動・ソフトウェア環境・共有ディレクトリなどプラットフォーム機能に関するサポートを受けられる.
- STScI サポートチームはプラットフォーム動作に焦点を当て, ユーザーコードの最適化や科学的解析内容には原則として踏み込まない.
- Early Access 中も機能改善が継続しており, チーム向け S3 ストレージ, 一括アップロード/ダウンロードツール, バッチ/並列ワークフロー例, 時間変動解析や WFI TVAC データ, 分光関連教材などが開発中である.
- ユーザーコミュニティからのフィードバックは Nexus 内ドキュメントや Help Desk 経由で収集され, 運用フェーズに向けた優先度付けに活用される.
