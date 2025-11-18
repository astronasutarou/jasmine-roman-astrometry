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

## 概要

この論文では Nancy Grace Roman 宇宙望遠鏡の高緯度撮像サーベイ (HLIS) と Vera C. Rubin 天文台の Legacy Survey of Space and Time (LSST) の観測領域のうち, 重複する 20 平方度の領域においてシミュレートした観測画像を提示している.


## シミュレーション概要

- **観測範囲**: HLIS, LSST の重複領域のうち 20 平方度
- **データ**:
  - Roman:
    - `GALSIM` (Rowe et al. 2015) ベースの画像シミュレーション (Troxel et al., 2021)
    - フィルタ: Y106, J129, H158, F184
  - Rubin (LSST):
    - 既存の Dark Energy Science Collaboration (DESC) Data Challenge 2 (DC2) によるシミュレーション画像を使用 (DC2ImageSim)
    - `GALSIM` (Rowe et al. 2015) ベースの画像シミュレーション
    - フィルタ: u, g, r, i, z


## Roman HLIS Simulation

- Point-Spread Function (PSF)
    - 高解像度 (2048&times;2048) の瞳モデルから PSF を生成
    - 各フィルタ, および検出器ごとに異なる PSF を使用
- Source spectrum
    - 単色ではなく天体のスペクトルを反映させた chromatic PSF を採用
- Detector physics
    - Roman H4RG-10 の特性を反映させたシミュレーションを実施
    - 取り入れた効果は以下の通り:
        1. relative quantum efficiency
        1. brighter-fatter effect
        1. persistence
        1. dark current map
        1. classical non-linearity
        1. inter-pixel capacitance
        1. readout noise model
    - 詳細は Appendix B (Troxel et al., 2023) を参照

実際のサーベイを想定した観測計画に基づいて single exposure images を生成, それらを決められた領域で足し合わせることでより深い coadded images を作成している. coadded images のサイズは 8825&times;8825 ピクセルであり, 周囲の 500 pixel は隣接する領域とオーバラップしている. ピクセルスケールは 0.0575 arcsec であり, J129 フィルタで Nyquist sampling を達成するように設計されている.


## データセット

Troxel et al. (2023) で生成したデータセットは以下の通り.

1. HLIS 観測シークエンスを記録したカタログ
1. 画像生成に使用した天体カタログ (input catalog)
1. SCA images, coadded images ごとの天体カタログ
1. 装置由来のノイズ (detector physics) を含まない _true_ images
1. _true_ images から切り出した天体のサムネイル
1. 観測画像セット
    - 29153 枚の simple model images
    - 6653 枚の full SCA physical model images
1. 検出器ごとの &times;8 oversampled PSF 画像
1. 各フィルタ 1039 枚の coadded images (simple model)
1. coadded images での &times;8 oversampled PSF 画像
1. 天体検出 segmentation マップ

データは [Roman DC2 Simulated Image (Troxel 2023) Data Access][access] から入手可能.

---

## 参考文献

- Troxel, M. A. et al. (2023)
    - A joint Roman Space Telescope and Rubin Observatory synthetic wide-field imaging survey
    - Monthly Notices of the Royal Astronomical Society, 522, 2801
    - DOI: [10.1093/mnras/stad664](https://doi.org/10.1093/mnras/stad664)
    - arXiv: [2209.06829](https://arxiv.org/abs/2209.06829)
- Troxel, M. A. et al. (2021)
    - A synthetic Roman Space Telescope High-Latitude Imaging Survey: simulation suite and the impact of wavefront errors on weak gravitational lensing
    - Monthly Notices of the Royal Astronomical Society, 501, 2044
    - DOI: [10.1093/mnras/staa3658](https://doi.org/10.1093/mnras/staa3658)
    - arXiv: [1912.09481](https://arxiv.org/abs/1912.09481)
- Rowe, B. T. P. et al. (2015)
    - GALSIM: The modular galaxy image simulation toolkit
    - Astronomy and Computing, 10, 121
    - DOI: [10.1016/j.ascom.2015.02.002](https://doi.org/10.1016/j.ascom.2015.02.002)
    - arXiv: [1407.7676](https://arxiv.org/abs/1407.7676)
