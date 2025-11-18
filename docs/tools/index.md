---
title: Roman Simulation Tools
description: Overview of simulation tools for the Nancy Grace Roman Space Telescope
tags: [simulation, tools, Roman, STPSF, STIPS, Pandeia, Roman I-Sim]
---

# Roman Simulation Tools

このページの内容は主に以下のソースを引用・参考にしている:

- [Simulation Tools Handbook Home](https://roman-docs.stsci.edu/simulation-tools-handbook-home)


Roman 宇宙望遠鏡の観測計画とデータ解析のためのシミュレーションツールとリソースの概要をまとめる. これらのツールは STScI の Roman Science Operations Center によって維持管理されている.

## 主要シミュレーションツール

- **[STPSF](stpsf.md)**: Space Telescope Point-Spread Function (PSF) Simulator
    - 光学モデル (波面誤差) から Point-Spread Function を生成するツール. JWST と Roman の各装置･検出器に対応した PSF をシミュレート可能.
- **[STIPS](stips.md)**: Space Telescope Imaging Product Simulator
    - 観測画像を高速にシミュレートするツール. 天体カタログと装置の PSF モデルを使用して Roman の観測画像 (Level 2 に相当) を生成する.
- **[Roman I-Sim](i-sim.md)**: Roman Image Simulator
    - 詳細な観測シミュレーションをおこなうツール. 実際の観測データに近い Level 1 および Level 2 データプロダクトを生成する.
- **[Pandeia](pandeia.md)**: Exposure Time Calculator
    - 高精度な露出時間計算ツール. 観測計画の最適化と S/N 比の予測に使用される.

## 開発支援ツール

- **[PySIAF](../inst/siaf.md)**: 座標変換ツール
    - Science Instrument Aperture File を読み込んで, 検出器座標から各種座標への変換をおこなうツール. Python のパッケージとして提供されている.
- **Synphot**: スペクトル合成ツール
    - 天体のスペクトルを合成するツール. 観測シミュレーションやデータ解析に使用される.

## ツール選択ガイド

| 用途 | 推奨ツール | 詳細 |
|------|------------|------|
| PSF 解析 | STPSF | Point-Spread Function の詳細シミュレーション |
| フルフレーム画像生成 | STIPS | 効率的な観測画像シミュレーション |
| 観測シミュレーション | Roman I-Sim | 実観測データに近い出力 |
| 観測計画 | Pandeia | 露出時間と S/N 比の計算 |
