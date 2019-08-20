---
title: Application Insights サーバーレスアプリ
description: Application Insights は、開発者が web アプリ、モバイルアプリ、デスクトップアプリ、およびマイクロサービスの問題を検出、トリアージ、および診断できるようにする、サーバーレス診断プラットフォームです。
author: JEREMYLIKNESS
ms.author: jeliknes
ms.date: 06/26/2018
ms.openlocfilehash: 1f5b99fba448c2c1c12139524ffdcd3708b3c956
ms.sourcegitcommit: f20dd18dbcf2275513281f5d9ad7ece6a62644b4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "69577725"
---
# <a name="telemetry-with-application-insights"></a><span data-ttu-id="4574d-103">Application Insights を使用したテレメトリ</span><span class="sxs-lookup"><span data-stu-id="4574d-103">Telemetry with Application Insights</span></span>

<span data-ttu-id="4574d-104">[Application Insights](https://docs.microsoft.com/azure/application-insights)は、開発者が web アプリ、モバイルアプリ、デスクトップアプリ、およびマイクロサービスの問題を検出、トリアージ、および診断できるようにする、サーバーレス診断プラットフォームです。</span><span class="sxs-lookup"><span data-stu-id="4574d-104">[Application Insights](https://docs.microsoft.com/azure/application-insights) is a serverless diagnostics platform that enables developers to detect, triage, and diagnose issues in web apps, mobile apps, desktop apps, and microservices.</span></span> <span data-ttu-id="4574d-105">ポータルでスイッチを反転するだけで、関数アプリの Application Insights を有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="4574d-105">You can turn on Application Insights for function apps simply by flipping a switch in the portal.</span></span> <span data-ttu-id="4574d-106">Application Insights には、サーバーを構成したり、独自のデータベースをセットアップしたりすることなく、これらすべての機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="4574d-106">Application Insights provides all of these capabilities without you having to configure a server or set up your own database.</span></span> <span data-ttu-id="4574d-107">すべての Application Insights の機能は、アプリと自動的に統合されるサービスとして提供されます。</span><span class="sxs-lookup"><span data-stu-id="4574d-107">All of Application Insights' capabilities are provided as a service that automatically integrates with your apps.</span></span>

![Application Insights ロゴ](./media/application-insights-logo.png)

<span data-ttu-id="4574d-109">既存のアプリに Application Insights を追加することは、アプリケーションの設定にインストルメンテーションキーを追加するのと同じほど簡単です。</span><span class="sxs-lookup"><span data-stu-id="4574d-109">Adding Application Insights to existing apps is as easy as adding an instrumentation key to your application's settings.</span></span> <span data-ttu-id="4574d-110">Application Insights では、次のことができます。</span><span class="sxs-lookup"><span data-stu-id="4574d-110">With Application Insights you can:</span></span>

* <span data-ttu-id="4574d-111">関数呼び出しの数、関数の実行にかかる時間、例外などのメトリックに基づいて、カスタムのグラフと警告を作成します。</span><span class="sxs-lookup"><span data-stu-id="4574d-111">Create custom charts and alerts based on metrics such as number of function invocations, the time it takes to run a function, and exceptions</span></span>
* <span data-ttu-id="4574d-112">エラーとサーバーの例外を分析する</span><span class="sxs-lookup"><span data-stu-id="4574d-112">Analyze failures and server exceptions</span></span>
* <span data-ttu-id="4574d-113">操作によってパフォーマンスを掘り下げ、サードパーティの依存関係の呼び出しにかかる時間を測定する</span><span class="sxs-lookup"><span data-stu-id="4574d-113">Drill into performance by operation and measure the time it takes to call third-party dependencies</span></span>
* <span data-ttu-id="4574d-114">関数アプリをホストするすべてのサーバーの CPU 使用率、メモリ、および率を監視します</span><span class="sxs-lookup"><span data-stu-id="4574d-114">Monitor CPU usage, memory, and rates across all servers that host your function apps</span></span>
* <span data-ttu-id="4574d-115">関数アプリの要求数や待機時間など、メトリックのライブストリームを表示します</span><span class="sxs-lookup"><span data-stu-id="4574d-115">View a live stream of metrics including request count and latency for your function apps</span></span>
* <span data-ttu-id="4574d-116">[分析](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)を使用して関数データの検索、クエリ、およびカスタムグラフの作成を行う</span><span class="sxs-lookup"><span data-stu-id="4574d-116">Use [Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics) to search, query, and create custom charts over your function data</span></span>

![メトリックスエクスプローラー](./media/metrics-explorer.png)

<span data-ttu-id="4574d-118">組み込みのテレメトリに加えて、カスタムテレメトリを生成することもできます。</span><span class="sxs-lookup"><span data-stu-id="4574d-118">In addition to built-in telemetry, it's also possible to generate custom telemetry.</span></span> <span data-ttu-id="4574d-119">次のコードスニペットは、関数アプリ用に設定されたインストルメンテーションキーを使用して、カスタムテレメトリクライアントを作成します。</span><span class="sxs-lookup"><span data-stu-id="4574d-119">The following code snippet creates a custom telemetry client using the instrumentation key set for the function app:</span></span>

```csharp
public static TelemetryClient telemetry = new TelemetryClient()
{
    InstrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY")
};
```

<span data-ttu-id="4574d-120">次のコードは、 [Azure Table Storage](https://docs.microsoft.com/azure/cosmos-db/table-storage-overview)インスタンスに新しい行を挿入するのにかかる時間を測定します。</span><span class="sxs-lookup"><span data-stu-id="4574d-120">The following code measures how long it takes to insert a new row into an [Azure Table Storage](https://docs.microsoft.com/azure/cosmos-db/table-storage-overview) instance:</span></span>

```csharp
var operation = TableOperation.Insert(entry);
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
await table.ExecuteAsync(operation);
telemetry.TrackDependency("AzureTableStorageInsert", "Insert", startTime, timer.Elapsed, true);
```

<span data-ttu-id="4574d-121">結果のパフォーマンスグラフが表示されます。</span><span class="sxs-lookup"><span data-stu-id="4574d-121">The resulting performance graph is shown:</span></span>

![カスタムテレメトリ](./media/custom-telemetry.png)

<span data-ttu-id="4574d-123">カスタムテレメトリは、新しい行の挿入にかかる平均時間が32.6 ミリ秒であることを示しています。</span><span class="sxs-lookup"><span data-stu-id="4574d-123">The custom telemetry reveals the average time to insert a new row is 32.6 milliseconds.</span></span>

<span data-ttu-id="4574d-124">Application Insights には、サーバーレスアプリケーションに関する詳細なテレメトリをログに記録するための強力で便利な方法が用意されています。</span><span class="sxs-lookup"><span data-stu-id="4574d-124">Application Insights provides a powerful, convenient way to log detailed telemetry about your serverless applications.</span></span> <span data-ttu-id="4574d-125">提供されているトレースとログ記録のレベルを完全に制御できます。</span><span class="sxs-lookup"><span data-stu-id="4574d-125">You have full control over the level of tracing and logging that is provided.</span></span> <span data-ttu-id="4574d-126">イベント、依存関係、ページビューなどのカスタム統計情報を追跡できます。</span><span class="sxs-lookup"><span data-stu-id="4574d-126">You can track custom statistics such as events, dependencies, and page view.</span></span> <span data-ttu-id="4574d-127">さらに、強力な分析により、重要な質問をしてグラフや高度な洞察を生成するクエリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="4574d-127">Finally, the powerful analytics enable you to write queries that ask important questions and generate charts and advanced insights.</span></span>

<span data-ttu-id="4574d-128">詳細については、「 [Monitor Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4574d-128">For more information, see [Monitor Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring).</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="4574d-129">[前へ](azure-functions.md)
>[次へ](logic-apps.md)</span><span class="sxs-lookup"><span data-stu-id="4574d-129">[Previous](azure-functions.md)
[Next](logic-apps.md)</span></span>