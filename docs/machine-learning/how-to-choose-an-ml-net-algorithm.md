---
title: ML.NET アルゴリズムの選び方
description: 機械学習モデルに適した ML.NET アルゴリズムの選び方について説明します
author: natke
ms.topic: overview
ms.date: 04/20/1029
ms.openlocfilehash: d1c637437a7b285f2b66b597d616fcf39248697f
ms.sourcegitcommit: 682c64df0322c7bda016f8bfea8954e9b31f1990
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2019
ms.locfileid: "65557779"
---
# <a name="how-to-choose-an-mlnet-algorithm"></a><span data-ttu-id="c6019-103">ML.NET アルゴリズムの選び方</span><span class="sxs-lookup"><span data-stu-id="c6019-103">How to choose an ML.NET algorithm</span></span>

<span data-ttu-id="c6019-104">[ML.NET タスク](resources/tasks.md) ごとに選択できるトレーニング アルゴリズムは複数あります。</span><span class="sxs-lookup"><span data-stu-id="c6019-104">For each [ML.NET task](resources/tasks.md), there are multiple training algorithms to choose from.</span></span> <span data-ttu-id="c6019-105">どれを選択するかは、解決しようとしている問題、データの特性、利用できるコンピューティング リソースとストレージ リソースによって変わります。</span><span class="sxs-lookup"><span data-stu-id="c6019-105">Which one to choose depends on the problem you are trying to solve, the characteristics of your data, and the compute and storage resources you have available.</span></span> <span data-ttu-id="c6019-106">機械学習モデルのトレーニングは反復処理である点に注意することが重要です。</span><span class="sxs-lookup"><span data-stu-id="c6019-106">It is important to note that training a machine learning model is an iterative process.</span></span> <span data-ttu-id="c6019-107">場合によっては、最適なものを見つけるために複数のアルゴリズムを試す必要があります。</span><span class="sxs-lookup"><span data-stu-id="c6019-107">You might need to try multiple algorithms to find the one that works best.</span></span>

<span data-ttu-id="c6019-108">アルゴリズムは**特徴**に対して動作します。</span><span class="sxs-lookup"><span data-stu-id="c6019-108">Algorithms operate on **features**.</span></span> <span data-ttu-id="c6019-109">特徴は、入力データから計算された数値です。</span><span class="sxs-lookup"><span data-stu-id="c6019-109">Features are numerical values computed from your input data.</span></span> <span data-ttu-id="c6019-110">それらは機械学習アルゴリズムに最適な入力です。</span><span class="sxs-lookup"><span data-stu-id="c6019-110">They are optimal inputs for machine learning algorithms.</span></span> <span data-ttu-id="c6019-111">生の入力データを 1 つ以上の[データ変換](resources/transforms.md)を使用して特徴に変換します。</span><span class="sxs-lookup"><span data-stu-id="c6019-111">You transform your raw input data into features using one or more [data transforms](resources/transforms.md).</span></span> <span data-ttu-id="c6019-112">たとえば、テキスト データは、単語数と単語の組み合わせ数のセットに変換されます。</span><span class="sxs-lookup"><span data-stu-id="c6019-112">For example, text data is transformed into a set of word counts and word combination counts.</span></span> <span data-ttu-id="c6019-113">データ変換を使用して生のデータの種類から特徴が抽出されると、**特徴付けされた**と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="c6019-113">Once the features have been extracted from a raw data type using data transforms, they are referred to as **featurized**.</span></span> <span data-ttu-id="c6019-114">たとえば、特徴付けされたテキスト、特徴付けされた画像データなどです。</span><span class="sxs-lookup"><span data-stu-id="c6019-114">For example, featurized text, or featurized image data.</span></span>

## <a name="trainer--algorithm--task"></a><span data-ttu-id="c6019-115">トレーナー = アルゴリズム + タスク</span><span class="sxs-lookup"><span data-stu-id="c6019-115">Trainer = Algorithm + Task</span></span>

<span data-ttu-id="c6019-116">アルゴリズムは、**モデル**を生成するために実行される数学です。</span><span class="sxs-lookup"><span data-stu-id="c6019-116">An algorithm is the math that executes to produce a **model**.</span></span> <span data-ttu-id="c6019-117">アルゴリズムが異なると、特性が異なるモデルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c6019-117">Different algorithms produce models with different characteristics.</span></span> 

<span data-ttu-id="c6019-118">ML.NET では、同じアルゴリズムを異なるタスクに適用できます。</span><span class="sxs-lookup"><span data-stu-id="c6019-118">With ML.NET, the same algorithm can be applied to different tasks.</span></span> <span data-ttu-id="c6019-119">たとえば、Stochastic Descent Coordinated Ascent (確率的勾配座標上昇法) は、二項分類、多クラス分類、回帰に使用できます。</span><span class="sxs-lookup"><span data-stu-id="c6019-119">For example, Stochastic Descent Coordinated Ascent can be used for Binary Classification, Multiclass Classification, and Regression.</span></span> <span data-ttu-id="c6019-120">違いは、タスクに合わせてアルゴリズムの出力が解釈される方法にあります。</span><span class="sxs-lookup"><span data-stu-id="c6019-120">The difference is in how the output of the algorithm is interpreted to match the task.</span></span> 

<span data-ttu-id="c6019-121">ML.NET には、各アルゴリズムとタスクの組み合わせに対して、トレーニング アルゴリズムを実行し、解釈するコンポーネントが用意されています。</span><span class="sxs-lookup"><span data-stu-id="c6019-121">For each algorithm/task combination, ML.NET provides a component that executes the training algorithm and does the interpretation.</span></span> <span data-ttu-id="c6019-122">これらのコンポーネントはトレーナーと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="c6019-122">These components are called trainers.</span></span> <span data-ttu-id="c6019-123">たとえば、<xref:Microsoft.ML.Trainers.SdcaRegressionTrainer> には、**回帰**タスクに適用される **StochasticDualCoordinatedAscent** アルゴリズムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="c6019-123">For example, the <xref:Microsoft.ML.Trainers.SdcaRegressionTrainer> uses the **StochasticDualCoordinatedAscent** algorithm applied to the **Regression** task.</span></span>

## <a name="linear-algorithms"></a><span data-ttu-id="c6019-124">線形アルゴリズム</span><span class="sxs-lookup"><span data-stu-id="c6019-124">Linear algorithms</span></span>

<span data-ttu-id="c6019-125">線形アルゴリズムでは、入力データと一連の**重み**の線形結合から**スコア**を計算するモデルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c6019-125">Linear algorithms produce a model that calculates **scores** from a linear combination of the input data and a set of **weights**.</span></span> <span data-ttu-id="c6019-126">重みは、トレーニング中に推定されるモデルのパラメーターです。</span><span class="sxs-lookup"><span data-stu-id="c6019-126">The weights are parameters of the model estimated during training.</span></span>

<span data-ttu-id="c6019-127">線形アルゴリズムは、[線形分離可能](https://en.wikipedia.org/wiki/Linear_separability)である特徴の場合に適しています。</span><span class="sxs-lookup"><span data-stu-id="c6019-127">Linear algorithms work well for features that are [linearly separable](https://en.wikipedia.org/wiki/Linear_separability).</span></span>

<span data-ttu-id="c6019-128">線形アルゴリズムでトレーニングする前に、特徴を正規化しておくことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c6019-128">Before training with a linear algorithm, the features should be normalized.</span></span> <span data-ttu-id="c6019-129">こうすることで、ある特徴が他の特徴よりも結果に大きな影響を及ぼすことを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="c6019-129">This prevents one feature having more influence over the result than others.</span></span>

<span data-ttu-id="c6019-130">一般に、線形アルゴリズムはスケーラブルで高速であり、トレーニング コストが低コストで、予測も低コストです。</span><span class="sxs-lookup"><span data-stu-id="c6019-130">In general linear algorithms are scalable and fast, cheap to train, cheap to predict.</span></span> <span data-ttu-id="c6019-131">これらは、特徴の数と、おおよそのトレーニング データ セットのサイズによってスケールします。</span><span class="sxs-lookup"><span data-stu-id="c6019-131">They scale by the number of features and approximately by the size of the training data set.</span></span>

<span data-ttu-id="c6019-132">線形アルゴリズムは、トレーニング データに対して複数回実行されます。</span><span class="sxs-lookup"><span data-stu-id="c6019-132">Linear algorithms make multiple passes over the training data.</span></span> <span data-ttu-id="c6019-133">データセットがメモリに収まる場合は、トレーナーを追加する前に ML.NET パイプラインに[キャッシュ チェックポイント](xref:Microsoft.ML.LearningPipelineExtensions.AppendCacheCheckpoint*)を追加すると、トレーニングが高速になります。</span><span class="sxs-lookup"><span data-stu-id="c6019-133">If your dataset fits into memory, then adding a [cache checkpoint](xref:Microsoft.ML.LearningPipelineExtensions.AppendCacheCheckpoint*) to your ML.NET pipeline before appending the trainer, will make the training run faster.</span></span>

<span data-ttu-id="c6019-134">**線形トレーナー**</span><span class="sxs-lookup"><span data-stu-id="c6019-134">**Linear Trainers**</span></span>

|<span data-ttu-id="c6019-135">アルゴリズム</span><span class="sxs-lookup"><span data-stu-id="c6019-135">Algorithm</span></span>|<span data-ttu-id="c6019-136">プロパティ</span><span class="sxs-lookup"><span data-stu-id="c6019-136">Properties</span></span>|<span data-ttu-id="c6019-137">トレーナー</span><span class="sxs-lookup"><span data-stu-id="c6019-137">Trainers</span></span>|
|---------|----------|--------|
|<span data-ttu-id="c6019-138">Averaged perceptron (平均化パーセプトロン)</span><span class="sxs-lookup"><span data-stu-id="c6019-138">Averaged perceptron</span></span>|<span data-ttu-id="c6019-139">テキスト分類に最適です</span><span class="sxs-lookup"><span data-stu-id="c6019-139">Best for text classification</span></span>|<xref:Microsoft.ML.Trainers.AveragedPerceptronTrainer>|
|<span data-ttu-id="c6019-140">Stochastic descent coordinated ascent (確率的勾配座標上昇法)</span><span class="sxs-lookup"><span data-stu-id="c6019-140">Stochastic descent coordinated ascent</span></span>|<span data-ttu-id="c6019-141">既定のパフォーマンスが適切な場合は、調整が不要です</span><span class="sxs-lookup"><span data-stu-id="c6019-141">Tuning not needed for good default performance</span></span>|<span data-ttu-id="c6019-142"><xref:Microsoft.ML.Trainers.SdcaLogisticRegressionBinaryTrainer> <xref:Microsoft.ML.Trainers.SdcaNonCalibratedBinaryTrainer> <xref:Microsoft.ML.Trainers.SdcaMaximumEntropyMulticlassTrainer> <xref:Microsoft.ML.Trainers.SdcaNonCalibratedMulticlassTrainer> <xref:Microsoft.ML.Trainers.SdcaRegressionTrainer></span><span class="sxs-lookup"><span data-stu-id="c6019-142"><xref:Microsoft.ML.Trainers.SdcaLogisticRegressionBinaryTrainer> <xref:Microsoft.ML.Trainers.SdcaNonCalibratedBinaryTrainer> <xref:Microsoft.ML.Trainers.SdcaMaximumEntropyMulticlassTrainer> <xref:Microsoft.ML.Trainers.SdcaNonCalibratedMulticlassTrainer> <xref:Microsoft.ML.Trainers.SdcaRegressionTrainer></span></span>|
|<span data-ttu-id="c6019-143">L-BFGS</span><span class="sxs-lookup"><span data-stu-id="c6019-143">L-BFGS</span></span>|<span data-ttu-id="c6019-144">特徴数が多い場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="c6019-144">Use when number of features is large.</span></span> <span data-ttu-id="c6019-145">ロジスティック回帰トレーニング統計が生成されますが、AveragedPerceptronTrainer ほどスケールしません</span><span class="sxs-lookup"><span data-stu-id="c6019-145">Produces logistic regression training statistics, but doesn't scale as well as the AveragedPerceptronTrainer</span></span>|<span data-ttu-id="c6019-146"><xref:Microsoft.ML.Trainers.LbfgsLogisticRegressionBinaryTrainer> <xref:Microsoft.ML.Trainers.LbfgsMaximumEntropyMulticlassTrainer> <xref:Microsoft.ML.Trainers.LbfgsPoissonRegressionTrainer></span><span class="sxs-lookup"><span data-stu-id="c6019-146"><xref:Microsoft.ML.Trainers.LbfgsLogisticRegressionBinaryTrainer> <xref:Microsoft.ML.Trainers.LbfgsMaximumEntropyMulticlassTrainer> <xref:Microsoft.ML.Trainers.LbfgsPoissonRegressionTrainer></span></span>|
|<span data-ttu-id="c6019-147">Symbolic stochastic gradient descent (シンボリック確率的勾配降下法)</span><span class="sxs-lookup"><span data-stu-id="c6019-147">Symbolic stochastic gradient descent</span></span>|<span data-ttu-id="c6019-148">最も高速で最も正確な線形二項分類トレーナー。</span><span class="sxs-lookup"><span data-stu-id="c6019-148">Fastest and most accurate linear binary classification trainer.</span></span> <span data-ttu-id="c6019-149">プロセッサの数に合わせてスケールします</span><span class="sxs-lookup"><span data-stu-id="c6019-149">Scales well with number of processors</span></span>|<xref:Microsoft.ML.Trainers.SymbolicSgdLogisticRegressionBinaryTrainer>|

## <a name="decision-tree-algorithms"></a><span data-ttu-id="c6019-150">デシジョン ツリー アルゴリズム</span><span class="sxs-lookup"><span data-stu-id="c6019-150">Decision tree algorithms</span></span>

<span data-ttu-id="c6019-151">デシジョン ツリー アルゴリズムは、一連の決定を含むモデルを作成します。事実上、データ値を通るフロー チャートです。</span><span class="sxs-lookup"><span data-stu-id="c6019-151">Decision tree algorithms create a model that contains a series of decisions: effectively a flow chart through the data values.</span></span>

<span data-ttu-id="c6019-152">この種類のアルゴリズムを使用するために、特徴が線形に分離可能である必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c6019-152">Features do not need to be linearly separable to use this type of algorithm.</span></span> <span data-ttu-id="c6019-153">また、特徴ベクター内の個々の値は決定プロセスで独立して使用されるため、特徴を正規化する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c6019-153">And features do not need to be normalized, because the individual values in the feature vector are used independently in the decision process.</span></span>

<span data-ttu-id="c6019-154">一般的に、デシジョン ツリー アルゴリズムは非常に正確です。</span><span class="sxs-lookup"><span data-stu-id="c6019-154">Decision tree algorithms are generally very accurate.</span></span>

<span data-ttu-id="c6019-155">Generalized Additive Model (一般化加法モデル、GAM) を除き、特徴の数が多いと、説明可能性のないツリー モデルになる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c6019-155">Except for Generalized Additive Models (GAMs), tree models can lack explainability when the number of features is large.</span></span>

<span data-ttu-id="c6019-156">デシジョン ツリー アルゴリズムに消費されるリソースは多く、線形アルゴリズムほどスケールしません。</span><span class="sxs-lookup"><span data-stu-id="c6019-156">Decision tree algorithms take more resources and do not scale as well as linear ones do.</span></span> <span data-ttu-id="c6019-157">メモリに収まるようなデータセットに対しては適切に動作します。</span><span class="sxs-lookup"><span data-stu-id="c6019-157">They do perform well on datasets that can fit into memory.</span></span>

<span data-ttu-id="c6019-158">Boosted decision trees (ブースト デシジョン ツリー) は小さなツリーの集合であり、各ツリーで入力データにスコアが付けられ、そのスコアは次のツリーに渡され、より良いスコアが生成されるという処理が繰り返されます。集合内の各ツリーは、前のツリーに基づいて改善されます。</span><span class="sxs-lookup"><span data-stu-id="c6019-158">Boosted decision trees are an ensemble of small trees where each tree scores the input data and passes the score onto the next tree to produce a better score, and so on, where each tree in the ensemble improves on the previous.</span></span>

<span data-ttu-id="c6019-159">**デシジョン ツリー トレーナー**</span><span class="sxs-lookup"><span data-stu-id="c6019-159">**Decision tree trainers**</span></span>

|<span data-ttu-id="c6019-160">アルゴリズム</span><span class="sxs-lookup"><span data-stu-id="c6019-160">Algorithm</span></span>|<span data-ttu-id="c6019-161">プロパティ</span><span class="sxs-lookup"><span data-stu-id="c6019-161">Properties</span></span>|<span data-ttu-id="c6019-162">トレーナー</span><span class="sxs-lookup"><span data-stu-id="c6019-162">Trainers</span></span>|
|---------|----------|--------|
|<span data-ttu-id="c6019-163">Light gradient boosted machine (軽勾配ブースト マシン)</span><span class="sxs-lookup"><span data-stu-id="c6019-163">Light gradient boosted machine</span></span>|<span data-ttu-id="c6019-164">最も高速で最も正確な二項分類ツリー トレーナー。</span><span class="sxs-lookup"><span data-stu-id="c6019-164">Fastest and most accurate of the binary classification tree trainers.</span></span> <span data-ttu-id="c6019-165">高度に調整可能</span><span class="sxs-lookup"><span data-stu-id="c6019-165">Highly tunable</span></span>|<span data-ttu-id="c6019-166"><xref:Microsoft.ML.Trainers.LightGbm.LightGbmBinaryTrainer> <xref:Microsoft.ML.Trainers.LightGbm.LightGbmMulticlassTrainer> <xref:Microsoft.ML.Trainers.LightGbm.LightGbmRegressionTrainer> <xref:Microsoft.ML.Trainers.LightGbm.LightGbmRankingTrainer></span><span class="sxs-lookup"><span data-stu-id="c6019-166"><xref:Microsoft.ML.Trainers.LightGbm.LightGbmBinaryTrainer> <xref:Microsoft.ML.Trainers.LightGbm.LightGbmMulticlassTrainer> <xref:Microsoft.ML.Trainers.LightGbm.LightGbmRegressionTrainer> <xref:Microsoft.ML.Trainers.LightGbm.LightGbmRankingTrainer></span></span>|
|<span data-ttu-id="c6019-167">Fast tree (高速ツリー)</span><span class="sxs-lookup"><span data-stu-id="c6019-167">Fast tree</span></span>|<span data-ttu-id="c6019-168">特徴付けされた画像データに使用します。</span><span class="sxs-lookup"><span data-stu-id="c6019-168">Use for featurized image data.</span></span> <span data-ttu-id="c6019-169">不均衡なデータに対応できます。</span><span class="sxs-lookup"><span data-stu-id="c6019-169">Resilient to unbalanced data.</span></span> <span data-ttu-id="c6019-170">高度に調整可能</span><span class="sxs-lookup"><span data-stu-id="c6019-170">Highly tunable</span></span> | <span data-ttu-id="c6019-171"><xref:Microsoft.ML.Trainers.FastTree.FastTreeBinaryTrainer> <xref:Microsoft.ML.Trainers.FastTree.FastTreeRegressionTrainer> <xref:Microsoft.ML.Trainers.FastTree.FastTreeTweedieTrainer> <xref:Microsoft.ML.Trainers.FastTree.FastTreeRankingTrainer></span><span class="sxs-lookup"><span data-stu-id="c6019-171"><xref:Microsoft.ML.Trainers.FastTree.FastTreeBinaryTrainer> <xref:Microsoft.ML.Trainers.FastTree.FastTreeRegressionTrainer> <xref:Microsoft.ML.Trainers.FastTree.FastTreeTweedieTrainer> <xref:Microsoft.ML.Trainers.FastTree.FastTreeRankingTrainer></span></span>|
|<span data-ttu-id="c6019-172">Fast forest (高速フォレスト)</span><span class="sxs-lookup"><span data-stu-id="c6019-172">Fast forest</span></span>|<span data-ttu-id="c6019-173">ノイズの多いデータに適しています</span><span class="sxs-lookup"><span data-stu-id="c6019-173">Works well with noisy data</span></span>|<span data-ttu-id="c6019-174"><xref:Microsoft.ML.Trainers.FastTree.FastForestBinaryTrainer> <xref:Microsoft.ML.Trainers.FastTree.FastForestRegressionTrainer></span><span class="sxs-lookup"><span data-stu-id="c6019-174"><xref:Microsoft.ML.Trainers.FastTree.FastForestBinaryTrainer> <xref:Microsoft.ML.Trainers.FastTree.FastForestRegressionTrainer></span></span>|
|<span data-ttu-id="c6019-175">Generalized Additive Model (一般化加法モデル、GAM)</span><span class="sxs-lookup"><span data-stu-id="c6019-175">Generalized additive model (GAM)</span></span>|<span data-ttu-id="c6019-176">ツリー アルゴリズムに適した問題で、説明可能性が優先される場合に最適です</span><span class="sxs-lookup"><span data-stu-id="c6019-176">Best for problems that perform well with tree algorithms but where explainability is a priority</span></span>|<span data-ttu-id="c6019-177"><xref:Microsoft.ML.Trainers.FastTree.GamBinaryTrainer> <xref:Microsoft.ML.Trainers.FastTree.GamRegressionTrainer></span><span class="sxs-lookup"><span data-stu-id="c6019-177"><xref:Microsoft.ML.Trainers.FastTree.GamBinaryTrainer> <xref:Microsoft.ML.Trainers.FastTree.GamRegressionTrainer></span></span>|

## <a name="matrix-factorization"></a><span data-ttu-id="c6019-178">行列因子分解</span><span class="sxs-lookup"><span data-stu-id="c6019-178">Matrix factorization</span></span>

|<span data-ttu-id="c6019-179">プロパティ</span><span class="sxs-lookup"><span data-stu-id="c6019-179">Properties</span></span>|<span data-ttu-id="c6019-180">トレーナー</span><span class="sxs-lookup"><span data-stu-id="c6019-180">Trainers</span></span>|
|----------|--------|
|<span data-ttu-id="c6019-181">データセットが大規模で、カテゴリ別のスパース データに最適です</span><span class="sxs-lookup"><span data-stu-id="c6019-181">Best for sparse categorical data, with large datasets</span></span>|<xref:Microsoft.ML.Trainers.FieldAwareFactorizationMachineTrainer>|

## <a name="meta-algorithms"></a><span data-ttu-id="c6019-182">メタ アルゴリズム</span><span class="sxs-lookup"><span data-stu-id="c6019-182">Meta algorithms</span></span>

<span data-ttu-id="c6019-183">これらのトレーナーでは、二項トレーナーから多クラストレーナーを作成します。</span><span class="sxs-lookup"><span data-stu-id="c6019-183">These trainers create a multi-class trainer from a binary trainer.</span></span> <span data-ttu-id="c6019-184"><xref:Microsoft.ML.Trainers.AveragedPerceptronTrainer>、<xref:Microsoft.ML.Trainers.LbfgsLogisticRegressionBinaryTrainer>、<xref:Microsoft.ML.Trainers.SymbolicSgdLogisticRegressionBinaryTrainer>、<xref:Microsoft.ML.Trainers.LightGbm.LightGbmBinaryTrainer>、<xref:Microsoft.ML.Trainers.FastTree.FastTreeBinaryTrainer>、<xref:Microsoft.ML.Trainers.FastTree.FastForestBinaryTrainer>、<xref:Microsoft.ML.Trainers.FastTree.GamBinaryTrainer> と共に使用します。</span><span class="sxs-lookup"><span data-stu-id="c6019-184">Use with <xref:Microsoft.ML.Trainers.AveragedPerceptronTrainer>, <xref:Microsoft.ML.Trainers.LbfgsLogisticRegressionBinaryTrainer>, <xref:Microsoft.ML.Trainers.SymbolicSgdLogisticRegressionBinaryTrainer>, <xref:Microsoft.ML.Trainers.LightGbm.LightGbmBinaryTrainer>, <xref:Microsoft.ML.Trainers.FastTree.FastTreeBinaryTrainer>, <xref:Microsoft.ML.Trainers.FastTree.FastForestBinaryTrainer>, <xref:Microsoft.ML.Trainers.FastTree.GamBinaryTrainer>.</span></span>

|<span data-ttu-id="c6019-185">アルゴリズム</span><span class="sxs-lookup"><span data-stu-id="c6019-185">Algorithm</span></span>|<span data-ttu-id="c6019-186">プロパティ</span><span class="sxs-lookup"><span data-stu-id="c6019-186">Properties</span></span>|<span data-ttu-id="c6019-187">トレーナー</span><span class="sxs-lookup"><span data-stu-id="c6019-187">Trainers</span></span>|
|---------|----------|--------|
|<span data-ttu-id="c6019-188">One versus all (1 対すべて)</span><span class="sxs-lookup"><span data-stu-id="c6019-188">One versus all</span></span>|<span data-ttu-id="c6019-189">この多クラス分類子では、クラスごとに 1 つの二項分類子がトレーニングされます。これにより、そのクラスは他のすべてのクラスと区別されます。</span><span class="sxs-lookup"><span data-stu-id="c6019-189">This multiclass classifier trains one binary classifier for each class, which distinguishes that class from all other classes.</span></span> <span data-ttu-id="c6019-190">分類するクラスの数によってスケールが制限されます</span><span class="sxs-lookup"><span data-stu-id="c6019-190">Is limited in scale by the number of classes to categorize</span></span>|[<span data-ttu-id="c6019-191">OneVersusAllTrainer\<BinaryClassificationTrainer></span><span class="sxs-lookup"><span data-stu-id="c6019-191">OneVersusAllTrainer\<BinaryClassificationTrainer></span></span>](xref:Microsoft.ML.Trainers.OneVersusAllTrainer) |
|<span data-ttu-id="c6019-192">Pairwise coupling (ペアワイズ結合)</span><span class="sxs-lookup"><span data-stu-id="c6019-192">Pairwise coupling</span></span>|<span data-ttu-id="c6019-193">この多クラス分類子では、クラスの各ペアに対して二項分類アルゴリズムがトレーニングされます。</span><span class="sxs-lookup"><span data-stu-id="c6019-193">This multiclass classifier trains a binary classification algorithm on each pair of classes.</span></span> <span data-ttu-id="c6019-194">2 つのクラスの各組み合わせをトレーニングする必要があるので、クラスの数によってスケールが制限されます。</span><span class="sxs-lookup"><span data-stu-id="c6019-194">Is limited in scale by the number of classes, as each combination of two classes must be trained.</span></span>|[<span data-ttu-id="c6019-195">PairwiseCouplingTrainer\<BinaryClassificationTrainer></span><span class="sxs-lookup"><span data-stu-id="c6019-195">PairwiseCouplingTrainer\<BinaryClassificationTrainer></span></span>](xref:Microsoft.ML.Trainers.PairwiseCouplingTrainer)|

## <a name="k-means"></a><span data-ttu-id="c6019-196">K-Means</span><span class="sxs-lookup"><span data-stu-id="c6019-196">K-Means</span></span>

|<span data-ttu-id="c6019-197">プロパティ</span><span class="sxs-lookup"><span data-stu-id="c6019-197">Properties</span></span>|<span data-ttu-id="c6019-198">トレーナー</span><span class="sxs-lookup"><span data-stu-id="c6019-198">Trainers</span></span>|
|----------|--------|
|<span data-ttu-id="c6019-199">クラスタリングに使用します</span><span class="sxs-lookup"><span data-stu-id="c6019-199">Use for clustering</span></span>|<xref:Microsoft.ML.Trainers.KMeansTrainer>|

## <a name="principal-component-analysis"></a><span data-ttu-id="c6019-200">主成分分析</span><span class="sxs-lookup"><span data-stu-id="c6019-200">Principal component analysis</span></span>

|<span data-ttu-id="c6019-201">プロパティ</span><span class="sxs-lookup"><span data-stu-id="c6019-201">Properties</span></span>|<span data-ttu-id="c6019-202">トレーナー</span><span class="sxs-lookup"><span data-stu-id="c6019-202">Trainers</span></span>|
|----------|--------|
|<span data-ttu-id="c6019-203">異常検出に使用されます</span><span class="sxs-lookup"><span data-stu-id="c6019-203">Use for anomaly detection</span></span>|<xref:Microsoft.ML.Trainers.RandomizedPcaTrainer>|

## <a name="naive-bayes"></a><span data-ttu-id="c6019-204">ナイーブ ベイズ</span><span class="sxs-lookup"><span data-stu-id="c6019-204">Naive Bayes</span></span>

|<span data-ttu-id="c6019-205">プロパティ</span><span class="sxs-lookup"><span data-stu-id="c6019-205">Properties</span></span>|<span data-ttu-id="c6019-206">トレーナー</span><span class="sxs-lookup"><span data-stu-id="c6019-206">Trainers</span></span>|
|----------|--------|
|<span data-ttu-id="c6019-207">特徴が独立しており、トレーニング データセットが小さい場合は、この多クラス分類トレーナーを使用します。</span><span class="sxs-lookup"><span data-stu-id="c6019-207">Use this multi-class classification trainer when the features are independent, and the training dataset is small.</span></span>|<xref:Microsoft.ML.Trainers.NaiveBayesMulticlassTrainer>|

## <a name="prior-trainer"></a><span data-ttu-id="c6019-208">以前のトレーナー</span><span class="sxs-lookup"><span data-stu-id="c6019-208">Prior Trainer</span></span>

|<span data-ttu-id="c6019-209">プロパティ</span><span class="sxs-lookup"><span data-stu-id="c6019-209">Properties</span></span>|<span data-ttu-id="c6019-210">トレーナー</span><span class="sxs-lookup"><span data-stu-id="c6019-210">Trainers</span></span>|
|----------|--------|
|<span data-ttu-id="c6019-211">他のトレーナーのパフォーマンスを基準にするには、この二項分類トレーナーを使用します。</span><span class="sxs-lookup"><span data-stu-id="c6019-211">Use this binary classification trainer to baseline the performance of other trainers.</span></span> <span data-ttu-id="c6019-212">他のトレーナーのメトリックは以前のトレーナーよりも効果の点で優れています。</span><span class="sxs-lookup"><span data-stu-id="c6019-212">To be effective, the metrics of the other trainers should be better than the prior trainer.</span></span> |<xref:Microsoft.ML.Trainers.PriorTrainer>|