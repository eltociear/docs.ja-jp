---
title: データを読み込む
description: データ ファイルとストリーミング データを ML.NET に読み込む
ms.date: 05/03/2019
ms.custom: mvc,how-to
ms.openlocfilehash: 6edcc92b610e2e1f5e21c371b9f0aefd0b216d31
ms.sourcegitcommit: ca2ca60e6f5ea327f164be7ce26d9599e0f85fe4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65063645"
---
# <a name="load-data-from-file-and-in-memory-sources"></a><span data-ttu-id="bb23f-103">ファイルとメモリ内のソースからデータを読み込む</span><span class="sxs-lookup"><span data-stu-id="bb23f-103">Load data from file and in-memory sources</span></span>

<span data-ttu-id="bb23f-104">このハウツーでは、処理とトレーニング用のデータを ML.NET に読み込む方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="bb23f-104">This how-to shows you how to load data for processing and training into ML.NET.</span></span> <span data-ttu-id="bb23f-105">データは、もともとファイルに格納されていたか、リアルタイム/ストリーミング データ ソースです。</span><span class="sxs-lookup"><span data-stu-id="bb23f-105">The data is originally stored in files or real-time / streaming data sources.</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="bb23f-106">データ モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="bb23f-106">Create the data model</span></span>

<span data-ttu-id="bb23f-107">ML.NET を使用すると、クラスを介してデータ モデルを定義できます。</span><span class="sxs-lookup"><span data-stu-id="bb23f-107">ML.NET enables you to define data models via classes.</span></span> <span data-ttu-id="bb23f-108">たとえば、次のような入力データがあるとします。</span><span class="sxs-lookup"><span data-stu-id="bb23f-108">For example, given the following input data:</span></span>

```text
Size (Sq. ft.), HistoricalPrice1 ($), HistoricalPrice2 ($), HistoricalPrice3 ($), Current Price ($)
700, 100000, 3000000, 250000, 500000
1000, 600000, 400000, 650000, 700000
```

<span data-ttu-id="bb23f-109">以下のスニペットを表すデータ モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="bb23f-109">Create a data model that represents the snippet below:</span></span>

```csharp
public class HousingData
{
    [LoadColumn(0)]
    public float Size { get; set; }
 
    [LoadColumn(1, 3)]
    [VectorType(3)]
    public float[] HistoricalPrices { get; set; }

    [LoadColumn(4)]
    [ColumnName("Label")]
    public float CurrentPrice { get; set; }
}
```

### <a name="annotating-the-data-model-with-column-attributes"></a><span data-ttu-id="bb23f-110">列属性を使用してデータ モデルに注釈を付ける</span><span class="sxs-lookup"><span data-stu-id="bb23f-110">Annotating the data model with column attributes</span></span>

<span data-ttu-id="bb23f-111">属性を使用すると、データ モデルとデータ ソースについてより多くの情報を ML.NET に与えられます。</span><span class="sxs-lookup"><span data-stu-id="bb23f-111">Attributes give ML.NET more information about the data model and the data source.</span></span>

<span data-ttu-id="bb23f-112">[`LoadColumn`](xref:Microsoft.ML.Data.LoadColumnAttribute) 属性では、プロパティの列インデックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="bb23f-112">The [`LoadColumn`](xref:Microsoft.ML.Data.LoadColumnAttribute) attribute specifies your properties' column indices.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="bb23f-113">[`LoadColumn`](xref:Microsoft.ML.Data.LoadColumnAttribute) は、ファイルからデータを読み込む場合にのみ必要です。</span><span class="sxs-lookup"><span data-stu-id="bb23f-113">[`LoadColumn`](xref:Microsoft.ML.Data.LoadColumnAttribute) is only required when loading data from a file.</span></span>

<span data-ttu-id="bb23f-114">列は次のように読み込みます。</span><span class="sxs-lookup"><span data-stu-id="bb23f-114">Load columns as:</span></span> 
- <span data-ttu-id="bb23f-115">`HousingData` クラスの `Size` や `CurrentPrices` のように個々の列。</span><span class="sxs-lookup"><span data-stu-id="bb23f-115">Individual columns like `Size` and `CurrentPrices` in the `HousingData` class.</span></span>
- <span data-ttu-id="bb23f-116">`HousingData` クラスの `HistoricalPrices` のようにベクター形式で一度に複数の列。</span><span class="sxs-lookup"><span data-stu-id="bb23f-116">Multiple columns at a time in the form of a vector like `HistoricalPrices` in the `HousingData` class.</span></span>

<span data-ttu-id="bb23f-117">ベクター プロパティがある場合は、データ モデルのプロパティに [`VectorType`](xref:Microsoft.ML.Data.VectorTypeAttribute) 属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="bb23f-117">If you have a vector property, apply the [`VectorType`](xref:Microsoft.ML.Data.VectorTypeAttribute) attribute to the property in your data model.</span></span> <span data-ttu-id="bb23f-118">ベクター内のすべての要素は同じ型にする必要がある点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="bb23f-118">It's important to note that all of the elements in the vector need to be the same type.</span></span>

<span data-ttu-id="bb23f-119">ML.NET は列名を介して動作します。</span><span class="sxs-lookup"><span data-stu-id="bb23f-119">ML.NET Operates through column names.</span></span> <span data-ttu-id="bb23f-120">列の名前をプロパティ名以外に変更する場合は、[`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) 属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="bb23f-120">If you want to change the name of a column to something other than the property name, use the [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) attribute.</span></span> <span data-ttu-id="bb23f-121">メモリ内オブジェクトを作成するときも、プロパティ名を使ってオブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="bb23f-121">When creating in-memory objects, you still create objects using the property name.</span></span> <span data-ttu-id="bb23f-122">ただし、データ処理と機械学習モデルの構築の場合、ML.NET では [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) 属性に指定された値でプロパティがオーバーライドされ、参照されます。</span><span class="sxs-lookup"><span data-stu-id="bb23f-122">However, for data processing and building machine learning models, ML.NET overrides and references the property with the value provided in the [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) attribute.</span></span>

## <a name="load-data-from-a-single-file"></a><span data-ttu-id="bb23f-123">1 つのファイルからデータを読み込む</span><span class="sxs-lookup"><span data-stu-id="bb23f-123">Load data from a single file</span></span>

<span data-ttu-id="bb23f-124">ファイルからデータを読み込むには、読み込むデータのデータ モデルと共に [`LoadFromTextFile`](xref:Microsoft.ML.TextLoaderSaverCatalog.LoadFromTextFile*) メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="bb23f-124">To load data from a file use the [`LoadFromTextFile`](xref:Microsoft.ML.TextLoaderSaverCatalog.LoadFromTextFile*) method along with the data model for the data to be loaded.</span></span> <span data-ttu-id="bb23f-125">`separatorChar` パラメーターは既定でタブ区切りなので、必要に応じてデータ ファイルに合わせて変更します。</span><span class="sxs-lookup"><span data-stu-id="bb23f-125">Since `separatorChar` parameter is tab-delimited by default, change it for your data file as needed.</span></span> <span data-ttu-id="bb23f-126">ファイルにヘッダーがある場合は、ファイルの最初の行を無視して 2 行目からデータの読み込みを開始するように、`hasHeader` パラメーターを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="bb23f-126">If your file has a header, set the `hasHeader` parameter to `true` to ignore the first line in the file and begin to load data from the second line.</span></span>

```csharp
//Create MLContext
MLContext mlContext = new MLContext();

//Load Data
IDataView data = mlContext.Data.LoadFromTextFile<HousingData>("my-data-file.csv", separatorChar: ',', hasHeader: true);
```

## <a name="load-data-from-multiple-files"></a><span data-ttu-id="bb23f-127">複数のファイルからデータを読み込む</span><span class="sxs-lookup"><span data-stu-id="bb23f-127">Load data from multiple files</span></span>

<span data-ttu-id="bb23f-128">データが複数のファイルに格納されている場合、データ スキーマが同じである限り、ML.NET では、同じディレクトリまたは複数のディレクトリ内にある複数のファイルからデータを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="bb23f-128">In the event that your data is stored in multiple files, as long as the data schema is the same, ML.NET allows you to load data from multiple files that are either in the same directory or multiple directories.</span></span>

### <a name="load-from-files-in-a-single-directory"></a><span data-ttu-id="bb23f-129">1 つのディレクトリ内のファイルから読み込む</span><span class="sxs-lookup"><span data-stu-id="bb23f-129">Load from files in a single directory</span></span>

<span data-ttu-id="bb23f-130">すべてのデータ ファイルが同じディレクトリ内にある場合は、[`LoadFromTextFile`](xref:Microsoft.ML.TextLoaderSaverCatalog.LoadFromTextFile*) メソッドでワイルドカードを使用します。</span><span class="sxs-lookup"><span data-stu-id="bb23f-130">When all of your data files are in the same directory, use wildcards in the [`LoadFromTextFile`](xref:Microsoft.ML.TextLoaderSaverCatalog.LoadFromTextFile*) method.</span></span>

```csharp
//Create MLContext
MLContext mlContext = new MLContext();

//Load Data File
IDataView data = mlContext.Data.LoadFromTextFile<HousingData>("Data/*", separatorChar: ',', hasHeader: true);
```

### <a name="load-from-files-in-multiple-directories"></a><span data-ttu-id="bb23f-131">複数のディレクトリ内にあるファイルから読み込む</span><span class="sxs-lookup"><span data-stu-id="bb23f-131">Load from files in multiple directories</span></span>

<span data-ttu-id="bb23f-132">複数のディレクトリからデータを読み込むには、[`CreateTextLoader`](xref:Microsoft.ML.TextLoaderSaverCatalog.CreateTextLoader*) メソッドを使用して [`TextLoader`](xref:Microsoft.ML.Data.TextLoader) を作成します。</span><span class="sxs-lookup"><span data-stu-id="bb23f-132">To load data from multiple directories, use the [`CreateTextLoader`](xref:Microsoft.ML.TextLoaderSaverCatalog.CreateTextLoader*) method to create a [`TextLoader`](xref:Microsoft.ML.Data.TextLoader).</span></span> <span data-ttu-id="bb23f-133">次に、[`TextLoader.Load`](xref:Microsoft.ML.DataLoaderExtensions.Load*) メソッドを使用して個々のファイル パスを指定します (ワイルドカードは使用できません)。</span><span class="sxs-lookup"><span data-stu-id="bb23f-133">Then, use the [`TextLoader.Load`](xref:Microsoft.ML.DataLoaderExtensions.Load*) method and specify the individual file paths (wildcards can't be used).</span></span>

```csharp
//Create MLContext
MLContext mlContext = new MLContext();

// Create TextLoader
TextLoader textLoader = mlContext.Data.CreateTextLoader<HousingData>(separatorChar: ',', hasHeader: true);

// Load Data
IDataView data = textLoader.Load("DataFolder/SubFolder1/1.txt", "DataFolder/SubFolder2/1.txt");
```

## <a name="load-data-from-a-streaming-source"></a><span data-ttu-id="bb23f-134">ストリーミング ソースからデータを読み込む</span><span class="sxs-lookup"><span data-stu-id="bb23f-134">Load data from a streaming source</span></span>

<span data-ttu-id="bb23f-135">ディスク上の格納データの読み込みに加え、ML.NET は、以下のようにさまざまなストリーミング ソースからのデータの読み込みをサポートします。</span><span class="sxs-lookup"><span data-stu-id="bb23f-135">In addition to loading data stored on disk, ML.NET supports loading data from various streaming sources that include but are not limited to:</span></span>

- <span data-ttu-id="bb23f-136">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="bb23f-136">In-memory collections</span></span>
- <span data-ttu-id="bb23f-137">JSON/XML</span><span class="sxs-lookup"><span data-stu-id="bb23f-137">JSON/XML</span></span>
- <span data-ttu-id="bb23f-138">データベース</span><span class="sxs-lookup"><span data-stu-id="bb23f-138">Databases</span></span>

> [!IMPORTANT]
> <span data-ttu-id="bb23f-139">ストリーミング ソースを使用する場合、ML.NET では入力がメモリ内コレクション形式であると想定される点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="bb23f-139">Note that when working with streaming sources, ML.NET expects input to be in the form of an in-memory collection.</span></span> <span data-ttu-id="bb23f-140">そのため、JSON/XML などのソースを使用するときは、必ずデータをメモリ内コレクション形式にします。</span><span class="sxs-lookup"><span data-stu-id="bb23f-140">Therefore, when working with sources like JSON/XML, make sure to format the data into an in-memory collection.</span></span>

<span data-ttu-id="bb23f-141">次のメモリ内コレクションがあるとします。</span><span class="sxs-lookup"><span data-stu-id="bb23f-141">Given the following in-memory collection:</span></span>

```csharp
HousingData[] inMemoryCollection = new HousingData[]
{
    new HousingData
    {
        Size =700f,
        HistoricalPrices = new float[]
        {
            100000f, 3000000f, 250000f
        },
        CurrentPrice = 500000f
    },
    new HousingData
    {
        Size =1000f,
        HistoricalPrices = new float[]
        {
            600000f, 400000f, 650000f
        },
        CurrentPrice=700000f
    }
};
```

<span data-ttu-id="bb23f-142">[`LoadFromEnumerable`](xref:Microsoft.ML.DataOperationsCatalog.LoadFromEnumerable*) メソッドを使用してメモリ内コレクションを [`IDataView`](xref:Microsoft.ML.IDataView) に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="bb23f-142">Load the in-memory collection into an [`IDataView`](xref:Microsoft.ML.IDataView) with the [`LoadFromEnumerable`](xref:Microsoft.ML.DataOperationsCatalog.LoadFromEnumerable*) method:</span></span>

```csharp
// Create MLContext
MLContext mlContext = new MLContext();

//Load Data
IDataView data = mlContext.Data.LoadFromEnumerable<HousingData>(inMemoryCollection);
```