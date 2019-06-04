---
title: ML.NET のメトリック
description: ML.NET モデルのパフォーマンスを評価するために使用されるメトリックを理解する
ms.date: 04/29/2019
author: ''
ms.openlocfilehash: 802f0a8fd32c492c8d9f89933b183802cb178cb3
ms.sourcegitcommit: 7e129d879ddb42a8b4334eee35727afe3d437952
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66053043"
---
# <a name="model-evaluation-metrics-in-mlnet"></a><span data-ttu-id="937d8-103">ML.NET のモデル評価メトリック</span><span class="sxs-lookup"><span data-stu-id="937d8-103">Model evaluation metrics in ML.NET</span></span>

## <a name="metrics-for-binary-classification"></a><span data-ttu-id="937d8-104">二項分類のメトリック</span><span class="sxs-lookup"><span data-stu-id="937d8-104">Metrics for Binary Classification</span></span>

| <span data-ttu-id="937d8-105">メトリック</span><span class="sxs-lookup"><span data-stu-id="937d8-105">Metrics</span></span>   |      <span data-ttu-id="937d8-106">説明</span><span class="sxs-lookup"><span data-stu-id="937d8-106">Description</span></span>      |  <span data-ttu-id="937d8-107">求めるもの</span><span class="sxs-lookup"><span data-stu-id="937d8-107">Look for</span></span> |
|-----------|-----------------------|-----------|
| <span data-ttu-id="937d8-108">**正確度**</span><span class="sxs-lookup"><span data-stu-id="937d8-108">**Accuracy**</span></span> |  <span data-ttu-id="937d8-109">[正確度](https://en.wikipedia.org/wiki/Accuracy_and_precision#In_binary_classification)は、テスト データ セットでの正しい予測の割合です。</span><span class="sxs-lookup"><span data-stu-id="937d8-109">[Accuracy](https://en.wikipedia.org/wiki/Accuracy_and_precision#In_binary_classification) is the proportion of correct predictions with a test data set.</span></span> <span data-ttu-id="937d8-110">これは、入力サンプルの総数に対する正しい予測数の比率です。</span><span class="sxs-lookup"><span data-stu-id="937d8-110">It is the ratio of number of correct predictions to the total number of input samples.</span></span> <span data-ttu-id="937d8-111">各クラスに属するサンプルの数が同様の場合にのみ適しています。</span><span class="sxs-lookup"><span data-stu-id="937d8-111">It works well only if there are similar number of samples belonging to each class.</span></span>| <span data-ttu-id="937d8-112">**1.00 に近いほど優れています**。</span><span class="sxs-lookup"><span data-stu-id="937d8-112">**The closer to 1.00, the better**.</span></span> <span data-ttu-id="937d8-113">ただし、ぴったりの 1.00 は問題を示しています (通常: ラベル/ターゲットの漏えい、過剰適合、またはトレーニング データによるテスト)。</span><span class="sxs-lookup"><span data-stu-id="937d8-113">But exactly 1.00 indicates an issue (commonly: label/target leakage, over-fitting, or testing with training data).</span></span> <span data-ttu-id="937d8-114">テスト データのバランスが取れていない場合 (ほとんどのインスタンスがいずれかのクラスに属している場合)、データセットが非常に小さいか、スコアが 0.00 または 1.00 に近く、正確度では実際に分類子の有効性がキャプチャされないので、追加のメトリックを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="937d8-114">When the test data is unbalanced (where most of the instances belong to one of the classes), the dataset is very small, or scores approach 0.00 or 1.00, then accuracy doesn’t really capture the effectiveness of a classifier and you need to check additional metrics.</span></span> |
| <span data-ttu-id="937d8-115">**AUC**</span><span class="sxs-lookup"><span data-stu-id="937d8-115">**AUC**</span></span> |    <span data-ttu-id="937d8-116">[aucROC](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) または "*曲線下面積*":これは、真陽性率と偽陽性率のスイープによって作成される曲線下面積を測定する処理です。</span><span class="sxs-lookup"><span data-stu-id="937d8-116">[aucROC](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) or *Area under the curve*: This is measuring the area under the curve created by sweeping the true positive rate vs. the false positive rate.</span></span>  |   <span data-ttu-id="937d8-117">**1.00 に近いほど優れています**。</span><span class="sxs-lookup"><span data-stu-id="937d8-117">**The closer to 1.00, the better**.</span></span> <span data-ttu-id="937d8-118">モデルが許容されるには、0.50 より大きい必要があります。AUC が 0.50 以下のモデルは役に立ちません。</span><span class="sxs-lookup"><span data-stu-id="937d8-118">It should be greater than 0.50 for a model to be acceptable; a model with AUC of 0.50 or less is worthless.</span></span> |
| <span data-ttu-id="937d8-119">**AUCPR**</span><span class="sxs-lookup"><span data-stu-id="937d8-119">**AUCPR**</span></span> | <span data-ttu-id="937d8-120">[aucPR](https://www.coursera.org/lecture/ml-classification/precision-recall-curve-rENu8) または "*精度 - 再現率曲線の曲線下面積*":クラスが非常に不均衡な (非常に偏ったデータセット) 場合の予測の成功の役に立つ測定。</span><span class="sxs-lookup"><span data-stu-id="937d8-120">[aucPR](https://www.coursera.org/lecture/ml-classification/precision-recall-curve-rENu8) or *Area under the curve of a Precision-Recall curve*: Useful measure of success of prediction when the classes are very imbalanced (highly skewed datasets).</span></span> |  <span data-ttu-id="937d8-121">**1.00 に近いほど優れています**。</span><span class="sxs-lookup"><span data-stu-id="937d8-121">**The closer to 1.00, the better**.</span></span> <span data-ttu-id="937d8-122">1.00 に近い高スコアは、分類子が正確な結果を返していること (高精度) と、すべての肯定的な結果の大部分も返していること (高い再現率) を示します。</span><span class="sxs-lookup"><span data-stu-id="937d8-122">High scores close to 1.00 show that the classifier is returning accurate results (high precision), as well as returning a majority of all positive results (high recall).</span></span> |
| <span data-ttu-id="937d8-123">**F1 スコア**</span><span class="sxs-lookup"><span data-stu-id="937d8-123">**F1-score**</span></span> | <span data-ttu-id="937d8-124">[F1 スコア](https://en.wikipedia.org/wiki/F1_score)は "*バランスの取れた F スコアまたは F メジャー*" とも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="937d8-124">[F1 score](https://en.wikipedia.org/wiki/F1_score) also known as *balanced F-score or F-measure*.</span></span> <span data-ttu-id="937d8-125">これは精度と再現率の調和平均です。</span><span class="sxs-lookup"><span data-stu-id="937d8-125">It's the harmonic mean of the precision and recall.</span></span> <span data-ttu-id="937d8-126">F1 スコアは、精度と再現率のバランスを取る場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="937d8-126">F1 Score is helpful when you want to seek a balance between Precision and Recall.</span></span>| <span data-ttu-id="937d8-127">**1.00 に近いほど優れています**。</span><span class="sxs-lookup"><span data-stu-id="937d8-127">**The closer to 1.00, the better**.</span></span>  <span data-ttu-id="937d8-128">F1 スコアは 1.00 で最高値に到達し、0.00 で最低スコアに到達します。</span><span class="sxs-lookup"><span data-stu-id="937d8-128">An F1 score reaches its best value at 1.00 and worst score at 0.00.</span></span> <span data-ttu-id="937d8-129">分類子の精度がわかります。</span><span class="sxs-lookup"><span data-stu-id="937d8-129">It tells you how precise your classifier is.</span></span> |

<span data-ttu-id="937d8-130">二項分類メトリックの詳細については、以下の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="937d8-130">For further details on binary classification metrics read the following articles:</span></span>

- [<span data-ttu-id="937d8-131">正確度、精度、再現率、または F1?</span><span class="sxs-lookup"><span data-stu-id="937d8-131">Accuracy, Precision, Recall or F1?</span></span>](https://towardsdatascience.com/accuracy-precision-recall-or-f1-331fb37c5cb9)
- [<span data-ttu-id="937d8-132">BinaryClassificationMetrics クラス</span><span class="sxs-lookup"><span data-stu-id="937d8-132">Binary Classification Metrics class</span></span>](xref:Microsoft.ML.Data.BinaryClassificationMetrics)
- [<span data-ttu-id="937d8-133">精度 - 再現率曲線と ROC 曲線の関係</span><span class="sxs-lookup"><span data-stu-id="937d8-133">The Relationship Between Precision-Recall and ROC Curves</span></span>](http://pages.cs.wisc.edu/~jdavis/davisgoadrichcamera2.pdf)

## <a name="metrics-for-multi-class-classification"></a><span data-ttu-id="937d8-134">多クラス分類のメトリック</span><span class="sxs-lookup"><span data-stu-id="937d8-134">Metrics for Multi-class Classification</span></span>

| <span data-ttu-id="937d8-135">メトリック</span><span class="sxs-lookup"><span data-stu-id="937d8-135">Metrics</span></span>   |      <span data-ttu-id="937d8-136">説明</span><span class="sxs-lookup"><span data-stu-id="937d8-136">Description</span></span>      |  <span data-ttu-id="937d8-137">求めるもの</span><span class="sxs-lookup"><span data-stu-id="937d8-137">Look for</span></span> |
|-----------|-----------------------|-----------|
| <span data-ttu-id="937d8-138">**マイクロ正確度**</span><span class="sxs-lookup"><span data-stu-id="937d8-138">**Micro-Accuracy**</span></span> |  <span data-ttu-id="937d8-139">[マイクロ平均正確度](xref:Microsoft.ML.Data.MulticlassClassificationMetrics.MicroAccuracy)では、すべてのクラスのコントリビューションを集計して平均メトリックが計算されます。</span><span class="sxs-lookup"><span data-stu-id="937d8-139">[Micro-average Accuracy](xref:Microsoft.ML.Data.MulticlassClassificationMetrics.MicroAccuracy) aggregates the contributions of all classes to compute the average metric.</span></span> <span data-ttu-id="937d8-140">正しく予測されたインスタンスの割合です。</span><span class="sxs-lookup"><span data-stu-id="937d8-140">It is the fraction of instances predicted correctly.</span></span> <span data-ttu-id="937d8-141">マイクロ平均には、クラスのメンバーシップが考慮されません。</span><span class="sxs-lookup"><span data-stu-id="937d8-141">The micro-average does not take class membership into account.</span></span> <span data-ttu-id="937d8-142">基本的に、すべてのサンプルとクラスのペアが、精度メトリックに均等に作用します。</span><span class="sxs-lookup"><span data-stu-id="937d8-142">Basically, every sample-class pair contributes equally to the accuracy metric.</span></span> | <span data-ttu-id="937d8-143">**1.00 に近いほど優れています**。</span><span class="sxs-lookup"><span data-stu-id="937d8-143">**The closer to 1.00, the better**.</span></span> <span data-ttu-id="937d8-144">多クラス分類タスクで、マイクロ正確度の方がマクロ正確度よりも適しているのは、クラスの不均衡があると思われる場合です (言い換えると、</span><span class="sxs-lookup"><span data-stu-id="937d8-144">In a multi-class classification task, micro-accuracy is preferable over macro-accuracy if you suspect there might be class imbalance (i.e</span></span> <span data-ttu-id="937d8-145">あるクラスが他のクラスよりも例の数が多い場合です)。</span><span class="sxs-lookup"><span data-stu-id="937d8-145">you may have many more examples of one class than of other classes).</span></span>|
| <span data-ttu-id="937d8-146">**マクロ正確度**</span><span class="sxs-lookup"><span data-stu-id="937d8-146">**Macro-Accuracy**</span></span> | <span data-ttu-id="937d8-147">[マクロ平均正確度](xref:Microsoft.ML.Data.MulticlassClassificationMetrics.MacroAccuracy)はクラス レベルの平均正確度です。</span><span class="sxs-lookup"><span data-stu-id="937d8-147">[Macro-average Accuracy](xref:Microsoft.ML.Data.MulticlassClassificationMetrics.MacroAccuracy) is the average accuracy at the class level.</span></span> <span data-ttu-id="937d8-148">各クラスの正確度が計算され、マクロ正確度はこれらの正確度の平均です。</span><span class="sxs-lookup"><span data-stu-id="937d8-148">The accuracy for each class is computed and the macro-accuracy is the average of these accuracies.</span></span> <span data-ttu-id="937d8-149">基本的に、すべてのクラスが、精度メトリックに均等に作用します。</span><span class="sxs-lookup"><span data-stu-id="937d8-149">Basically, every class contributes equally to the accuracy metric.</span></span> <span data-ttu-id="937d8-150">少数派のクラスは、大規模なクラスと同じ重みが与えられています。</span><span class="sxs-lookup"><span data-stu-id="937d8-150">Minority classes are given equal weight as the larger classes.</span></span> <span data-ttu-id="937d8-151">マクロ平均メトリックでは、データセットにそのクラスのインスタンスがいくつ含まれていても、各クラスに同じ重みが与えられます。</span><span class="sxs-lookup"><span data-stu-id="937d8-151">The macro-average metric gives the same weight to each class, no matter how many instances from that class the dataset contains.</span></span> |  <span data-ttu-id="937d8-152">**1.00 に近いほど優れています**。</span><span class="sxs-lookup"><span data-stu-id="937d8-152">**The closer to 1.00, the better**.</span></span>  <span data-ttu-id="937d8-153">クラスごとに独立してメトリックを計算してから、平均を受け取ります (そのため、すべてのクラスを平等に扱います)。</span><span class="sxs-lookup"><span data-stu-id="937d8-153">It computes the metric independently for each class and then takes the average (hence treating all classes equally)</span></span> |
| <span data-ttu-id="937d8-154">**対数損失**</span><span class="sxs-lookup"><span data-stu-id="937d8-154">**Log-loss**</span></span>| <span data-ttu-id="937d8-155">[対数損失](http://wiki.fast.ai/index.php/Log_Loss)では、予測入力が 0.00 と 1.00 の間の確率値である分類モデルのパフォーマンスを測定します。</span><span class="sxs-lookup"><span data-stu-id="937d8-155">[Logarithmic loss](http://wiki.fast.ai/index.php/Log_Loss) measures the performance of a classification model where the prediction input is a probability value between 0.00 and 1.00.</span></span> <span data-ttu-id="937d8-156">予測される確率が実際のラベルから分岐すると、対数損失が増加します。</span><span class="sxs-lookup"><span data-stu-id="937d8-156">Log-loss increases as the predicted probability diverges from the actual label.</span></span> | <span data-ttu-id="937d8-157">**0.00 に近いほど、優れています**。</span><span class="sxs-lookup"><span data-stu-id="937d8-157">**The closer to 0.00, the better**.</span></span> <span data-ttu-id="937d8-158">完璧なモデルの対数損失は 0.00 になります。</span><span class="sxs-lookup"><span data-stu-id="937d8-158">A perfect model would have a log-loss of 0.00.</span></span> <span data-ttu-id="937d8-159">機械学習モデルの目的はこの値を最小にすることです。</span><span class="sxs-lookup"><span data-stu-id="937d8-159">The goal of our machine learning models is to minimize this value.</span></span>|
| <span data-ttu-id="937d8-160">**対数損失の減少**</span><span class="sxs-lookup"><span data-stu-id="937d8-160">**Log-Loss Reduction**</span></span> | <span data-ttu-id="937d8-161">[対数損失の減少](xref:Microsoft.ML.Data.MulticlassClassificationMetrics.LogLossReduction)は、ランダム予測よりも優れている分類子の利点と解釈できます。</span><span class="sxs-lookup"><span data-stu-id="937d8-161">[Logarithmic loss reduction](xref:Microsoft.ML.Data.MulticlassClassificationMetrics.LogLossReduction) can be interpreted as the advantage of the classifier over a random prediction.</span></span>| <span data-ttu-id="937d8-162">**範囲は -inf から 1.00 です。ここで、1.00 は完璧な予測であり、0.00 は平均の予測を示します**。</span><span class="sxs-lookup"><span data-stu-id="937d8-162">**Ranges from -inf and 1.00, where 1.00 is perfect predictions and 0.00 indicates mean predictions**.</span></span> <span data-ttu-id="937d8-163">たとえば、値が 0.20 の場合、"正しい予測の確率は、ランダムな推測よりも 20% 優れている" と解釈できます。</span><span class="sxs-lookup"><span data-stu-id="937d8-163">For example, if the value equals 0.20, it can be interpreted as "the probability of a correct prediction is 20% better than random guessing"</span></span>|

<span data-ttu-id="937d8-164">マイクロ正確度は、一般に、ML 予測のビジネス ニーズにより適しています。</span><span class="sxs-lookup"><span data-stu-id="937d8-164">Micro-accuracy is generally better aligned with the business needs of ML predictions.</span></span> <span data-ttu-id="937d8-165">多クラス分類タスクの品質を選択するために 1 つのメトリックを選択する場合は、通常、それはマイクロ正確度です。</span><span class="sxs-lookup"><span data-stu-id="937d8-165">If you want to select a single metric for choosing the quality of a multiclass classification task, it should usually be micro-accuracy.</span></span>

<span data-ttu-id="937d8-166">サポート チケット分類タスクの例: (受け取るチケットをサポート チームに割り当てる)</span><span class="sxs-lookup"><span data-stu-id="937d8-166">Example, for a support ticket classification task: (maps incoming tickets to support teams)</span></span>

- <span data-ttu-id="937d8-167">マイクロ正確度 -- 受け取るチケットが適切なチームに分類される頻度はどのくらいですか。</span><span class="sxs-lookup"><span data-stu-id="937d8-167">Micro-accuracy -- how often does an incoming ticket get classified to the right team?</span></span>
- <span data-ttu-id="937d8-168">マクロ正確度 -- 平均的なチームの場合、受け取るチケットがそのチームにとって正しい頻度はどのくらいですか。</span><span class="sxs-lookup"><span data-stu-id="937d8-168">Macro-accuracy -- for an average team, how often is an incoming ticket correct for their team?</span></span>

<span data-ttu-id="937d8-169">この例で、マクロ正確度は、小規模なチームには重すぎます。年間 10 枚のチケットのみを取得する小規模なチームが、チケットが年間 10,000 枚の大規模なチームと重要度が同じになります。</span><span class="sxs-lookup"><span data-stu-id="937d8-169">Macro-accuracy overweights small teams in this example; a small team which gets only 10 tickets per year counts as much as a large team with 10k tickets per year.</span></span> <span data-ttu-id="937d8-170">このケースのマイクロ正確度は、"チケット ルーティング プロセスを自動化することで、会社がどれくらいの時間/お金を節約できるか" というビジネス ニーズとより相関しています。</span><span class="sxs-lookup"><span data-stu-id="937d8-170">Micro-accuracy in this case correlates better with the business need of, "how much time/money can the company save by automating my ticket routing process".</span></span>

<span data-ttu-id="937d8-171">多クラス分類メトリックの詳細については、以下の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="937d8-171">For further details on multi-class classification metrics read the following articles:</span></span>

- [<span data-ttu-id="937d8-172">精度、再現率、および F スコアのマイクロ平均とマクロ平均</span><span class="sxs-lookup"><span data-stu-id="937d8-172">Micro- and Macro-average of Precision, Recall and F-Score</span></span>](http://rushdishams.blogspot.com/2011/08/micro-and-macro-average-of-precision.html)
- [<span data-ttu-id="937d8-173">不均衡なデータセットによる多クラス分類</span><span class="sxs-lookup"><span data-stu-id="937d8-173">Multiclass Classification with Imbalanced Dataset</span></span>](https://towardsdatascience.com/machine-learning-multiclass-classification-with-imbalanced-data-set-29f6a177c1a)

## <a name="metrics-for-regression"></a><span data-ttu-id="937d8-174">回帰のメトリック</span><span class="sxs-lookup"><span data-stu-id="937d8-174">Metrics for Regression</span></span>

| <span data-ttu-id="937d8-175">メトリック</span><span class="sxs-lookup"><span data-stu-id="937d8-175">Metrics</span></span>   |      <span data-ttu-id="937d8-176">説明</span><span class="sxs-lookup"><span data-stu-id="937d8-176">Description</span></span>      |  <span data-ttu-id="937d8-177">求めるもの</span><span class="sxs-lookup"><span data-stu-id="937d8-177">Look for</span></span> |
|-----------|-----------------------|-----------|
| <span data-ttu-id="937d8-178">**R-2 乗**</span><span class="sxs-lookup"><span data-stu-id="937d8-178">**R-Squared**</span></span> |  <span data-ttu-id="937d8-179">[R-2 乗 (R2)](https://en.wikipedia.org/wiki/Coefficient_of_determination)、または "*決定係数*" では、モデルの予測係数が -inf から 1.00 の間の値として表されます。</span><span class="sxs-lookup"><span data-stu-id="937d8-179">[R-squared (R2)](https://en.wikipedia.org/wiki/Coefficient_of_determination), or *Coefficient of determination* represents the predictive power of the model as a value between -inf and 1.00.</span></span> <span data-ttu-id="937d8-180">1.00 は完璧な適合があることを意味します。適合は任意に低くなる可能性があるのでスコアはマイナスになることがあります。</span><span class="sxs-lookup"><span data-stu-id="937d8-180">1.00 means there is a perfect fit, and the fit can be arbitrarly poor so the scores can be negative.</span></span> <span data-ttu-id="937d8-181">0.00 のスコアは、モデルがラベルの期待値を推測していることを意味します。</span><span class="sxs-lookup"><span data-stu-id="937d8-181">A score of 0.00 means the model is guessing the expected value for the label.</span></span> <span data-ttu-id="937d8-182">R2 では、実際のテスト データ値が予測値にどのくらい近いかを測定します。</span><span class="sxs-lookup"><span data-stu-id="937d8-182">R2 measures how close the actual test data values are to the predicted values.</span></span> | <span data-ttu-id="937d8-183">**1.00 に近いほど、高品質です**。</span><span class="sxs-lookup"><span data-stu-id="937d8-183">**The closer to 1.00, the better quality**.</span></span> <span data-ttu-id="937d8-184">ただし、低い R-2 乗値 (0.50 など) がシナリオにとって完全に正常または十分である場合があり、高い R-2 乗値が常に優れているとは限らず、疑わしい場合もあります。</span><span class="sxs-lookup"><span data-stu-id="937d8-184">However, sometimes low R-squared values (such as 0.50) can be entirely normal or good enough for your scenario and high R-squared values are not always good and be suspicious.</span></span> |
| <span data-ttu-id="937d8-185">**絶対損失**</span><span class="sxs-lookup"><span data-stu-id="937d8-185">**Absolute-loss**</span></span> |  <span data-ttu-id="937d8-186">[絶対損失](https://en.wikipedia.org/wiki/Mean_absolute_error)または "*平均絶対誤差 (MAE)* " では、予測が実際の結果にどのくらい近いかを測定します。</span><span class="sxs-lookup"><span data-stu-id="937d8-186">[Absolute-loss](https://en.wikipedia.org/wiki/Mean_absolute_error) or *Mean absolute error (MAE)* measures how close the predictions are to the actual outcomes.</span></span> <span data-ttu-id="937d8-187">これはすべてのモデルの誤差の平均です。モデルの誤差とは、予測されたラベル値と正確なラベル値の間の絶対的な距離です。</span><span class="sxs-lookup"><span data-stu-id="937d8-187">It is the average of all the model errors, where model error is the absolute distance between the predicted label value and the correct label value.</span></span> <span data-ttu-id="937d8-188">この予測の誤差は、テスト データ セットの各レコードに対して計算されます。</span><span class="sxs-lookup"><span data-stu-id="937d8-188">This prediction error is calculated for each record of the test data set.</span></span> <span data-ttu-id="937d8-189">最後に、記録されたすべての絶対誤差について平均値が計算されます。</span><span class="sxs-lookup"><span data-stu-id="937d8-189">Finally, the mean value is calculated for all recorded absolute errors.</span></span>| <span data-ttu-id="937d8-190">**0.00 に近いほど、高品質です。**</span><span class="sxs-lookup"><span data-stu-id="937d8-190">**The closer to 0.00, the better quality.**</span></span> <span data-ttu-id="937d8-191">平均絶対誤差には、測定されるデータと同じスケールが使用される点に注意してください (特定の範囲に正規化されません)。</span><span class="sxs-lookup"><span data-stu-id="937d8-191">Note that the mean absolute error uses the same scale as the data being measured (is not normalized to specific range).</span></span> <span data-ttu-id="937d8-192">絶対損失、2 乗損失、および RMS 損失は、同じデータセット、またはラベル値の分布が同様のデータセットのモデル間の比較にのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="937d8-192">Absolute-loss, Squared-loss, and RMS-loss can only be used to make comparisons between models for the same dataset or dataset with a smilar label value distribution.</span></span> |
| <span data-ttu-id="937d8-193">**2 乗損失**</span><span class="sxs-lookup"><span data-stu-id="937d8-193">**Squared-loss**</span></span> |  <span data-ttu-id="937d8-194">[2 乗損失](https://en.wikipedia.org/wiki/Mean_squared_error)または*平均 2 乗誤差 (MSE)* (*平均 2 乗偏差 (MSD)* とも呼ばれます) で、回帰直線が一連のテスト データ値にどのくらい近いかがわかります。</span><span class="sxs-lookup"><span data-stu-id="937d8-194">[Squared-loss](https://en.wikipedia.org/wiki/Mean_squared_error) or *Mean Squared Error (MSE)*, also called *Mean Squared Deviation (MSD)*, tells you how close a regression line is to a set of test data values.</span></span> <span data-ttu-id="937d8-195">これを行うには、点から回帰直線までの距離 (これらの距離が誤差 E です) を受け取り、2 乗します。</span><span class="sxs-lookup"><span data-stu-id="937d8-195">It does this by taking the distances from the points to the regression line (these distances are the errors E) and squaring them.</span></span> <span data-ttu-id="937d8-196">2 乗すると、差が大きいほど、重みが大きくなります。</span><span class="sxs-lookup"><span data-stu-id="937d8-196">The squaring gives more weight to larger differences.</span></span> | <span data-ttu-id="937d8-197">これは常に負以外であり、**値が 0.00 に近いほど優れています**。</span><span class="sxs-lookup"><span data-stu-id="937d8-197">It is always non-negative, and **values closer to 0.00 are better**.</span></span> <span data-ttu-id="937d8-198">データによっては、平均 2 乗誤差の値を非常に小さくすることができない場合があります。</span><span class="sxs-lookup"><span data-stu-id="937d8-198">Depending on your data, it may be impossible to get a very small value for the mean squared error.</span></span>|
| <span data-ttu-id="937d8-199">**RMS 損失**</span><span class="sxs-lookup"><span data-stu-id="937d8-199">**RMS-loss**</span></span> |  <span data-ttu-id="937d8-200">[RMS 損失](https://en.wikipedia.org/wiki/Root-mean-square_deviation)または "*平均 2 乗誤差平方根 (RMSE)* " ("*平均 2 乗偏差平方根 (RMSD)* " とも呼ばれます) では、モデルによって予測された値と、モデル化されている環境から実際に観察された値の差を測定します。</span><span class="sxs-lookup"><span data-stu-id="937d8-200">[RMS-loss](https://en.wikipedia.org/wiki/Root-mean-square_deviation) or *Root Mean Squared Error (RMSE)* (also called *Root Mean Square Deviation, RMSD*), measures the difference between values predicted by a model and the values actually observed from the environment that is being modeled.</span></span> <span data-ttu-id="937d8-201">RMS 損失は 2 乗損失の平方根であり、ラベルと単位が同じです。絶対損失と似ていますが、差が大きいほど、重みが大きくなります。</span><span class="sxs-lookup"><span data-stu-id="937d8-201">RMS-loss is the square root of Squared-loss and has the same units as the label, similar to the abolute-loss though giving more weight to larger diferences.</span></span> <span data-ttu-id="937d8-202">平均 2 乗誤差平方根は、一般的に、気候学、予測、および回帰分析で実験結果を検証するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="937d8-202">Root mean square error is commonly used in climatology, forecasting, and regression analysis to verify experimental results.</span></span> | <span data-ttu-id="937d8-203">これは常に負以外であり、**値が 0.00 に近いほど優れています**。</span><span class="sxs-lookup"><span data-stu-id="937d8-203">It is always non-negative, and **values closer to 0.00 are better**.</span></span> <span data-ttu-id="937d8-204">RMSD は、スケールに依存するため、データセット間ではなく、特定のデータセットに対する異なるモデルの予測誤差を比較するための正確度の測定です。</span><span class="sxs-lookup"><span data-stu-id="937d8-204">RMSD is a measure of accuracy, to compare forecasting errors of different models for a particular dataset and not between datasets, as it is scale-dependent.</span></span>|

<span data-ttu-id="937d8-205">回帰メトリックの詳細については、以下の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="937d8-205">For further details on regression metrics, read the following articles:</span></span>

- [<span data-ttu-id="937d8-206">回帰分析:R-2 乗を解釈して適合度を評価する方法</span><span class="sxs-lookup"><span data-stu-id="937d8-206">Regression Analysis: How Do I Interpret R-squared and Assess the Goodness-of-Fit?</span></span>](https://blog.minitab.com/blog/adventures-in-statistics-2/regression-analysis-how-do-i-interpret-r-squared-and-assess-the-goodness-of-fit)
- [<span data-ttu-id="937d8-207">回帰分析で R-2 乗を解釈する方法</span><span class="sxs-lookup"><span data-stu-id="937d8-207">How To Interpret R-squared in Regression Analysis</span></span>](https://statisticsbyjim.com/regression/interpret-r-squared-regression)
- [<span data-ttu-id="937d8-208">R-2 乗の定義</span><span class="sxs-lookup"><span data-stu-id="937d8-208">R-Squared Definition</span></span>](https://www.investopedia.com/terms/r/r-squared.asp)
- [<span data-ttu-id="937d8-209">平均 2 乗誤差の定義</span><span class="sxs-lookup"><span data-stu-id="937d8-209">Mean Squared Error Definition</span></span>](https://www.statisticshowto.datasciencecentral.com/mean-squared-error/)
- [<span data-ttu-id="937d8-210">平均 2 乗誤差と平均 2 乗誤差平方根とは</span><span class="sxs-lookup"><span data-stu-id="937d8-210">What are Mean Squared Error and Root Mean Squared Error?</span></span>](https://www.vernier.com/til/1014/)