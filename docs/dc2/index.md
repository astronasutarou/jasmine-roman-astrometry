---
title: Data Challenge 2 Simulation (Troxel et al., 2023)
description: Documentation for the Data Challenge 2 simulation (Troxel et al., 2023)
tags: [simulation, data challenge, Roman]
---

# Roman DC2 Simulated Image

|Item|Description|
|---|---|
|論文|[Troxel et al., 2023, MNRAS 522, 2801][Troxel]|
|データ概要|[Roman DC2 Simulated Image (Troxel 2023) Overview][IPAC]|
|データアクセス|[Roman DC2 Simulated Image (Troxel 2023) Data Access][access]|

[Troxel]: https://ui.adsabs.harvard.edu/abs/2023MNRAS.522.2801T/abstract
[IPAC]: https://irsa.ipac.caltech.edu/data/theory/Roman/Troxel2023/overview.html
[access]: https://irsa.ipac.caltech.edu/data/theory/Roman/Troxel2023/index.html

## 概要 (Overview)

本研究では Nancy Grace Roman 宇宙望遠鏡の高緯度撮像サーベイ (HLIS) の全深度と Vera C. Rubin 天文台の Legacy Survey of Space and Time (LSST) の5年間の観測を代表する20平方度の重複する合成撮像サーベイを提示し、検証した。

## 主な内容 (Main Content)

### シミュレーション詳細
- **観測範囲**: 20 平方度の重複領域
- **データ**: Dark Energy Science Collaboration (DESC) Data Challenge 2 (DC2) の既存 300 平方度の LSST 模擬撮像データに基づく
- **波長範囲**: 約 0.3--2.0 &mu;mをカバー

### Roman 宇宙望遠鏡のシミュレーション
- 検出器を用いた実験室測定から得られた Sensor Chip Assemblies (SCA) の詳細な物理特性を初めて完全色彩画像としてシミュレーション
- Roman シミュレーションパイプラインの更新
- 元の DC2 シミュレーションと比較した天体物理学的オブジェクトのシミュレーション手法の変更
- 結果として得られる Roman データプロダクトの説明

### 重要な発見
**天体の分解能比較**:
- LSST の画像で $i$ 等級 25 等より明るく識別されるオブジェクトのうち、**20--30 %**が Roman 画像では複数のオブジェクトとして識別可能
- これは地上と宇宙ベースの画像データセットの解像度差を示す重要な結果

### 科学的意義
本シミュレーションは、2020年代後半における地上・宇宙ベース撮像データセットの共同ピクセルレベル解析技術の開発と検証のための独自のテスト環境を提供する。特に、RomanとLSSTの合同解析における手法開発において重要な役割を果たす。

---

#### 論文情報

- 著者: Troxel, M. A. et al. (2023)
- 雑誌: Monthly Notices of the Royal Astronomical Society, 522, 2801
- DOI: [10.1093/mnras/stad664](https://doi.org/10.1093/mnras/stad664)
- arXiv: [2209.06829](https://arxiv.org/abs/2209.06829)
