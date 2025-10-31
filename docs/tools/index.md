---
title: Roman Simulation Tools
description: Overview of simulation tools for the Nancy Grace Roman Space Telescope
tags: [simulation, tools, Roman, STPSF, STIPS, Pandeia, Roman I-Sim]
---

# Roman Simulation Tools

このページの内容は主に以下のソースを引用・参考にしています:

- [Simulation Tools Handbook Home](https://roman-docs.stsci.edu/simulation-tools-handbook-home)


Roman 宇宙望遠鏡の観測計画とデータ解析のためのシミュレーションツールとリソースの概要をまとめます. これらのツールは STScI の Roman Science Operations Center によって維持管理されています.

## 主要シミュレーションツール

### [STPSF](stpsf.md)
**点広がり関数 (PSF) シミュレーター**
stp
- **機能**: 光学モデルから PSF を生成
- **対象**: JWST と Roman 宇宙望遠鏡
- **用途**: 観測計画, データ解析ツール開発

### [STIPS](stips.md)
**Space Telescope Imaging Product Simulator**

- **機能**: 天文シーンの高速シミュレーション
- **出力**: Level 2 (L2) データプロダクト
- **特徴**: フルフレーム画像の効率的な生成

### Pandeia
**露出時間計算機 (Exposure Time Calculator)**

- **機能**: 高精度な露出時間計算
- **対象**: 小領域の天文シーン
- **形式**: Web インターフェースと Python API

### Roman I-Sim
**Roman Image Simulator**

- **機能**: 詳細な画像シミュレーション
- **出力**: Level 1 (L1) と Level 2 (L2) データプロダクト
- **特徴**: 実際の観測データに近い詳細なシミュレーション

## 開発支援ツール

### PySIAF
**座標変換ツール**

- **機能**: 装置, 天文台, 天球座標系間の変換
- **用途**: 観測計画, データ処理

### Synphot
**合成測光ツール**

- **機能**: 天体の合成測光計算
- **用途**: 観測シミュレーション, データ解析

### Jupyter Footprint Viewer
**観測フットプリント可視化ツール**

- **機能**: 観測領域の可視化
- **環境**: Jupyter Notebook

## ツール選択ガイド

| 用途 | 推奨ツール | 詳細 |
|------|------------|------|
| PSF 解析 | STPSF | 点広がり関数の詳細シミュレーション |
| 観測計画 | Pandeia | 露出時間とS/N比の計算 |
| 大領域シミュレーション | STIPS | 効率的な観測画像シミュレーション |
| 詳細画像シミュレーション | Roman I-Sim | 実観測データに近い出力 |

## ドキュメント体系

各ツールには 3 つの形式のドキュメントが提供されています：

1. **ユーザーガイド** (RDox): 基本的な使用方法と科学応用
2. **コードリポジトリ** (GitHub): ソースコードとサンプルノートブック
3. **開発者ドキュメント** (ReadTheDocs): 最新のインストール手順とAPI詳細

## ソフトウェアバージョン

現在のドキュメントは以下のバージョンに対応しています：

- **STPSF**: version 2.0
- **STIPS**: version 2.3.0 (2025年6月27日リリース)
- **Pandeia**: version 2025.5 (2025年5月21日リリース)
- **Roman I-Sim**: version 0.8.0 (2025年2月18日リリース)

---

**詳細情報**: [Roman Help Desk](https://stsci.service-now.com/roman) または各ツールの個別ドキュメントをご参照ください.
