# Kaggleで勝つ データ分析の技術
門脇大輔、阪田隆司、保坂桂佑、平松雄司 著
技術評論社

2023-12-28 - 2024-1-11

## 第1章 分析コンペとは？

### 1-1. 分析コンペって何

コンペの終了時刻はUTCタイムゾーン<br>日本時間とは9時間のずれ

### 1-2. 分析コンペのプラットフォーム

ランキング
- Competition
- Kernel
- Discussion
- データセット
API
通常のコンペ
カーネルコンペ

### 1-3. 分析コンペに参加してから終わるまで

### 1-4. 分析コンペに参加する意義

賞金や称号が得られないコンペでは他の参加者の熱量が少ないので、あまり新しいテクニックを学べない可能性がある

### 1-5. 上位を目指すためのポイント

データ理解（EDA: Exploratory Data Analysis）... 探索的データ分析

特徴量の作成

モデルの作成

モデルの評価
- クロスバリデーション
- accuracyは小さな改善をとらえづらいので、合わせてloglossという指標でも評価

モデルのチューニング
- ハイパーパラメータのチューニング
- 特徴量の選択
- クラスの分布が偏っている場合の取り扱いなど

アンサンブル
複数のモデルを組み合わせて予測

## 第2章 タスクと評価指標

### 2-1. 分析コンペにおけるタスクの種類

- 回帰タスク...RMSE,MAE
- 分類タスク
  - 二値分類
    - ラベル提出...F1-score
    - 確率提出...logloss, AUC
  - 多クラス分類
    - マルチクラス分類(どれか1つに属する)...multi-class logloss
    - マルチラベル分類(複数のクラスに属する場合あり)...meanF1, macroF1
- レコメンデーション
  - 順位をつけるもの...MAP@K
  - 順位をつけないもの...マルチラベル分類と同じ
- その他のタスク
  - 物体検出
  - セグメンテーション

### 2-2. 分析コンペのデータセット

- テーブルデータ
- 外部データ
- 時系列データ
- 画像、音声や自然言語などのデータ

### 2-3. 評価指標

■回帰における評価指標
RMSE： 平均平方二乗誤差
    外れ値の影響を受けやすいので、あらかじめ外れ値を除く処理をする
    np.sqrt(mean_squared_error(y_true, y_pred))
RMSLE: log
MAE: 絶対値
決定係数($R^2$)

■二値分類における評価指標～正例か負例かを予測値とする場合
accuracy(正答率)とerror rate(誤答率)
precision(適合率)とrecall(再現率)
F1-scoreとFβ-score
MCC

■二値分類における評価指標～正例である確率を予測値とする場合
logloss
AUC

■多クラス分類における評価指標
multi-class accuracy
multi-class logloss
mean-F1, macro-F1, micro-F1
quadratic weighted kappa

■レコメンデーションにおける評価指標
MAP@K

### 2-4. 評価指標と目的関数

### 2-5. 評価指標の最適化

out-of-fold

予測確率の調整

### 2-6. 評価指標の最適化の例

### 2-7. リーク(data leakage)

## 第3章 特徴量の作成

### 3-2. モデルと特徴量

特徴量の作成にあたっては、どのモデルの入力として特徴量を用いるかを意識する

■GBDT
- 数値の大きさ自体には意味がなく、大小関係のみが影響する
- 欠損値があってもそのまま扱うことができる
- 決定木の分岐の繰り返しにより変数間の相互作用を反映する
- label encodingでOK

■ニューラルネット
- 数値の大きさが影響する
- 欠損値を埋めなくてはいけない
- 前の層の出力を結合する計算などによって変数間の相互作用を反映する
- one-hot encodingがのぞましい

決定木の気持ちになって考える

### 3-3. 欠損値の扱い

1. 欠損値のまま取り扱う
2. 欠損値を代表値で埋める
3. 欠損値を他の変数から予測する
   重要な特徴量の欠損値は、予測による補完
4. 欠損値から新たな特徴量を作成する
5. データ上の欠損の認識(-1や9999など)
   - データ読み込み時にna_values=[]
   - 読み込んだ後、df['']=df[''].replace(-1, np.nan)

### 3-4. 数値変数の変換

1. 標準化
   線形回帰やロジスティック回帰などの線形モデル、ニューラルネット等の場合に必要
   StandardScaler
   二値変数に対しては不要
2. Min-Maxスケーリング
   MinMaxScaler
3. 非線形変換
   - 対数をとる、log(x+1)をとる、絶対値の対数をとるなど
   - Box-Cox変換、Yeo-Johnson変換
   - generalized log transformation
   - その他の非線形変化
     - 絶対値をとる
     - 平方根をとる
     - 二乗をとる、n乗をとる
     - 二値変数とする
     - 数値の端数をとる
     - 四捨五入、切り上げ、切り捨て
4. clipping
5. binning
6. 順位への変換
7. RankGauss

### 3-5. カテゴリ変数の変換

- one-hot encoding
- label encoding
- feature hashing
- frequency encoding
- target encoding
- embedding

### 3-6. 日付・時刻を表す変数の変換

### 3-7. 変数の組合せ

### 3-8. 他のテーブルの結合

### 3-9. 集約して統計量をとる

1. 単純な統計量をとる
   - カウント、ユニーク数のカウント、存在の有無
   - 合計、平均、割合
   - 最大、最小、標準偏差、中央値、分位点、尖度、歪度
2. 時間的な統計量をとる
3. 条件を絞る
4. 集計する単位を変える

### 3-10. 時系列データの扱い

1. 予測する時点より過去の情報のみを使う
2. ワイドフォーマットとロングフォーマット
   - pivotとstack
3. ラグ特徴量
   - 単純なラグ特徴量
    - shift関数
  - 移動平均やその他のラグ特徴量
    - rolling関数とmeanなどの要約関数の組合せ
      - .shift().rolling(window=  ).mean()
  - リード特徴量
    - shift(-  )

### 3-11. 次元削減・教師なし学習による特徴量

1. 主成分分析(PCA)
   - sklearn.decomposition PCA
   - TruncatedSVD 
2. 非負値行列因子分解(NMF)
3. LDA(Latent Dirichlet Allocation)
4. LDA(Linear Dicriminant Analysis)
5. t-SNE
6. UMAP
7. オートエンコーダ
8. クラスタリング

### 3-12. その他のテクニック

1. 背景にあるメカニズムから考える
   - ユーザの行動について考える
   - サービスを提供する側の動きについて考える
   - 業界で一般的に行われている手法
   - 複数の変数を組み合わせて指数を作ってみる
   - 自然現象のメカニズムを考える
   - 対象のサービスを実際に利用してみる
2. レコード間の関係性に注目する
3. 相対値に注目する
4. 位置情報に注目する
5. 自然言語処理の手法
6. 自然言語処理の応用
7. トピックモデルの応用によるカテゴリ変数の変換
8. 画像特徴量を扱う手法
9. decision tree feature transformation
10. 匿名化されたデータの変換前の値を推測する
11. データの誤りを修正する

### 3-13. 分析コンペにおける特徴量の作成の例

## 第4章 モデルの作成

### 4-2. 分析コンペで使われるモデル

- 勾配ブースティング木(GBDT)
- ニューラルネット
- 線形モデル
- その他のモデル
  - k近傍法
  - ランダムフォレスト
  - Extremly Randomized Trees
  - Reguralrizzed Greedy Forest
  - Field-aware Factorization Machines
サポートベクターマシンは精度や計算速度が見劣りするため、あまり使われない

### 4-3. GBDT(勾配ブースティング木)

■GBDTの特徴
- 特徴量は数値
- 欠損値を扱える
- 変数間の相互作用が反映される

- 精度が高い
- パラメータチューニングをしなくても精度がでやすい
- 不要な特徴量を追加しても精度が落ちにくい

- 特徴量をスケーリングする必要がない
- カテゴリ変数をone-hotエンコードしなくてよい(labelエンコードでＯＫ)
- 疎行列への対応がサポートされている

■GBDTの主なライブラリ
- xgboost
- lightgbm
- catboost

### 4-4. ニューラルネット

出力層の活性化関数
- 回帰   なし
- 二値分類  シグモイド関数
- マルチクラス分類   ソフトマックス関数

■ニューラルネットの特徴
- 特徴量は数値
- 欠損値不可
- 非線形性や変数間の相互作用が反映される
- 特徴量を標準化などでスケーリングする必要あり
- ハイパーパラメータの調整が難しい
- 多クラス分類に比較的強い(GBDTと同等)

■ニューラルネットの主なライブラリ
- keras
- pytorch
- chainer
- tensorfrow

### 4-5. 線形モデル

■線形モデルの特徴
- 特徴量は数値
- 欠損値不可
- 非線形性を表現するには明示的に特徴量を作成する必要がある
- 特徴量は標準化が必要
- 特徴量作成に丁寧な処理が必要

■線形モデルの主なライブラリ
- sklearn.linear_modelモジュール
- vowpal wabbit

### 4-6. その他のモデル

- k近傍法
- ランダムフォレスト
- Ｅxtremely Randomized Trees
- Regularized Greedy Forest
- Field-aware Factorization Machine

### 4-7. モデルのその他のポイントとテクニック

## 第5章 モデルの評価

### 5-2. バリデーションの手法

- hold-out法
  - train_test_split()
  - KFold()
- クロスバリデーション(CV)
- stratified k-fold
- group k-fold
- leave-one-out
  
### 5-3. 時系列データのバリデーション手法

- 時系列データのhold-out法
- 時系列データのクロスバリデーション(時系列に沿って行う方法)
- 時系列データのクロスバリデーション(単純に時間で分割する方法)

### 5-4. バリデーションのポイントとテクニック

学習データとバリデーションデータの分割方法は、学習データとテストデータの分割方法をまねる

## 第6章 モデルのチューニング

### 6-1. パラメータチューニング

- 手動
- グリッドサーチ
- ランダムサーチ
- ベイズ最適化
  - hyperopt
  - oputuna
  - gpyopt
  - speamint
  - scikit-optimize

■パラメータチューニングのポイント
- すべてのパラメータを調整する必要はない
- 重要なパラメータから調整する
  
### 6-2. 特徴選択および特徴量の重要度

■相関係数
- numpy corrcoef
- scipy.stats spearmanr
- pandas corr

■特徴選択
- numpy argsort
- scikit-learn feature_selection SelectKBest

■カイ二乗統計量

■相互情報量

！ 特徴選択は学習データ全体でなくout-of-foldで検証するのが望ましい（リーク）

■特徴量の重要度の出力
- ランダムフォレスト
  - fi = model.feature_importance_
- xgboost
  - fscore = model.get_score(importance_type='total_gain')
    fscore = soreted([(k, v)for k, v in fscore.items()], key=lambda tpl: tpl[1], reverse=True)
    print(fscore[: 5])
- permulation importance
  - ライブラリ eli5
- null importance
- boruta
- xgbfir

### 6-3. クラスの分布が偏っている場合

- アンダーサンプリング・オーバーサンプリング
  負例または正例の一部のみを使用してモデルを学習させる方法
- 重み付け

## 第7章 アンサンブル

### 7-2. シンプルなアンサンブル手法

- 平均
- 加重平均

- 算術平均
- 幾何平均
- 調和平均
- n乗平均

### 7-3. スタッキング

■手順
1. 学習データをクロスバリデーションのfoldに分ける
2. モデルをout-of-foldで学習させ、バリデーションデータへの予測値を作成
3. 各foldで学習したモデルでテストデータを予測し、平均などをとったものをテストデータの特徴量とする
4. 2～3をスタッキングしたいモデルの数だけ繰り返す
5. 2～4で作成した特徴量を使ってモデルの学習と予測を行う

時系列データではスタッキングではなく、モデルの加重平均によるアンサンブルが用いられることが多い

### 7-4. どんなモデルをアンサンブルすると良いか？

◎精度よりも多様性が重要

- GBDT
- ニューラルネット
- 線形モデル
- K近傍法
- ランダムフォレスト
- Regularized Greedy Forest(RGF)
- Field-aware Factorization Machines(FFM)

モデルが同じでもハイパーパラメータを変えてみる

特徴量を変える

問題のとらえ方を変える

### 7-5. 分析こんぺにおけるアンサンブルの例

## 付録

### A.1 分析コンペの参考資料

### A.2 参考文献

### A.3 本書で参照した分析コンペ
