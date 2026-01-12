---
title: Cycle 1
Short description: Overview of Nancy Grace Roman Telescope Cycle 1 proposal requirements, available datasets, and RRN computing resources.
tags: [Roman, Proposal, Cycle 1]
---
# Call for proposal -- Cycle 1

このページでは Nancy Grace Roman Space Telescope の Cycle 1 Call for Proposals に関する情報をまとめる. 特に新規観測提案ではなく, データ解析のみの提案についてとりあげる.

主な情報源は以下である:

- [Cycle 1 Call for Proposals](https://roman-docs.ipac.caltech.edu/roman-proposals-home/cycle-1-call-for-proposals)
- [Cycle 1 General Investigator Program Call for Proposals (PDF)](https://roman-docs.ipac.caltech.edu/files/208969831/208969832/2/1765405248840/Roman_Cycle1_Call.pdf)


## Important Dates

Cycle 1 プロポーザルのスケジュールは以下の通り.

- Call issued: 10 December 2025
- Due: 17 March 2026 (17:00 PDT)
- Review: May–June 2026
- Results: Summer 2026


## Program Scope

Cycle 1 のプロポーザルでカバーするトピックは以下の通り.

- Roman Wide Field Instrument (WFI) で観測されたデータの解析
- 公開キャリブレーションデータやパラレル観測データ[^1]の解析
- Roman/WFI の観測に直接関係のある理論研究や実験
- Roman/WFI の観測結果を説明するために必要な既存のデータセットの解析

Roman/WFI のデータには優先期間はなく, すべてのデータはパイプラインによって処理された後に即座に公開される. データは MAST を通じて取得することができる.

プロポーザルが採択されると STScI が提供するデータ解析環境 (Roman Research Nexus; RRN) を利用するためのチームアカウントを取得できる. RRN は Roman データの解析に最適化されたクラウドベースの計算環境であり, データ解析に必要なソフトウェアやツールがあらかじめインストールされている. RRN の利用により, 大規模データセットの効率的な解析が可能となる. 迅速なデータ解析と科学成果の創出のためには RRN の利用が望ましい.

プロポーザルでは以下のことを明確に示す必要がある.

1. どの観測プログラムのデータを使用するか
1. 科学目標の定義と観測データの役割
1. 観測データが Cycle 1 で利用可能なデータセットに含まれること
1. 科学目標が Roman community に与える貢献

[^1]: Roman Coronagraph Instrument (CGI) による観測がおこわれているときに, 副観測装置として WFI でも観測をおこなうことができる. このような観測で得られたデータをパラレル観測データと呼ぶ.


## Available Observation Programs

Cycle 1 のプロポーザルで解析対象とすることができる観測プログラムは以下の通り. ただし, Cycle 1 ではミッションのうち最初の 2 年間で取得したデータに限られる. プロポーザルでは科学目標が最初の 2 年間のデータで達成されるのかを明確にする必要がある.

- **High-Latitude Wide-Area Survey (HLWAS)**: およそ 5100 deg² の撮像データとグリズム分光データ
- **High-Latitude Time-Domain Survey (HLTDS)**: Ia 型超新星の探索をターゲットとした, &sim;5-day cadence の多色撮像データ
- **Galactic Bulge Time-Domain Survey (GBTDS)**: 銀河中心領域の 6 フィールドを高頻度で観測した撮像データ
- **Galactic Plane Survey**: 銀河面に沿った広域多色撮像データ
- **Calibration data**: キャリブレーション目的で取得されたデータセット
- **Parallel imaging**: CGI の観測に伴う副観測装置として取得された撮像データ


## Computational Resources

採択されたプロポーザルごとに Roman Research Nexus (RRN) のチームアカウントが発行される. チームアカウントには RRN を利用するための **credit** が割り当てられる. 計算リソースを使用すると credit が消費される.

各アカウントにはまず **500 credit** が割り当てられる. 必要に応じて RRN 内で credit の追加申請をおこなうことができる. 現状では累計で **4000 credit** まで申請可能ということになっている (NASA の予算状況に応じて設定される予定).

申請時にはプロポーザルで必要とされる credit の量を見積もって, 計算リソースのクラス (Resource Bin) を選択する必要がある. なお, _10000 credit を超えるような大規模なプロジェクトを RRN で実施することは難しいと考えられており, そういったプログラムでは外部の計算リソースを活用する (プロポーザルに明記する) ことが望ましい._

|Bin|Credit|
|:---|:---|
|Bin 1|credit &le; 4000|
|Bin 2|4000 &lt; credit &le; 7000|
|Bin 3|7000 &lt; credit &le; 10000|
|Bin 4|10000 &lt; credit|

### Credit Consumption

計算に使用するインスタンス･計算時間と使用する credit の目安は以下のとおり.

|Server|AWS name|CPU|RAM|credit/24 hrs usage|credit/1000 hrs compute|
|:---|:---|:---|:---|:---|:---
|Small|r5.large|2|16 GiB|3|63|
|Medium|r5.2xlarge|8|64 GiB|12|63|
|Large (CPU)|c6i.8xlarge|32|64 GiB|33|43|
|Large (RAM)|r5.4xlarge|16|128 GiB|24|63|

データを保管しておくだけでも credit が消費される. ストレージには頻繁にアクセスすることを想定した Elastic File System (EFS) と大量のデータを長期保存することを想定した Simple Storage Service (S3) が利用可能である. ストレージタイプごとの credit 消費量の目安は以下のとおり.

|Storage Type|credit/1 TB 1 month|
|:---|:---|
|EFS - Frequent Access|279|
|EFS - Infrequent Access[^2]|15|
|S3|10|

[^2]: 1 四半期に数回程度のアクセス頻度のデータを指す


## Proposal Preparation

プロポーザルは 2 枚の PDF ファイルを提出する必要がある.

- Science Program:
    - Science Plan
        - 研究の動機, 関連研究, 科学的意義
    - Analysis Plan
        - Roman のデータがどのように科学目標を達成するかを示す
        - データについての詳細 (領域, フィルタ, 等) を明記する
    - Implementation Plan
        - チームメンバの仕事の分担と見積もり
        - 使用するリソースの見積もり (Resource Bin)
        - 具体的な成果物の提示
        - 研究のスケジュール
- Team Expertise
    - チームメンバの専門性と研究業績 (最大 2 ページ)


レビューは dual anonymous で行われる. プロポーザルは著者がわからないように作成しなければならない. 個人名だけでなくチームメンバが特定されるような単語や表現を避ける必要がある.

レビューでは以下の点に注目して評価が行われる.

1. 科学的な意義
1. Roman データの適切な活用
1. データ解析を含む研究計画の実現性
1. 技術的な実現性 (観測提案のみ)
