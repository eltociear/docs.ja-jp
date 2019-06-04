---
title: 売上の異常検出シナリオに ML.NET を使用する
description: 製品売上の異常検出シナリオに ML.NET を使用してデータの異常スパイクと変化点について分析し、適切な措置を講じる方法について説明します。
ms.date: 05/06/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 39e812facccfa75d1643704f8960a387a70c94bc
ms.sourcegitcommit: 8699383914c24a0df033393f55db3369db728a7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/15/2019
ms.locfileid: "65641146"
---
# <a name="tutorial-use-mlnet-for-product-sales-anomaly-detection"></a><span data-ttu-id="9d70b-103">チュートリアル: 製品売上の異常検出に ML.NET を使用する</span><span class="sxs-lookup"><span data-stu-id="9d70b-103">Tutorial: Use ML.NET for product sales anomaly detection</span></span> 

<span data-ttu-id="9d70b-104">このサンプル チュートリアルでは、ML.NET を使用して製品売上データの異常を検出し、Visual Studio 2019 で C# を使用して .NET Core コンソール アプリケーションを介して適切な措置を講じる方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-104">This sample tutorial illustrates using ML.NET to detect anomalies in product sales data to take the appropriate action via a .NET Core console application using C# in Visual Studio 2019.</span></span> 

<span data-ttu-id="9d70b-105">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-105">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="9d70b-106">データを読み込む</span><span class="sxs-lookup"><span data-stu-id="9d70b-106">Load the data</span></span>
> * <span data-ttu-id="9d70b-107">スパイクの異常検出のためにモデルをトレーニングする</span><span class="sxs-lookup"><span data-stu-id="9d70b-107">Train the model for spike anomaly detection</span></span>
> * <span data-ttu-id="9d70b-108">トレーニング済みモデルを使用してスパイクの異常を検出する</span><span class="sxs-lookup"><span data-stu-id="9d70b-108">Detect spike anomalies with the trained model</span></span>
> * <span data-ttu-id="9d70b-109">変化点の異常検出のためにモデルをトレーニングする</span><span class="sxs-lookup"><span data-stu-id="9d70b-109">Train the model for change point anomaly detection</span></span>
> * <span data-ttu-id="9d70b-110">トレーニング済みモデルを使用して変化点の異常を検出する</span><span class="sxs-lookup"><span data-stu-id="9d70b-110">Detect change point anomalies with the trained model</span></span>

<span data-ttu-id="9d70b-111">このチュートリアルのソース コードは [dotnet/samples](https://github.com/dotnet/samples/tree/master/machine-learning/tutorials/ProductSalesAnomalyDetection) リポジトリで確認できます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-111">You can find the source code for this tutorial at the [dotnet/samples](https://github.com/dotnet/samples/tree/master/machine-learning/tutorials/ProductSalesAnomalyDetection) repository.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9d70b-112">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9d70b-112">Prerequisites</span></span>

* <span data-ttu-id="9d70b-113">[Visual Studio 2017 15.6 以降](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)が ".NET Core クロスプラット フォーム開発" とともにインストールされていること。</span><span class="sxs-lookup"><span data-stu-id="9d70b-113">[Visual Studio 2017 15.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the ".NET Core cross-platform development" workload installed.</span></span>

* [<span data-ttu-id="9d70b-114">product-sales.csv データセット</span><span class="sxs-lookup"><span data-stu-id="9d70b-114">The product-sales.csv dataset</span></span>](https://raw.githubusercontent.com/dotnet/machinelearning-samples/master/samples/csharp/getting-started/AnomalyDetection_Sales/SpikeDetection/Data/product-sales.csv)

>[!NOTE]
> <span data-ttu-id="9d70b-115">`product-sales.csv` のデータ形式は、DataMarket が出典であるデータセット "Shampoo Sales Over a Three Year Period" (過去 3 年間のシャンプーの売上) に基づいており、Rob Hyndman が作成した Time Series Data Library (TSDL) で提供されています。</span><span class="sxs-lookup"><span data-stu-id="9d70b-115">The data format in `product-sales.csv` is based on the dataset “Shampoo Sales Over a Three Year Period” originally sourced from DataMarket and provided by Time Series Data Library (TSDL), created by Rob Hyndman.</span></span> <span data-ttu-id="9d70b-116">"Shampoo Sales Over a Three Year Period" データセットは、DataMarket Default Open License の下でライセンスを受けています。</span><span class="sxs-lookup"><span data-stu-id="9d70b-116">“Shampoo Sales Over a Three Year Period” Dataset Licensed Under the DataMarket Default Open License.</span></span>

## <a name="create-a-console-application"></a><span data-ttu-id="9d70b-117">コンソール アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="9d70b-117">Create a console application</span></span>

1. <span data-ttu-id="9d70b-118">"ProductSalesAnomalyDetection" という **.NET Core コンソール アプリケーション**を作成します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-118">Create a **.NET Core Console Application** called "ProductSalesAnomalyDetection".</span></span>

2. <span data-ttu-id="9d70b-119">データ セット ファイルを保存するために、プロジェクトに *Data* という名前のディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-119">Create a directory named *Data* in your project to save your data set files.</span></span>

3. <span data-ttu-id="9d70b-120">**Microsoft.ML NuGet パッケージ**をインストールします。</span><span class="sxs-lookup"><span data-stu-id="9d70b-120">Install the **Microsoft.ML NuGet Package**:</span></span>

    <span data-ttu-id="9d70b-121">ソリューション エクスプローラーで、プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-121">In Solution Explorer, right-click on your project and select **Manage NuGet Packages**.</span></span> <span data-ttu-id="9d70b-122">[パッケージ ソース] として "nuget.org" を選択します。[参照] タブを選択し、「**Microsoft.ML**」を検索します。一覧から **v1.0.0** パッケージを選択し、 **[インストール]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-122">Choose "nuget.org" as the Package source, select the Browse tab, search for **Microsoft.ML**, select the **v1.0.0** package in the list, and select the **Install** button.</span></span> <span data-ttu-id="9d70b-123">**[変更のプレビュー]** ダイアログの **[OK]** を選択します。表示されているパッケージのライセンス条項に同意する場合は、 **[ライセンスの同意]** ダイアログの **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-123">Select the **OK** button on the **Preview Changes** dialog and then select the **I Accept** button on the **License Acceptance** dialog if you agree with the license terms for the packages listed.</span></span> <span data-ttu-id="9d70b-124">**Microsoft.ML.TimeSeries v0.12.0** についてもこれらの手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-124">Repeat these steps for **Microsoft.ML.TimeSeries v0.12.0**.</span></span>

4. <span data-ttu-id="9d70b-125">*Program.cs* の先頭に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-125">Add the following `using` statements at the top of your *Program.cs* file:</span></span>

    [!code-csharp[AddUsings](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/Program.cs#AddUsings "Add necessary usings")]

### <a name="download-your-data"></a><span data-ttu-id="9d70b-126">データをダウンロードする</span><span class="sxs-lookup"><span data-stu-id="9d70b-126">Download your data</span></span>

1. <span data-ttu-id="9d70b-127">データセットをダウンロードし、以前に作成した *Data* フォルダーに保存します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-127">Download the dataset and save it to the *Data* folder you previously created:</span></span>

   * <span data-ttu-id="9d70b-128">[*product-sales.csv*](https://raw.githubusercontent.com/dotnet/machinelearning-samples/master/samples/csharp/getting-started/AnomalyDetection_Sales/SpikeDetection/Data/product-sales.csv) を右クリックし、[名前を付けてリンクを保存 (またはターゲット)] を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-128">Right click on [*product-sales.csv*](https://raw.githubusercontent.com/dotnet/machinelearning-samples/master/samples/csharp/getting-started/AnomalyDetection_Sales/SpikeDetection/Data/product-sales.csv) and select "Save Link (or Target) As..."</span></span>

     <span data-ttu-id="9d70b-129">\*.csv ファイルを *Data* フォルダーに保存したことを確認します。または他の場所に保存した後に、\*.csv ファイルを *Data* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-129">Make sure you either save the \*.csv file to the *Data* folder, or after you save it elsewhere, move the \*.csv file to the *Data* folder.</span></span>

2. <span data-ttu-id="9d70b-130">ソリューション エクスプローラーで、\*.csv ファイルを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-130">In Solution Explorer, right-click the \*.csv file and select **Properties**.</span></span> <span data-ttu-id="9d70b-131">**[詳細設定]** で、 **[出力ディレクトリにコピー]** の値を **[新しい場合はコピーする]** に変更します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-131">Under **Advanced**, change the value of **Copy to Output Directory** to **Copy if newer**.</span></span>

<span data-ttu-id="9d70b-132">次の表は、\*.csv ファイルのデータのプレビューです。</span><span class="sxs-lookup"><span data-stu-id="9d70b-132">The following table is a data preview from your \*.csv file:</span></span>

|<span data-ttu-id="9d70b-133">月</span><span class="sxs-lookup"><span data-stu-id="9d70b-133">Month</span></span>  |<span data-ttu-id="9d70b-134">ProductSales</span><span class="sxs-lookup"><span data-stu-id="9d70b-134">ProductSales</span></span> |
|-------|-------------|
|<span data-ttu-id="9d70b-135">1-Jan</span><span class="sxs-lookup"><span data-stu-id="9d70b-135">1-Jan</span></span>  |    <span data-ttu-id="9d70b-136">271</span><span class="sxs-lookup"><span data-stu-id="9d70b-136">271</span></span>      |
|<span data-ttu-id="9d70b-137">2-Jan</span><span class="sxs-lookup"><span data-stu-id="9d70b-137">2-Jan</span></span>  |    <span data-ttu-id="9d70b-138">150.9</span><span class="sxs-lookup"><span data-stu-id="9d70b-138">150.9</span></span>    |
|<span data-ttu-id="9d70b-139">.....</span><span class="sxs-lookup"><span data-stu-id="9d70b-139">.....</span></span>  |    <span data-ttu-id="9d70b-140">.....</span><span class="sxs-lookup"><span data-stu-id="9d70b-140">.....</span></span>    |
|<span data-ttu-id="9d70b-141">1-Feb</span><span class="sxs-lookup"><span data-stu-id="9d70b-141">1-Feb</span></span>  |    <span data-ttu-id="9d70b-142">199.3</span><span class="sxs-lookup"><span data-stu-id="9d70b-142">199.3</span></span>    |
|<span data-ttu-id="9d70b-143">.....</span><span class="sxs-lookup"><span data-stu-id="9d70b-143">.....</span></span>  |    <span data-ttu-id="9d70b-144">.....</span><span class="sxs-lookup"><span data-stu-id="9d70b-144">.....</span></span>    |

### <a name="create-classes-and-define-paths"></a><span data-ttu-id="9d70b-145">クラスを作成してパスを定義する</span><span class="sxs-lookup"><span data-stu-id="9d70b-145">Create classes and define paths</span></span>

<span data-ttu-id="9d70b-146">次に、入力クラスのデータ構造を定義します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-146">Next, define your input class data structure.</span></span>

<span data-ttu-id="9d70b-147">プロジェクトに新しいクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-147">Add a new class to your project:</span></span>

1. <span data-ttu-id="9d70b-148">**ソリューション エクスプローラー**で、プロジェクトを右クリックして、 **[追加]、[新しいアイテム]** の順に選びます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-148">In **Solution Explorer**, right-click the project, and then select **Add > New Item**.</span></span>

2. <span data-ttu-id="9d70b-149">**[新しい項目の追加] ダイアログ ボックス**で、 **[クラス]** を選択し、 **[名前]** フィールドを「*ProductSalesData.cs*」に変更します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-149">In the **Add New Item dialog box**, select **Class** and change the **Name** field to *ProductSalesData.cs*.</span></span> <span data-ttu-id="9d70b-150">次に、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-150">Then, select the **Add** button.</span></span>

<span data-ttu-id="9d70b-151">コード エディターで *ProductSalesData.cs* ファイルが開きます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-151">The *ProductSalesData.cs* file opens in the code editor.</span></span> <span data-ttu-id="9d70b-152">次の `using` ステートメントを *ProductSalesData.cs* の先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-152">Add the following `using` statement to the top of *ProductSalesData.cs*:</span></span>

```csharp
using Microsoft.ML.Data;
```

<span data-ttu-id="9d70b-153">既存のクラス定義を削除し、`ProductSalesData` と `ProductSalesPrediction` の 2 つのクラスを含む次のコードを *ProductSalesData.cs* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-153">Remove the existing class definition and add the following code, which has two classes `ProductSalesData` and `ProductSalesPrediction`, to the *ProductSalesData.cs* file:</span></span>

[!code-csharp[DeclareTypes](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/ProductSalesData.cs#DeclareTypes "Declare data record types")]

<span data-ttu-id="9d70b-154">`ProductSalesData` は入力データ クラスを指定します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-154">`ProductSalesData` specifies an input data class.</span></span> <span data-ttu-id="9d70b-155">[LoadColumn](xref:Microsoft.ML.Data.LoadColumnAttribute.%23ctor%28System.Int32%29) 属性は、データセット内のどの列 (列インデックス) を読み込むかを指定します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-155">The [LoadColumn](xref:Microsoft.ML.Data.LoadColumnAttribute.%23ctor%28System.Int32%29) attribute specifies which columns (by column index) in the dataset should be loaded.</span></span> 

<span data-ttu-id="9d70b-156">次に示す追加の `using` ステートメントを *Program.cs* ファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-156">Add the following additional `using` statements to the top of the *Program.cs* file:</span></span>

[!code-csharp[AddUsings](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/Program.cs#AddUsings "Add necessary usings")]

<span data-ttu-id="9d70b-157">最近ダウンロードしたデータセット ファイルのパスと保存したモデル ファイルのパスを保持するために、2 つのグローバル フィールドを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d70b-157">You need to create two global fields to hold the recently downloaded dataset file path and the saved model file path:</span></span>

* <span data-ttu-id="9d70b-158">`_dataPath` には、モデルのトレーニングに使用するデータ セットのパスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-158">`_dataPath` has the path to the dataset used to train the model.</span></span>
* <span data-ttu-id="9d70b-159">`_docsize` はデータセット ファイル内のレコード数です。</span><span class="sxs-lookup"><span data-stu-id="9d70b-159">`_docsize` has the number of records in dataset file.</span></span> <span data-ttu-id="9d70b-160">これを `pvalueHistoryLength` の計算に使用します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-160">You'll use this to calculate `pvalueHistoryLength`.</span></span>

<span data-ttu-id="9d70b-161">`Main` メソッドのすぐ上にある行に次のコードを追加して、それらのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-161">Add the following code to the line right above the `Main` method to specify those paths:</span></span>

[!code-csharp[Declare global variables](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/Program.cs#DeclareGlobalVariables "Declare global variables")]

<span data-ttu-id="9d70b-162">[MLContext クラス](xref:Microsoft.ML.MLContext)は、すべての ML.NET 操作の開始点で、`mlContext` を初期化することで、モデル作成ワークフローのオブジェクト間で共有できる新しい ML.NET 環境が作成されます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-162">The [MLContext class](xref:Microsoft.ML.MLContext) is a starting point for all ML.NET operations, and initializing `mlContext` creates a new ML.NET environment that can be shared across the model creation workflow objects.</span></span> <span data-ttu-id="9d70b-163">これは Entity Framework における `DBContext` と概念的には同じです。</span><span class="sxs-lookup"><span data-stu-id="9d70b-163">It's similar, conceptually, to `DBContext` in Entity Framework.</span></span>

### <a name="initialize-variables-in-main"></a><span data-ttu-id="9d70b-164">Main で変数を初期化する</span><span class="sxs-lookup"><span data-stu-id="9d70b-164">Initialize variables in Main</span></span>

<span data-ttu-id="9d70b-165">`Main` メソッドの `Console.WriteLine("Hello World!")` の行は、`mlContext` 変数を宣言して初期化する次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-165">Replace the `Console.WriteLine("Hello World!")` line in the `Main` method with the following code to declare and initialize the `mlContext` variable:</span></span>

[!code-csharp[CreateMLContext](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/Program.cs#CreateMLContext "Create the ML Context")]

### <a name="load-the-data"></a><span data-ttu-id="9d70b-166">データを読み込む</span><span class="sxs-lookup"><span data-stu-id="9d70b-166">Load the data</span></span>

<span data-ttu-id="9d70b-167">ML.NET 内のデータは、[IDataView クラス](xref:Microsoft.ML.IDataView)として表されます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-167">Data in ML.NET is represented as an [IDataView class](xref:Microsoft.ML.IDataView).</span></span> <span data-ttu-id="9d70b-168">`IDataView` は、表形式のデータ (数値とテキスト) を表すための柔軟で効率的な方法です。</span><span class="sxs-lookup"><span data-stu-id="9d70b-168">`IDataView` is a flexible, efficient way of describing tabular data (numeric and text).</span></span> <span data-ttu-id="9d70b-169">データはテキスト ファイルから、またはリアルタイムで (SQL データベースやログ ファイルなど) `IDataView` オブジェクトに読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-169">Data can be loaded from a text file or in real time (for example, SQL database or log files) to an `IDataView` object.</span></span> <span data-ttu-id="9d70b-170">`Main()` メソッドの次の行として次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-170">Add the following code as the next line of the `Main()` method:</span></span>

[!code-csharp[LoadData](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/Program.cs#LoadData "loading dataset")]

<span data-ttu-id="9d70b-171">[LoadFromTextFile()](xref:Microsoft.ML.TextLoaderSaverCatalog.LoadFromTextFile%60%601%28Microsoft.ML.DataOperationsCatalog,System.String,System.Char,System.Boolean,System.Boolean,System.Boolean,System.Boolean%29) は、データ スキーマを定義し、ファイルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="9d70b-171">The [LoadFromTextFile()](xref:Microsoft.ML.TextLoaderSaverCatalog.LoadFromTextFile%60%601%28Microsoft.ML.DataOperationsCatalog,System.String,System.Char,System.Boolean,System.Boolean,System.Boolean,System.Boolean%29) defines the data schema and reads in the file.</span></span> <span data-ttu-id="9d70b-172">データ パス変数を取得して、`IDataView` を返します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-172">It takes in the data path variables and returns an `IDataView`.</span></span>

## <a name="ml-task---time-series-anomaly-detection"></a><span data-ttu-id="9d70b-173">ML タスク - 時系列の異常検出</span><span class="sxs-lookup"><span data-stu-id="9d70b-173">ML task - time series anomaly detection</span></span> 

<span data-ttu-id="9d70b-174">異常検出によって、予期しない、または通常とは異なるイベントや動作にフラグが立てられます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-174">Anomaly detection flags unexpected or unusual events or behaviors.</span></span> <span data-ttu-id="9d70b-175">これは、問題を探す場所の手がかりとなり、"これはおかしいだろうか" という問いの答えを見つけるために役立ちます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-175">It gives clues where to look for problems and helps you answer the question "Is this weird?".</span></span>

![これはおかしいだろうか](./media/sales-anomaly-detection/anomalydetection.png)

<span data-ttu-id="9d70b-177">異常検出は、時系列データの異常値 (指定された入力の時系列上で、動作が予期されたものではない点、つまり "おかしい" 点) を検出するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="9d70b-177">Anomaly detection is the process of detecting time-series data outliers; points on a given input time-series where the behavior isn't what was expected, or "weird".</span></span>

<span data-ttu-id="9d70b-178">これはさまざまな方法で役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-178">This can be useful in lots of ways.</span></span> <span data-ttu-id="9d70b-179">たとえば、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="9d70b-179">For instance:</span></span>

<span data-ttu-id="9d70b-180">車を持っている場合に知りたいこと:このオイル ゲージの表示値は正常か。それとも漏れがあるか。</span><span class="sxs-lookup"><span data-stu-id="9d70b-180">If you have a car, you might want to know: Is this oil gauge reading normal, or do I have a leak?</span></span>
<span data-ttu-id="9d70b-181">電力消費量を監視している場合に知りたいこと:停電はあるか。</span><span class="sxs-lookup"><span data-stu-id="9d70b-181">If you're monitoring power consumption, you’d want to know: Is there an outage?</span></span>

<span data-ttu-id="9d70b-182">検出できる時系列の異常には 2 つの種類があります。</span><span class="sxs-lookup"><span data-stu-id="9d70b-182">There are two types of time series anomalies that can be detected:</span></span> 

* <span data-ttu-id="9d70b-183">**スパイク**は、システム内の異常動作の一時的なバーストを示します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-183">**Spikes** indicate temporary bursts of anomalous behavior in the system.</span></span> 

* <span data-ttu-id="9d70b-184">**変化点**は、システム内での長期にわたる永続的な変化の始まりを示します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-184">**Change points** indicate the beginning of persistent changes over time in the system.</span></span> 

<span data-ttu-id="9d70b-185">ML.NET では、[独立した同一分散のデータセット](https://en.wikipedia.org/wiki/Independent_and_identically_distributed_random_variables)には IID Spike Detection (IID スパイク検出) アルゴリズムまたは IID Change point Detection (IID 変化点検出) アルゴリズムが適しています。</span><span class="sxs-lookup"><span data-stu-id="9d70b-185">In ML.NET, The IID Spike Detection or IID Change point Detection algorithms are suited for [independent and identically distributed datasets](https://en.wikipedia.org/wiki/Independent_and_identically_distributed_random_variables).</span></span> 

<span data-ttu-id="9d70b-186">スパイクと変化点の検出には、同じ製品売上データを分析します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-186">You'll analyze the same product sales data to detect spikes and change points.</span></span> <span data-ttu-id="9d70b-187">構築とトレーニング モデル プロセスは、スパイク検出と変化点検出で同じです。主な違いは、使用される具体的な検出アルゴリズムです。</span><span class="sxs-lookup"><span data-stu-id="9d70b-187">The building and training model process is the same for spike detection and change point detection; the main difference is the specific detection algorithm used.</span></span>

## <a name="spike-detection"></a><span data-ttu-id="9d70b-188">スパイク検出</span><span class="sxs-lookup"><span data-stu-id="9d70b-188">Spike detection</span></span> 

<span data-ttu-id="9d70b-189">スパイク検出の目的は、時系列データ値の大部分と大きく異なる突然の一時的なバーストを特定することです。</span><span class="sxs-lookup"><span data-stu-id="9d70b-189">The goal of spike detection is to identify sudden yet temporary bursts that significantly differ from the majority of the time series data values.</span></span> <span data-ttu-id="9d70b-190">このような疑わしいまれな項目、イベント、または観測値を適時に検出して最小限に抑えることが重要です。</span><span class="sxs-lookup"><span data-stu-id="9d70b-190">It's important to detect these suspicious rare items, events or observations in a timely manner to be minimized.</span></span> <span data-ttu-id="9d70b-191">次のようなアプローチを利用すると、停電、サイバー攻撃、バイラル Web コンテンツなど、さまざまな異常を検出できます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-191">The following approach can be used to detect a variety of anomalies such as: outages, cyber-attacks, or viral web content.</span></span> <span data-ttu-id="9d70b-192">次の図は、時系列データセットのスパイクの例です。</span><span class="sxs-lookup"><span data-stu-id="9d70b-192">The following image is an example of spikes in a time series dataset:</span></span>

![SpikeDetection](./media/sales-anomaly-detection/SpikeDetection.png)

### <a name="create-the-detectspike-method"></a><span data-ttu-id="9d70b-194">DetectSpike() メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-194">Create the DetectSpike() method</span></span>

<span data-ttu-id="9d70b-195">`Main()` メソッドの次のコード行として、`DetectSpike()` メソッドに次の呼び出しを追加します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-195">Add the following call to the `DetectSpike()`method as the next line of code in the `Main()` method:</span></span>

[!code-csharp[CallDetectSpike](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/Program.cs#CallDetectSpike)]

<span data-ttu-id="9d70b-196">`DetectSpike()` メソッドは次のタスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-196">The `DetectSpike()` method executes the following tasks:</span></span>

* <span data-ttu-id="9d70b-197">モデルをトレーニングする。</span><span class="sxs-lookup"><span data-stu-id="9d70b-197">Trains the model.</span></span>
* <span data-ttu-id="9d70b-198">売上データ履歴に基づいてスパイクを検出します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-198">Detects spikes based on on historical sales data.</span></span>
* <span data-ttu-id="9d70b-199">結果を表示します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-199">Displays the results.</span></span>

<span data-ttu-id="9d70b-200">`Main()` メソッドの直後に、次のコードを使用して `DetectSpike()` メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-200">Create the `DetectSpike()` method, just after the `Main()` method, using the following code:</span></span>

```csharp
static void DetectSpike(MLContext mlContext, int docSize, IDataView productSales)
{

}
```

<span data-ttu-id="9d70b-201">スパイク検出のために、[IidSpikeEstimator](xref:Microsoft.ML.Transforms.TimeSeries.IidSpikeEstimator) を使用してモデルをトレーニングします。</span><span class="sxs-lookup"><span data-stu-id="9d70b-201">Use the [IidSpikeEstimator](xref:Microsoft.ML.Transforms.TimeSeries.IidSpikeEstimator) to train the model for spike detection.</span></span> <span data-ttu-id="9d70b-202">それを次のコードを使用して `DetectChangepoint()` メソッドに追加します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-202">Add it to the `DetectChangepoint()` method with the following code:</span></span>

[!code-csharp[AddSpikeTrainer](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/Program.cs#AddSpikeTrainer)]

<span data-ttu-id="9d70b-203">`DetectSpike()` メソッドの次のコード行として以下を追加して、モデルを `productSales` データに適合させ、トレーニング済みモデルを返します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-203">Fit the model to the `productSales` data and return the trained model by adding the following as the next line of code in the `DetectSpike()` method:</span></span>

[!code-csharp[TrainModel1](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/Program.cs#TrainModel1)]

<span data-ttu-id="9d70b-204">[Fit()](xref:Microsoft.ML.Data.TrivialEstimator%601.Fit%2A) メソッドでは、データセットを変換して、トレーニングを適用することにより、モデルがトレーニングされます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-204">The [Fit()](xref:Microsoft.ML.Data.TrivialEstimator%601.Fit%2A) method trains your model by transforming the dataset and applying the training.</span></span>

<span data-ttu-id="9d70b-205">次のコード行を追加して、`productSales` データを `DetectSpike()` メソッドの次の行に変換します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-205">Add the following line of code to transform the `productSales` data as the next line in the `DetectSpike()` method:</span></span>

[!code-csharp[TransformData1](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/Program.cs#TransformData1)]

<span data-ttu-id="9d70b-206">前のコードでは、[Transform()](xref:Microsoft.ML.ITransformer.Transform%2A) メソッドを使用して、テスト データセットの指定した複数の入力行に対して予測しています。</span><span class="sxs-lookup"><span data-stu-id="9d70b-206">The previous code uses the [Transform()](xref:Microsoft.ML.ITransformer.Transform%2A) method to make predictions for multiple provided input rows of a test dataset.</span></span>

<span data-ttu-id="9d70b-207">表示を簡単にするために、次のコードを使用し、[CreateEnumerable()](xref:Microsoft.ML.DataOperationsCatalog.CreateEnumerable%2A) メソッドを使って `transformedData` を厳密に型指定された `IEnumerable` に変換します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-207">Convert your `transformedData` into a strongly-typed `IEnumerable` for easier display using the [CreateEnumerable()](xref:Microsoft.ML.DataOperationsCatalog.CreateEnumerable%2A) method with the following code:</span></span>

[!code-csharp[CreateEnumerable1](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/Program.cs#CreateEnumerable1)]

<span data-ttu-id="9d70b-208">次の <xref:System.Console.WriteLine?displayProperty=nameWithType> コードを使用して表示ヘッダー行を作成します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-208">Create a display header line using the following <xref:System.Console.WriteLine?displayProperty=nameWithType> code:</span></span>

[!code-csharp[DisplayHeader1](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/Program.cs#DisplayHeader1)]

<span data-ttu-id="9d70b-209">スパイクの検出結果には、次の情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-209">You'll display the following information in your spike detection results:</span></span>

* <span data-ttu-id="9d70b-210">`Alert` は、特定のデータ ポイントに対するスパイク アラートを示します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-210">`Alert` indicates a spike alert for a given data point.</span></span>

* <span data-ttu-id="9d70b-211">`Score`は、データセット内の特定のデータ ポイントに対する `ProductSales` 値です。</span><span class="sxs-lookup"><span data-stu-id="9d70b-211">`Score` is the `ProductSales` value for a given data point in the dataset.</span></span>

* <span data-ttu-id="9d70b-212">`P-Value` "P" は確率を表します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-212">`P-Value` The "P" stands for probability.</span></span> <span data-ttu-id="9d70b-213">これは、このデータ ポイントが異常である可能性の程度を示します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-213">This indicates how likely this data point is an anomaly.</span></span> 

<span data-ttu-id="9d70b-214">次のコードを使用して `predictions` `IEnumerable` を反復処理し、結果を表示します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-214">Use the following code to iterate through the `predictions` `IEnumerable` and display the results:</span></span>

[!code-csharp[DisplayResults1](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/Program.cs#DisplayResults1)]

## <a name="spike-detection-results"></a><span data-ttu-id="9d70b-215">スパイクの検出結果</span><span class="sxs-lookup"><span data-stu-id="9d70b-215">Spike detection results</span></span>

<span data-ttu-id="9d70b-216">結果は以下のようになるはずです。</span><span class="sxs-lookup"><span data-stu-id="9d70b-216">Your results should be similar to the following.</span></span> <span data-ttu-id="9d70b-217">処理中にメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-217">During processing, messages are displayed.</span></span> <span data-ttu-id="9d70b-218">警告または処理メッセージが表示されることがありますが、</span><span class="sxs-lookup"><span data-stu-id="9d70b-218">You may see warnings, or processing messages.</span></span> <span data-ttu-id="9d70b-219">わかりやすくするために、それらは次の結果から削除してあります。</span><span class="sxs-lookup"><span data-stu-id="9d70b-219">These have been removed from the following results for clarity.</span></span>

```console
Detect temporary changes in pattern
=============== Training the model ===============
=============== End of training process ===============
Alert   Score   P-Value
0       271.00  0.50
0       150.90  0.00
0       188.10  0.41
0       124.30  0.13
0       185.30  0.47
0       173.50  0.47
0       236.80  0.19
0       229.50  0.27
0       197.80  0.48
0       127.90  0.13
1       341.50  0.00 <-- Spike detected
0       190.90  0.48
0       199.30  0.48
0       154.50  0.24
0       215.10  0.42
0       278.30  0.19
0       196.40  0.43
0       292.00  0.17
0       231.00  0.45
0       308.60  0.18
0       294.90  0.19
1       426.60  0.00 <-- Spike detected
0       269.50  0.47
0       347.30  0.21
0       344.70  0.27
0       445.40  0.06
0       320.90  0.49
0       444.30  0.12
0       406.30  0.29
0       442.40  0.21
1       580.50  0.00 <-- Spike detected
0       412.60  0.45
1       687.00  0.01 <-- Spike detected
0       480.30  0.40
0       586.30  0.20
0       651.90  0.14
```

## <a name="change-point-detection"></a><span data-ttu-id="9d70b-220">変化点検出</span><span class="sxs-lookup"><span data-stu-id="9d70b-220">Change point detection</span></span>

<span data-ttu-id="9d70b-221">`Change points` は、レベルの変化や傾向など、時系列のイベント ストリームの値分布の永続的な変化です。</span><span class="sxs-lookup"><span data-stu-id="9d70b-221">`Change points` are persistent changes in a time series event stream distribution of values, like level changes and trends.</span></span> <span data-ttu-id="9d70b-222">こうした永続的な変化は、`spikes` よりもはるかに長く続き、壊滅的なイベントを示している可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9d70b-222">These persistent changes last much longer than `spikes` and could indicate catastrophic event(s).</span></span> <span data-ttu-id="9d70b-223">通常、`Change points` は目視ではわかりませんが、次のような方法を使用してデータから検出できます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-223">`Change points` are not usually visible to the naked eye, but can be detected in your data using approaches such as in the following method.</span></span>  <span data-ttu-id="9d70b-224">次の図は、変化点検出の例です。</span><span class="sxs-lookup"><span data-stu-id="9d70b-224">The following image is an example of a change point detection:</span></span>

![ChangePointDetection](./media/sales-anomaly-detection/ChangePointDetection.png)

### <a name="create-the-detectchangepoint-method"></a><span data-ttu-id="9d70b-226">DetectChangepoint() メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-226">Create the DetectChangepoint() method</span></span>

<span data-ttu-id="9d70b-227">`Main()` メソッドの次のコード行として、`DetectChangepoint()` メソッドに次の呼び出しを追加します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-227">Add the following call to the `DetectChangepoint()`method as the next line of code in the `Main()` method:</span></span>

[!code-csharp[CallDetectChangepoint](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/Program.cs#CallDetectChangepoint)]

<span data-ttu-id="9d70b-228">`DetectChangepoint()` メソッドは次のタスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-228">The `DetectChangepoint()` method executes the following tasks:</span></span>

* <span data-ttu-id="9d70b-229">モデルをトレーニングする。</span><span class="sxs-lookup"><span data-stu-id="9d70b-229">Trains the model.</span></span>
* <span data-ttu-id="9d70b-230">売上データ履歴に基づいて変更点を検出します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-230">Detects change points based on historical sales data.</span></span>
* <span data-ttu-id="9d70b-231">結果を表示します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-231">Displays the results.</span></span>

<span data-ttu-id="9d70b-232">`Main()` メソッドの直後に、次のコードを使用して `DetectChangepoint()` メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-232">Create the `DetectChangepoint()` method, just after the `Main()` method, using the following code:</span></span>

```csharp
static void DetectChangepoint(MLContext mlContext, int docSize, IDataView productSales)
{

}
```

<span data-ttu-id="9d70b-233">[iidChangePointEstimator](xref:Microsoft.ML.Transforms.TimeSeries.IidChangePointEstimator) は、変化点検出のモデルをトレーニングするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-233">The [iidChangePointEstimator](xref:Microsoft.ML.Transforms.TimeSeries.IidChangePointEstimator) is used to train the model for change point detection.</span></span> <span data-ttu-id="9d70b-234">それを次のコードを使用して `DetectChangepoint()` メソッドに追加します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-234">Add it to the `DetectChangepoint()` method with the following code:</span></span>

[!code-csharp[AddChangepointTrainer](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/Program.cs#AddChangepointTrainer)]

<span data-ttu-id="9d70b-235">以前と同様に、`DetectChangePoint()` メソッドの次のコード行として以下を追加して、モデルを `productSales` データに適合させ、トレーニング済みモデルを返します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-235">As you did previously, fit the model to the `productSales` data and return the trained model by adding the following as the next line of code in the `DetectChangePoint()` method:</span></span>

[!code-csharp[TrainModel2](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/Program.cs#TrainModel2)]

<span data-ttu-id="9d70b-236">`Transform()` メソッドを使用して、次のコードを `DetectChangePoint()` に追加して `Training` データを変換します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-236">Use the `Transform()` method to transform the `Training` data by adding the following code to `DetectChangePoint()`:</span></span>

[!code-csharp[TransformData2](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/Program.cs#TransformData2)]

<span data-ttu-id="9d70b-237">以前と同様に、表示を簡単にするために、次のコードを使用し、`CreateEnumerable()` メソッドを使って `transformedData` を厳密に型指定された `IEnumerable` に変換します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-237">As you did previously, convert your `transformedData` into a strongly-typed `IEnumerable` for easier display using the `CreateEnumerable()`method with the following code:</span></span>

[!code-csharp[CreateEnumerable2](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/Program.cs#CreateEnumerable2)]

<span data-ttu-id="9d70b-238">`DetectChangePoint()` メソッドの次の行として次のコードを使用して、表示ヘッダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-238">Create a display header with the following code as the next line in the `DetectChangePoint()` method:</span></span>

[!code-csharp[DisplayHeader2](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/Program.cs#DisplayHeader2)]

<span data-ttu-id="9d70b-239">変化点の検出結果には、次の情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-239">You'll display the following information in your change point detection results:</span></span>

* <span data-ttu-id="9d70b-240">`Alert` は、特定のデータ ポイントに対する変化点アラートを示します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-240">`Alert` indicates a change point alert for a given data point.</span></span>
* <span data-ttu-id="9d70b-241">`Score`は、データセット内の特定のデータ ポイントに対する `ProductSales` 値です。</span><span class="sxs-lookup"><span data-stu-id="9d70b-241">`Score` is the `ProductSales` value for a given data point in the dataset.</span></span>
* <span data-ttu-id="9d70b-242">`P-Value` "P" は確率を表します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-242">`P-Value` The "P" stands for probability.</span></span> <span data-ttu-id="9d70b-243">これは、このデータ ポイントが異常である可能性の程度を示します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-243">This indicates how likely this data point is an anomaly.</span></span> 
* <span data-ttu-id="9d70b-244">`Martingale value` は、一連の P 値に基づいて、データ ポイントがどの程度 "おかしい" かを特定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-244">`Martingale value` is used to identify how "weird" a data point is, based on the sequence of P-values.</span></span>  

<span data-ttu-id="9d70b-245">次のコードを使用して `predictions` `IEnumerable` を反復処理し、結果を表示します。</span><span class="sxs-lookup"><span data-stu-id="9d70b-245">Iterate through the `predictions` `IEnumerable` and display the results with the following code:</span></span>

[!code-csharp[DisplayResults2](~/samples/machine-learning/tutorials/ProductSalesAnomalyDetection/Program.cs#DisplayResults2)]

## <a name="change-point-detection-results"></a><span data-ttu-id="9d70b-246">変化点の検出結果</span><span class="sxs-lookup"><span data-stu-id="9d70b-246">Change point detection results</span></span>

<span data-ttu-id="9d70b-247">結果は以下のようになるはずです。</span><span class="sxs-lookup"><span data-stu-id="9d70b-247">Your results should be similar to the following.</span></span> <span data-ttu-id="9d70b-248">処理中にメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-248">During processing, messages are displayed.</span></span> <span data-ttu-id="9d70b-249">警告または処理メッセージが表示されることがありますが、</span><span class="sxs-lookup"><span data-stu-id="9d70b-249">You may see warnings, or processing messages.</span></span> <span data-ttu-id="9d70b-250">わかりやすくするために、それらは次の結果から削除してあります。</span><span class="sxs-lookup"><span data-stu-id="9d70b-250">These have been removed from the following results for clarity.</span></span>

```console
Detect Persistent changes in pattern
=============== Training the model Using Change Point Detection Algorithm===============
=============== End of training process ===============
Alert   Score   P-Value Martingale value
0       271.00  0.50    0.00
0       150.90  0.00    2.33
0       188.10  0.41    2.80
0       124.30  0.13    9.16
0       185.30  0.47    9.77
0       173.50  0.47    10.41
0       236.80  0.19    24.46
0       229.50  0.27    42.38
1       197.80  0.48    44.23 <-- alert is on, predicted changepoint
0       127.90  0.13    145.25
0       341.50  0.00    0.01
0       190.90  0.48    0.01
0       199.30  0.48    0.00
0       154.50  0.24    0.00
0       215.10  0.42    0.00
0       278.30  0.19    0.00
0       196.40  0.43    0.00
0       292.00  0.17    0.01
0       231.00  0.45    0.00
0       308.60  0.18    0.00
0       294.90  0.19    0.00
0       426.60  0.00    0.00
0       269.50  0.47    0.00
0       347.30  0.21    0.00
0       344.70  0.27    0.00
0       445.40  0.06    0.02
0       320.90  0.49    0.01
0       444.30  0.12    0.02
0       406.30  0.29    0.01
0       442.40  0.21    0.01
0       580.50  0.00    0.01
0       412.60  0.45    0.01
0       687.00  0.01    0.12
0       480.30  0.40    0.08
0       586.30  0.20    0.03
0       651.90  0.14    0.09
```

<span data-ttu-id="9d70b-251">おめでとうございます!</span><span class="sxs-lookup"><span data-stu-id="9d70b-251">Congratulations!</span></span> <span data-ttu-id="9d70b-252">これで、売上データのスパイクや変化点の異常を検出するための機械学習モデルを適切に構築できました。</span><span class="sxs-lookup"><span data-stu-id="9d70b-252">You've now successfully built machine learning models for detecting spikes and change point anomalies in sales data.</span></span>

<span data-ttu-id="9d70b-253">このチュートリアルのソース コードは [dotnet/samples](https://github.com/dotnet/samples/tree/master/machine-learning/tutorials/ProductSalesAnomalyDetection) リポジトリで確認できます。</span><span class="sxs-lookup"><span data-stu-id="9d70b-253">You can find the source code for this tutorial at the [dotnet/samples](https://github.com/dotnet/samples/tree/master/machine-learning/tutorials/ProductSalesAnomalyDetection) repository.</span></span>

<span data-ttu-id="9d70b-254">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="9d70b-254">In this tutorial, you learned how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="9d70b-255">データを読み込む</span><span class="sxs-lookup"><span data-stu-id="9d70b-255">Load the data</span></span>
> * <span data-ttu-id="9d70b-256">スパイクの異常検出のためにモデルをトレーニングする</span><span class="sxs-lookup"><span data-stu-id="9d70b-256">Train the model for spike anomaly detection</span></span>
> * <span data-ttu-id="9d70b-257">トレーニング済みモデルを使用してスパイクの異常を検出する</span><span class="sxs-lookup"><span data-stu-id="9d70b-257">Detect spike anomalies with the trained model</span></span>
> * <span data-ttu-id="9d70b-258">変化点の異常検出のためにモデルをトレーニングする</span><span class="sxs-lookup"><span data-stu-id="9d70b-258">Train the model for change point anomaly detection</span></span>
> * <span data-ttu-id="9d70b-259">トレーニング済みモデルを使用して変化点の異常を検出する</span><span class="sxs-lookup"><span data-stu-id="9d70b-259">Detect change point anomalies with the trained mode</span></span>

## <a name="next-steps"></a><span data-ttu-id="9d70b-260">次の手順</span><span class="sxs-lookup"><span data-stu-id="9d70b-260">Next steps</span></span>

<span data-ttu-id="9d70b-261">Machine Learning サンプルの GitHub リポジトリを確認し、Power Consumption Anomaly Detection サンプルを調べてください。</span><span class="sxs-lookup"><span data-stu-id="9d70b-261">Check out the Machine Learning samples GitHub repository to explore a Power Consumption Anomaly Detection sample.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="9d70b-262">dotnet/machinelearning-samples GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="9d70b-262">dotnet/machinelearning-samples GitHub repository</span></span>](https://github.com/dotnet/machinelearning-samples/tree/master/samples/csharp/getting-started/TimeSeries_PowerAnomalyDetection)