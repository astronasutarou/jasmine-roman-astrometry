---
title: High-Latitude Wide-Area Survey
description: Core Community Survey for wide-area high-latitude imaging and spectroscopy with Roman Space Telescope
tags: [Roman, HLWAS, High Latitude, wide-area, survey, cosmology]
---

# High-Latitude Wide-Area Survey (HLWAS)

このページでは, Nancy Grace Roman Space Telescope の Core Community Surveys (CCS) の 1 つである High-Latitude Wide-Area Survey (HLWAS) についてまとめる.

主な情報源は以下である:

- STScI: [High-Latitude Wide-Area Survey](https://roman-docs.stsci.edu/roman-community-defined-surveys/high-latitude-wide-area-survey)
- NASA GSFC: [Core Community Surveys: Overview and Definition](https://roman.gsfc.nasa.gov/science/core_community_survey_definition.html)
- ROTAC, 2025: Roman Observations Time Allocation Committee Final Report (arXiv:2505.10574)


## 概要と位置づけ

High-Latitude Wide-Area Survey (HLWAS) は, Roman の Wide Field Instrument (WFI) を用いて天球の約 12 % に相当する ~5,100 deg² を撮像・スリットレス分光する広域サーベイである. その主目的は, 弱重力レンズ, 銀河クラスタリング, バリオン音響振動 (BAO), 赤方偏移空間歪み (RSD) など複数の手法を組み合わせることで, 宇宙の加速膨張の起源と宇宙構造形成史を高精度に制約することである.

HLWAS は, Roman の Core Community Surveys (CCS) の 1 つとして, High-Latitude Time-Domain Survey (HLTDS) および Galactic Bulge Time-Domain Survey (GBTDS) と並んで定義されている. ROTAC (Roman Observations Time Allocation Committee) は, HLWAS 定義委員会による nominal (in-guide) サーベイ案をそのまま採択している. HLWAS は, ミッション 5 年間の一次運用期間にわたり実行され, 観測時間として合計 520 日が割り当てられている.

HLWAS は, 以下のような広い波長・空間スケールにわたる科学を可能にする:

- 弱重力レンズ (weak lensing) による銀河形状測定 ~6 億天体.
- スリットレス分光による銀河の赤方偏移測定 ~1,900 万天体 (うち 1 < z < 2 で ~930 万, 2 < z < 3 で ~170 万).
- 局所宇宙から再電離期に至る銀河・クエーサーの統計的研究.
- 太陽系小天体, 銀河系ハロー・厚い円盤の恒星, 近傍銀河の恒星集団など, 多様な天体の探査.


## HLWAS を構成する 4 つのティア

HLWAS は, 4 つの階層的なティアから構成される多層サーベイである:

- Wide Tier: F158 フィルターによる単色撮像で ~2,700 deg² をカバー.
- Medium Tier: F106 / F129 / F158 の 3 バンド撮像とスリットレス分光で ~2,400 deg² をカバー.
- Deep Tier: 2 つのディープフィールド (COSMOS, XMM-LSS) を合わせて ~19 deg² 観測し, 7 つの撮像フィルターとグリズム分光を実施.
- Ultra-Deep Tier: Deep Tier 内の COSMOS フィールド中に ~5.5 deg² の超深観測領域を設け, 3 バンド撮像をより深く実施.

これら 4 つのティアの概要を, 面積・フィルター構成・おおよその深さ (5σ 点源)・主な役割という観点から整理すると次のようになる:

| ティア | 面積 [deg²] | 主なフィルター | 特徴的な深さ (5σ, 点源) | 主な役割 |
| --- | --- | --- | --- | --- |
| Wide | ~2,700 | F158 | ~26.2 AB mag | H バンド単色像による広域弱レンズ用形状測定, BAO / RSD 用の銀河分布マッピング |
| Medium | ~2,400 | F106, F129, F158 + Grism | 撮像 ~26.4–26.5 AB mag, 線輝線フラックス限界 ~1.5 × 10^-16 erg s^-1 cm^-2 | 弱レンズ・BAO を同時に実現するコアティア. 3 バンド色に基づく精密形状測定・光度赤方偏移と, グリズムによる精密赤方偏移測定を両立 |
| Deep | ~19 | F087, F106, F129, F146, F158, F184, F213 + Grism | CANDELS や 3D-HST より ~0.5 mag 深く, 面積はそれぞれ ~86 倍, ~100 倍 | Medium / Wide のフォト z ・形状測定のキャリブレーション, 高赤方偏移銀河・クエーサー, 太陽系小天体を含む多様な天体物理を対象とした深観測 |
| Ultra-Deep | ~5.5 | F106, F129, F158 | Hubble Ultra Deep Field と同等の深さを, 面積 ~1,900 倍で実現 | フォト z と弱レンズ形状測定の極限キャリブレーション, 最遠方銀河・クエーサー探査, ノイズバイアスの詳細評価 |

観測時間 520 日のうち, 約 71 % が Medium Tier, 15 % が Wide Tier, 11 % が Deep Tier (Ultra-Deep を含む) に割り当てられている. Ultra-Deep は Deep Tier の一部として扱われる.


## 科学的目標

HLWAS は, 宇宙の加速膨張と構造形成史を測定することを第一の目標としつつ, 太陽系から再電離期に至る幅広い天体物理をカバーするよう設計されている. 主な科学的ドライバーは次の通りである:

- 宇宙の加速膨張とダークエネルギーの制約:
  - 弱重力レンズ: 銀河形状測定 ~6 億天体に基づき, 成長率と幾何学の両面からダークエネルギーを制約する.
  - 銀河クラスタリング, BAO, 赤方偏移空間歪み (RSD): ~1,900 万銀河の赤方偏移により, 標準物差しとしての BAO と構造成長のダイナミクスを測定する.
  - 強重力レンズ・銀河団レンズなどとの組み合わせによるマルチプローブ解析.

- 銀河形成・進化の研究:
  - 近傍宇宙から z ≳ 10 付近までの銀河・クエーサー母集団を, 広いダイナミックレンジと統計精度で調べる.
  - Deep / Ultra-Deep ティアでは, CANDELS / 3D-HST を大幅に超える面積と深さで, 暗い銀河・AGN の物理特性を導出する.

- 銀河系と近傍宇宙の構造:
  - Wide / Medium ティアで, 銀河系ハローや厚い円盤の恒星分布, 近傍銀河のハロー構造, 局所宇宙の大規模構造をマッピングする.

- 太陽系小天体と時間変動天体:
  - Deep / Ultra-Deep ティアは, 高い時間サンプリングで太陽系小天体の動きを追跡できるよう設計されており, 小惑星・トランスネプチュニアン天体などの統計を拡充する.
  - 長期にわたる繰り返し観測により, 変光星・トランジェント・可変 AGN など, 多様な時間変動現象の研究が可能になる.

これらの科学目標は, ROTAC 最終報告や HLWAS 定義委員会報告書において, 定量的な予測と性能指標を用いて議論されている.


## サーベイ設計と基本仕様

### 全体設計と時間配分

HLWAS の設計は, 広い面積と多色・分光情報, そして深いキャリブレーションフィールドを組み合わせることで, 系統誤差の制御と統計精度の両立を図ることに重点が置かれている.

- 総観測時間: 520 日 (slew, フィルタ変更, settle などのオーバーヘッドを含む).
- 時間配分:
  - Medium Tier: ~71 %
  - Wide Tier: ~15 %
  - Deep Tier (Ultra-Deep を含む): ~11 %
  - 残りは細かなマージン・調整に充当.

Wide と Medium を合わせた撮像は, H バンドに相当する F158 で ~5,100 deg² のイメージングを提供し, これにより約 6 億個の銀河形状測定が可能になると見積もられている. Medium Tier のグリズム分光は, およそ 1,900 万個の銀河赤方偏移を測定することを目標としており, そのうち約 9.3 百万個が 1 < z < 2, 約 1.7 百万個が 2 < z < 3 の銀河である.


### ディザリング戦略とモザイク構成

HLWAS の全ティアは, 基本的に同じディザパターンを用いる:

- ブロードバンド撮像: 対角 3 点ディザ `LINEGAP3`.
- グリズム分光: 対角 2 点ディザ `LINEGAP2`.

`LINEGAP3` は, 特に WFI 検出器の Y 方向 (science reference frame) におけるギャップやノイズ特性を考慮して, 非一様なステップサイズを採用している. これにより, ディテクタギャップによる有効面積損失を最小化しつつ, サンプリング性を高めている.

モザイク構成はティアごとに異なるが, Wide / Medium では 4 × 4 タイル, Deep では 4 × 8 タイル, Ultra-Deep では 3 × 6 タイルなど, 直交格子に基づいた分割が用いられている.


## Wide Tier

### フットプリントとフィールド選択

Wide Tier は, F158 単色撮像により ~2,700 deg² をカバーする. この領域は, Rubin/LSST の広域サーベイエリア内に完全に含まれ, かつ北半球からの追観測が可能な赤道付近を中心に選ばれている. 主な選定基準は次の通りである:

- Rubin/LSST との重なりと北半球からのアクセス性.
- DESI 銀河サーベイとのオーバーラップ拡大.
- Euclid 主サーベイとの ~470 deg² の重なり.
- eROSITA の X 線マップ, CMB レンズマップや Sunyaev–Zel'dovich 効果マップとの重なり.
- Medium Tier の配置との整合性 (Medium を銀緯・黄道上で最適化した上で, Wide を主として黄道付近に配置).

これらの重なりにより, 多波長・多プローブの結合解析が可能となり, 系統誤差の評価や補正に重要な役割を果たす.


### 観測仕様

Wide Tier の観測仕様は, Medium Tier とほぼ同一の観測戦略を用い, フィルターを F158 のみに制限することで広い面積を確保している.

- 面積と配置:
  - およそ 2 つの広いフィールドで合計 ~2,700 deg² をカバー.

- 露光仕様:
  - 光学素子: F158.
  - ブロードバンド撮像露光時間: ~107 秒, 採用 MA テーブルは IM_107_7 (非等間隔の 7 resultants).

- モザイクおよびディザー:
  - フットプリントは 4 × 4 の矩形モザイクに分割.
  - 各ポインティングは対角 3 点ディザ `LINEGAP3` で撮像.
  - Wide Tier は異なるロール角で 2 パス撮像され, 2 パス間の相対ロール差は ~167° (±5°).

Wide Tier の深さは, 平均的に Medium Tier よりわずかに浅いが, 面積が大きいため, 弱レンズ形状測定のサンプル数は Medium に匹敵し, 組み合わせることで宇宙論パラメータの figure of merit を大きく向上させると期待されている.


## Medium Tier

### フットプリントとフィールド選択

Medium Tier は, HLWAS のコアとなるティアであり, F106, F129, F158 の 3 バンド撮像とスリットレス分光を組み合わせて ~2,400 deg² を観測する. フットプリントは 2 つの大きなフィールドからなり, それぞれが Deep Tier フィールドを内包している.

フィールド選択の主な基準は以下である:

- 銀河面から十分に離れた, 低減光・低星密度領域 (dust と crowding の低減).
- Rubin/LSST のサーベイ領域との重なり (光学バンドとの結合による PSF 色依存性補正とフォトメトリック赤方偏移精度向上).
- 低黄道光 (zodiacal background) を実現するため, 黄道面から離れた領域の優先.
- 北半球からのアクセス性 (追観測やマルチ波長追跡の容易さ).
- LSST Deep Drilling Fields (CDF-S, ELAIS-S1), COSMOS, XMM-LSS, Euclid Deep Field–South など, 既存・計画中のディープサーベイとの重なり.

これらの要件により, Medium Tier は, 弱レンズ・BAO の両側面から宇宙論を制約するための最適な領域として設計されている.


### 観測仕様

Medium Tier の観測仕様は次の通りである:

- 面積と配置:
  - 2 つのフィールドで合計 ~2,400 deg².
  - Deep Tier の 2 フィールド (COSMOS, XMM-LSS) は, それぞれ 1 つずつ Medium Tier フィールドの内部に完全に含まれる.

- 露光仕様:
  - 撮像用フィルター: F106, F129, F158.
  - グリズム: 低分散スリットレス分光.
  - ブロードバンド撮像露光時間: 107 秒, MA テーブル IM_107_7.
  - スリットレス分光露光時間: 190 秒, MA テーブル SP_190_12.

- モザイクとディザー:
  - Wide Tier 同様, 4 × 4 のモザイク単位に分割.
  - 各撮像ポインティングは `LINEGAP3` で 3 点ディザ.
  - 各分光ポインティングは `LINEGAP2` で 2 点ディザ.

- パス数とロール角:
  - 撮像 (F106, F129, F158) は, ロール角を変えて 2 パス取得. 2 パス間の相対ロール差は ~167° (±5°).
  - グリズム分光は 4 つのロール角 (4 パス) で観測し, F106 撮像に紐付けて実行する. 各 F106 撮像に対して, 同一ロール角と 8–20° オフセットの 2 つのグリズム観測を取得する.

この設計により, Medium Tier は弱レンズ形状測定に十分な PSF サンプリングと, BAO/RSD 解析に必要な多方向の分光配置を同時に実現している.


## Deep Tier

### フットプリントとフィールド選択

Deep Tier は, Medium Tier 内に配置された 2 つのフィールドからなり, 合計 ~19 deg² をカバーする:

- COSMOS フィールド (赤経 10h00m24.00s, 赤緯 +02°10′55.0″).
- XMM-LSS フィールド (赤経 02h22m50.00s, 赤緯 −04°45′00.0″).

これら 2 つのフィールドは, いずれも Rubin/LSST Deep Drilling Fields に対応しており, 豊富なマルチ波長データとのシナジーが期待される. また, 赤道近傍に位置するため, 北半球・南半球いずれの地上望遠鏡からも追観測が可能である.

Deep Tier は, Medium / Wide で実現される宇宙論解析に必要なキャリブレーションを提供することに加えて, 高赤方偏移銀河・クエーサーや, 低質量銀河, 太陽系小天体など, 多様な天体物理のフラッグシップフィールドとしての役割を担う.


### 観測仕様

Deep Tier の観測仕様は次の通りである:

- 面積と配置:
  - COSMOS, XMM-LSS の 2 フィールドで各 ~9.5 deg², 合計 ~19 deg².
  - COSMOS フィールド内に Ultra-Deep Tier (~5.5 deg²) が含まれる.

- 露光仕様:
  - 撮像用フィルター: F087, F106, F129, F146, F158, F184, F213.
  - グリズム: 低分散スリットレス分光.
  - ブロードバンド撮像露光時間: 294 秒 (すべてのフィルターで共通), MA テーブル IM_294_16.
  - スリットレス分光露光時間: 190 秒, MA テーブル SP_190_12.

- モザイクとディザー:
  - 各フィールドは 4 × 8 モザイクでタイル張りされる.
  - 各撮像ポインティングは `LINEGAP3` で 3 点ディザ.
  - 各分光ポインティングは `LINEGAP2` で 2 点ディザ.

- パス数とロール角:
  - 撮像は, 各フィルターで 5 パス (F146 のみ 6 パス) 観測される.
  - グリズム分光は合計 36 パスで, 2 シーズンにほぼ均等に分割される.
  - 各シーズン内で, ロール角は 2–5° の範囲で少しずつオフセットされる.

Deep Tier は, Medium Tier と同じ光学素子を用いるフィルターに関して, 撮像で ~1.2 mag 深く, 分光で線輝線フラックス限界がおよそ 2.5 倍深くなるよう設計されている. これにより, フォト z キャリブレーションや弱レンズ形状測定の系統誤差評価に必要なデータが得られる.


## Ultra-Deep Tier

### フットプリントと役割

Ultra-Deep Tier は, Deep Tier の COSMOS フィールド内部に設けられた ~5.5 deg² のサブフィールドであり, F106, F129, F158 の 3 バンドで Medium Tier 撮像の 30 倍程度の有効露光時間を達成するよう設計されている.

- 面積: ~5.5 deg² (Deep Tier の ~25 % に相当).
- 位置: Rubin/LSST Deep Drilling Field の COSMOS 領域内, HLWAS Deep Tier フットプリントと整合.

Ultra-Deep は, Hubble Ultra Deep Field (HUDF) と同程度の深さを, 面積としては ~1,900 倍に拡張して実現することを目指している. これにより, 非常に暗い銀河・クエーサーの統計的研究, 弱レンズ形状測定におけるノイズバイアスの詳細な評価などが可能となる.


### 観測仕様

Ultra-Deep Tier の観測仕様は次の通りである:

- 面積とモザイク:
  - ~5.5 deg² の領域を 3 × 6 のモザイクでカバー.

- 露光仕様:
  - 撮像用フィルター: F106, F129, F158.
  - ブロードバンド撮像露光時間: 294 秒, MA テーブル IM_294_16.
  - 各フィルターにつき 10 パス取得し, Deep Tier よりさらに深い積分時間を確保.

- モザイクおよびディザー:
  - 各撮像ポインティングは `LINEGAP3` で 3 点ディザ.

- パスのシーケンス:
  - COSMOS Deep Tier と同様のロール角シーケンスを用い, 太陽系小天体の時間変動を追跡できるように設計.
  - ミッション前半 2 年で, 合計深さの少なくとも半分に到達することを要件とする.


## 宇宙論・天体物理へのインパクト

HLWAS は, 次のような宇宙論・天体物理上のインパクトを持つと期待されている:

- ダークエネルギーと重力理論の制約:
  - Weak lensing, galaxy clustering, BAO, RSD, strong lensing などの複数プローブを, 同一望遠鏡・同一データセットから得られた一貫した観測で組み合わせられる.
  - フォト z と形状測定のキャリブレーション (Deep / Ultra-Deep), 広域の統計精度 (Wide / Medium) を同時に確保できる設計になっている.

- 高赤方偏移宇宙の探査:
  - Deep / Ultra-Deep で, 再電離期およびそれ以前の銀河候補を大面積にわたって検出し, 銀河形成の初期段階を統計的に研究できる.

- 銀河系・近傍宇宙・太陽系科学:
  - 低減光・低星密度の高銀緯領域における星の分布や運動をマッピングし, 銀河系ハローの構造やサブハローを制約する.
  - Deep / Ultra-Deep の高い時間サンプリングは, 太陽系小天体の軌道決定や小天体母集団統計に貢献する.

これらの科学テーマは, HLTDS, GBTDS, Galactic Plane Survey などの他 CCS / General Astrophysics Surveys と組み合わせることで, さらに広いスケール・時間スケールにわたる総合的なローマン科学を構成する.


## 観測スケジュールと他サーベイとの関係

HLWAS の観測は, ローマンの一次 5 年ミッション全体にわたって分散させて実行される予定であり, CCS や General Astrophysics Surveys, コロナグラフ観測との整合を取りながら詳細なスケジュールが決定される.

ROTAC および HLWAS 定義委員会は, 以下のような優先順を推奨している:

- Deep Tier:
  - ミッション開始後早期に, Deep Tier の全光学素子について 1 年以内にフル深度に到達すること.
  - Ultra-Deep Tier は, 運用開始 2 年以内に目標深度の少なくとも半分に到達すること.

- Medium / Wide Tier:
  - 宇宙論解析のためのキャリブレーションを確保するため, Wide Tier よりも Medium Tier を先行して実施することが推奨される.
  - Medium と Wide の観測はできるだけインターレースされることが望ましい. これは, キャリブレーションと系統誤差評価を簡素化し, スケジューラビリティを向上させる.

さらに, HLWAS の全ティアは, 黄道光や熱的背景などの天文背景がその位置での年間最小値の 25 % 以内となるような条件でスケジュールされることが推奨されている. Deep Tier では, COSMOS については 2 シーズンを跨いでロール角を変えながら撮像・分光を行い, Solar System 科学と系統誤差診断を両立させるシーケンスが提案されている.


## 将来の評価とアップデート

HLWAS の実装は, ローマン打ち上げ後のオンオービット性能評価と初期サーベイデータの解析結果を踏まえて再評価される予定である. ROTAC 最終報告および HLWAS 定義委員会報告では, 初期データに基づき評価すべき天体物理的・技術的な性能指標が整理されている.

必要に応じて, サーベイ実装に対する修正提案は, HLWAS に関する広範な専門性を有するコミュニティメンバーからなる委員会で審査され, NASA Project Science Office の承認を経て適用される. これにより, ローマン科学センター (STScI や IPAC) とコミュニティが連携しつつ, HLWAS の科学的インパクトを最大化するような柔軟な運用が可能になる.


## 関連情報

- STScI: [High-Latitude Wide-Area Survey](https://roman-docs.stsci.edu/roman-community-defined-surveys/high-latitude-wide-area-survey)
- NASA GSFC: [Core Community Surveys: Overview and Definition](https://roman.gsfc.nasa.gov/science/core_community_survey_definition.html)
- ROTAC, 2025: "Roman Observations Time Allocation Committee: Final Report and Recommendations" (arXiv:2505.10574)
- Rubin/LSST Survey Cadence Optimization Committee, PSTN-055
- HLWAS Definition Committee Report (Appendix C.1, ROTAC 報告書内)
