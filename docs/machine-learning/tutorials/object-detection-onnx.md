---
title: 'チュートリアル: ONNX と ML.NET でディープ ラーニングを使用してオブジェクトを検出する'
description: このチュートリアルでは、ML.NET の事前トレーニング済みの ONNX ディープ ラーニング モデルを使用して画像内のオブジェクトを検出する方法について説明します。
author: luisquintanilla
ms.author: luquinta
ms.date: 08/01/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: e44ea5795beb90bafe3faf0bafb463d49ba1fc41
ms.sourcegitcommit: 9ee6cd851b6e176a5811ea28ed0d5935c71950f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68868726"
---
# <a name="tutorial-detect-objects-using-onnx-in-mlnet"></a><span data-ttu-id="c4c68-103">チュートリアル: ML.NET で ONNX を使用してオブジェクトを検出する</span><span class="sxs-lookup"><span data-stu-id="c4c68-103">Tutorial: Detect objects using ONNX in ML.NET</span></span>

<span data-ttu-id="c4c68-104">ML.NET の事前トレーニング済みの ONNX モデルを使用して画像内のオブジェクトを検出する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-104">Learn how to use a pre-trained ONNX model in ML.NET to detect objects in images.</span></span>

<span data-ttu-id="c4c68-105">オブジェクト検出モデルを最初からトレーニングするには、数百万のパラメーター、大量のラベル付きトレーニング データ、膨大な量の計算リソース (数百時間の GPU) を設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c4c68-105">Training an object detection model from scratch requires setting millions of parameters, a large amount of labeled training data and a vast amount of compute resources (hundreds of GPU hours).</span></span> <span data-ttu-id="c4c68-106">事前トレーニング済みモデルを使用すると、トレーニング プロセスをショートカットできます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-106">Using a pre-trained model allows you to shortcut the training process.</span></span>

<span data-ttu-id="c4c68-107">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-107">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="c4c68-108">問題を把握する</span><span class="sxs-lookup"><span data-stu-id="c4c68-108">Understand the problem</span></span>
> * <span data-ttu-id="c4c68-109">ONNX の概要と ML.NET でどのように動作するかについて説明します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-109">Learn what ONNX is and how it works with ML.NET</span></span>
> * <span data-ttu-id="c4c68-110">モデルの概要</span><span class="sxs-lookup"><span data-stu-id="c4c68-110">Understand the model</span></span>
> * <span data-ttu-id="c4c68-111">事前トレーニング済みモデルを再利用する</span><span class="sxs-lookup"><span data-stu-id="c4c68-111">Reuse the pre-trained model</span></span>
> * <span data-ttu-id="c4c68-112">読み込まれたモデルを使用してオブジェクトを検出する</span><span class="sxs-lookup"><span data-stu-id="c4c68-112">Detect objects with a loaded model</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="c4c68-113">前提条件</span><span class="sxs-lookup"><span data-stu-id="c4c68-113">Pre-requisites</span></span>

- <span data-ttu-id="c4c68-114">[Visual Studio 2017 15.6 以降](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2017)が ".NET Core クロスプラット フォーム開発" とともにインストールされていること。</span><span class="sxs-lookup"><span data-stu-id="c4c68-114">[Visual Studio 2017 15.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2017) with the ".NET Core cross-platform development" workload installed.</span></span>
- [<span data-ttu-id="c4c68-115">Microsoft.ML NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="c4c68-115">Microsoft.ML NuGet Package</span></span>](https://www.nuget.org/packages/Microsoft.ML/)
- [<span data-ttu-id="c4c68-116">Microsoft.ML.ImageAnalytics NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="c4c68-116">Microsoft.ML.ImageAnalytics NuGet Package</span></span>](https://www.nuget.org/packages/Microsoft.ML.ImageAnalytics/)
- [<span data-ttu-id="c4c68-117">Microsoft.ML.OnnxTransformer NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="c4c68-117">Microsoft.ML.OnnxTransformer NuGet Package</span></span>](https://www.nuget.org/packages/Microsoft.ML.OnnxTransformer/)
- [<span data-ttu-id="c4c68-118">Tiny YOLOv2 事前トレーニング済みモデル</span><span class="sxs-lookup"><span data-stu-id="c4c68-118">Tiny YOLOv2 pre-trained model</span></span>](https://github.com/onnx/models/tree/master/tiny_yolov2)
- <span data-ttu-id="c4c68-119">[Netron](https://github.com/lutzroeder/netron) (省略可能)</span><span class="sxs-lookup"><span data-stu-id="c4c68-119">[Netron](https://github.com/lutzroeder/netron) (optional)</span></span>

## <a name="onnx-object-detection-sample-overview"></a><span data-ttu-id="c4c68-120">ONNX オブジェクト検出サンプルの概要</span><span class="sxs-lookup"><span data-stu-id="c4c68-120">ONNX object detection sample overview</span></span>

<span data-ttu-id="c4c68-121">このサンプルでは、事前トレーニング済みのディープ ラーニング ONNX モデルを使用して、画像内のオブジェクトを検出する .NET Core コンソール アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-121">This sample creates a .NET core console application that detects objects within an image using a pre-trained deep learning ONNX model.</span></span> <span data-ttu-id="c4c68-122">このサンプルのコードについては、GitHub の [dotnet/machinelearning-samples リポジトリ](https://github.com/dotnet/machinelearning-samples/tree/master/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c4c68-122">The code for this sample can be found on the [dotnet/machinelearning-samples repository](https://github.com/dotnet/machinelearning-samples/tree/master/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx) on GitHub.</span></span>

## <a name="what-is-object-detection"></a><span data-ttu-id="c4c68-123">オブジェクトの検出とは</span><span class="sxs-lookup"><span data-stu-id="c4c68-123">What is object detection?</span></span>

<span data-ttu-id="c4c68-124">オブジェクト検出はコンピューターのビジョンの問題です。</span><span class="sxs-lookup"><span data-stu-id="c4c68-124">Object detection is a computer vision problem.</span></span> <span data-ttu-id="c4c68-125">画像の分類に密接に関連していますが、オブジェクト検出では、より詳細なスケールで画像分類が実行されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-125">While closely related to image classification, object detection performs image classification at a more granular scale.</span></span> <span data-ttu-id="c4c68-126">オブジェクト検出では、画像内のエンティティの特定 " _" 分類の両方が行われます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-126">Object detection both locates _and_ categorizes entities within images.</span></span> <span data-ttu-id="c4c68-127">オブジェクト検出は、画像に異なる種類のオブジェクトが複数含まれる場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-127">Use object detection when images contain multiple objects of different types.</span></span>

![](./media/object-detection-onnx/img-classification-obj-detection.PNG)

<span data-ttu-id="c4c68-128">オブジェクト検出のユース ケースには、次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="c4c68-128">Some use cases for object detection include:</span></span>

- <span data-ttu-id="c4c68-129">自動運転車</span><span class="sxs-lookup"><span data-stu-id="c4c68-129">Self-Driving Cars</span></span>
- <span data-ttu-id="c4c68-130">ロボティクス</span><span class="sxs-lookup"><span data-stu-id="c4c68-130">Robotics</span></span>
- <span data-ttu-id="c4c68-131">顔検出</span><span class="sxs-lookup"><span data-stu-id="c4c68-131">Face Detection</span></span>
- <span data-ttu-id="c4c68-132">職場の安全</span><span class="sxs-lookup"><span data-stu-id="c4c68-132">Workplace Safety</span></span>
- <span data-ttu-id="c4c68-133">オブジェクトのカウント</span><span class="sxs-lookup"><span data-stu-id="c4c68-133">Object Counting</span></span>
- <span data-ttu-id="c4c68-134">アクティビティ認識</span><span class="sxs-lookup"><span data-stu-id="c4c68-134">Activity Recognition</span></span>

## <a name="select-a-deep-learning-model"></a><span data-ttu-id="c4c68-135">ディープ ラーニング モデルを選択する</span><span class="sxs-lookup"><span data-stu-id="c4c68-135">Select a deep learning model</span></span>

<span data-ttu-id="c4c68-136">ディープ ラーニングは、機械学習のサブセットです。</span><span class="sxs-lookup"><span data-stu-id="c4c68-136">Deep learning is a subset of machine learning.</span></span> <span data-ttu-id="c4c68-137">ディープ ラーニング モデルをトレーニングするには、大量のデータが必要です。</span><span class="sxs-lookup"><span data-stu-id="c4c68-137">To train deep learning models, large quantities of data are required.</span></span> <span data-ttu-id="c4c68-138">データ内のパターンは、一連のレイヤーによって表されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-138">Patterns in the data are represented by a series of layers.</span></span> <span data-ttu-id="c4c68-139">データ内のリレーションシップは、重みを含むレイヤー間の接続としてエンコードされます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-139">The relationships in the data are encoded as connections between the layers containing weights.</span></span> <span data-ttu-id="c4c68-140">重みが高いほど、リレーションシップが強くなります。</span><span class="sxs-lookup"><span data-stu-id="c4c68-140">The higher the weight, the stronger the relationship.</span></span> <span data-ttu-id="c4c68-141">この一連のレイヤーと接続は、まとめて人工ニューラル ネットワークと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-141">Collectively, this series of layers and connections are known as artificial neural networks.</span></span> <span data-ttu-id="c4c68-142">ネットワーク内のレイヤーが多くなるほど、"深く" なり、ディープ ニューラル ネットワークになります。</span><span class="sxs-lookup"><span data-stu-id="c4c68-142">The more layers in a network, the "deeper" it is, making it a deep neural network.</span></span> 

<span data-ttu-id="c4c68-143">ニューラル ネットワークにはさまざまな種類があり、よく知られているものとして、マルチレイヤー パーセプトロン (MLP)、畳み込みニューラル ネットワーク (CNN)、および再帰型ニューラル ネットワーク (RNN) があります。</span><span class="sxs-lookup"><span data-stu-id="c4c68-143">There are different types of neural networks, the most common being Multi-Layered Perceptron (MLP), Convolutional Neural Network (CNN) and Recurrent Neural Network (RNN).</span></span> <span data-ttu-id="c4c68-144">最も基本的なのは MLP であり、一連の入力が一連の出力にマップされます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-144">The most basic is the MLP, which maps a set of inputs to a set of outputs.</span></span> <span data-ttu-id="c4c68-145">このニューラル ネットワークは、データに空間と時間のコンポーネントがない場合に適しています。</span><span class="sxs-lookup"><span data-stu-id="c4c68-145">This neural network is good when the data does not have a spatial or time component.</span></span> <span data-ttu-id="c4c68-146">CNN では、畳み込みレイヤーを利用し、データに含まれている空間情報を処理します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-146">The CNN makes use of convolutional layers to process spatial information contained in the data.</span></span> <span data-ttu-id="c4c68-147">CNN の良いユース ケースは、画像の領域内に特徴が存在するかどうかを検出する画像処理です (画像の中央に鼻があるか、など)。</span><span class="sxs-lookup"><span data-stu-id="c4c68-147">A good use case for CNNs is image processing to detect the presence of a feature in a region of an image (for example, is there a nose in the center of an image?).</span></span> <span data-ttu-id="c4c68-148">最後に、RNN では、状態またはメモリの永続化を入力として使用できます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-148">Finally, RNNs allow for the persistence of state or memory to be used as input.</span></span> <span data-ttu-id="c4c68-149">RNN は、時系列分析に使用されます。この場合、イベントの順序付けとコンテキストが重要です。</span><span class="sxs-lookup"><span data-stu-id="c4c68-149">RNNs are used for time-series analysis, where the sequential ordering and context of events is important.</span></span> 

### <a name="understand-the-model"></a><span data-ttu-id="c4c68-150">モデルの概要</span><span class="sxs-lookup"><span data-stu-id="c4c68-150">Understand the model</span></span>

<span data-ttu-id="c4c68-151">オブジェクト検出は、画像処理タスクです。</span><span class="sxs-lookup"><span data-stu-id="c4c68-151">Object detection is an image processing task.</span></span> <span data-ttu-id="c4c68-152">そのため、この問題を解決するためにトレーニングされるほとんどのディープ ラーニング モデルは、CNN です。</span><span class="sxs-lookup"><span data-stu-id="c4c68-152">Therefore, most deep learning models trained to solve this problem are CNNs.</span></span> <span data-ttu-id="c4c68-153">このチュートリアルで使用するモデルは、次のドキュメントで説明されている YOLOv2 モデルのコンパクトなバージョンである Tiny YOLOv2 モデルです。[「YOLO9000:Better, Faster, Stronger」 (YOLO9000: より良く、より速く、より強く) (Redmon、Fadhari 著)](https://arxiv.org/pdf/1612.08242.pdf)。</span><span class="sxs-lookup"><span data-stu-id="c4c68-153">The model used in this tutorial is the Tiny YOLOv2 model, a more compact version of the YOLOv2 model described in the paper: ["YOLO9000: Better, Faster, Stronger" by Redmon and Fadhari](https://arxiv.org/pdf/1612.08242.pdf).</span></span> <span data-ttu-id="c4c68-154">Tiny YOLOv2 は Pascal VOC データセットでトレーニングされ、20 種類のクラスのオブジェクトを予測できる 15 個のレイヤーで構成されています。</span><span class="sxs-lookup"><span data-stu-id="c4c68-154">Tiny YOLOv2 is trained on the Pascal VOC dataset and is made up of 15 layers that can predict 20 different classes of objects.</span></span> <span data-ttu-id="c4c68-155">Tiny YOLOv2 は元の YOLOv2 モデルを凝縮したバージョンなので、速度と精度のトレードオフが生じます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-155">Because Tiny YOLOv2 is a condensed version of the original YOLOv2 model, a tradeoff is made between speed and accuracy.</span></span> <span data-ttu-id="c4c68-156">Netron などのツールを使用して、モデルを構成するさまざまなレイヤーを視覚化できます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-156">The different layers that make up the model can be visualized using tools like Netron.</span></span> <span data-ttu-id="c4c68-157">モデルを検査すると、ニューラル ネットワークを構成するすべてのレイヤー間の接続のマッピングが生成されます。各レイヤーには、レイヤーの名前と共にそれぞれの入力/出力の寸法が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-157">Inspecting the model would yield a mapping of the connections between all the layers that make up the neural network, where each layer would contain the name of the layer along with the dimensions of the respective input / output.</span></span> <span data-ttu-id="c4c68-158">モデルの入力と出力を記述するために使用されるデータ構造は、テンソルと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-158">The data structures used to describe the inputs and outputs of the model are known as tensors.</span></span> <span data-ttu-id="c4c68-159">テンソルは、データを N 次元に格納するコンテナーと考えることができます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-159">Tensors can be thought of as containers that store data in N-dimensions.</span></span> <span data-ttu-id="c4c68-160">Tiny YOLOv2 の場合、入力レイヤーの名前は `image` であり、`3 x 416 x 416` の寸法のテンソルが想定されています。</span><span class="sxs-lookup"><span data-stu-id="c4c68-160">In the case of Tiny YOLOv2, the name of the input layer is `image` and it expects a tensor of dimensions `3 x 416 x 416`.</span></span> <span data-ttu-id="c4c68-161">出力レイヤーの名前は `grid` であり、`125 x 13 x 13` の寸法の出力テンソルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-161">The name of the output layer is `grid` and generates an output tensor of dimensions `125 x 13 x 13`.</span></span>  

![](./media/object-detection-onnx/netron-model-map.png)

<span data-ttu-id="c4c68-162">YOLO モデルは `3(RGB) x 416px x 416px` の画像を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c4c68-162">The YOLO model takes an image `3(RGB) x 416px x 416px`.</span></span> <span data-ttu-id="c4c68-163">この入力はモデルによって取得され、さまざまなレイヤーを経由して出力が生成されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-163">The model takes this input and passes it through the different layers to produce an output.</span></span> <span data-ttu-id="c4c68-164">出力では入力画像が `13 x 13` グリッドに分割されます。グリッド内の各セルは、`125` 値で構成されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-164">The output divides the input image into a `13 x 13` grid, with each cell in the grid consisting of `125` values.</span></span> 

### <a name="what-is-an-onnx-model"></a><span data-ttu-id="c4c68-165">ONNX モデルとは</span><span class="sxs-lookup"><span data-stu-id="c4c68-165">What is an ONNX model?</span></span>

<span data-ttu-id="c4c68-166">Open Neural Network Exchange (ONNX) は、AI モデルのオープン ソース形式です。</span><span class="sxs-lookup"><span data-stu-id="c4c68-166">The Open Neural Network Exchange (ONNX) is an open source format for AI models.</span></span> <span data-ttu-id="c4c68-167">ONNX は、フレームワーク間の相互運用性をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="c4c68-167">ONNX supports interoperability between frameworks.</span></span> <span data-ttu-id="c4c68-168">つまり、PyTorch などの多くの一般的な機械学習フレームワークのいずれかでモデルをトレーニングして ONNX 形式に変換し、ML.NET などの別のフレームワークで ONNX モデルを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-168">This means you can train a model in one of the many popular machine learning frameworks like PyTorch, convert it into ONNX format and consume the ONNX model in a different framework like ML.NET.</span></span> <span data-ttu-id="c4c68-169">詳細については、[ONNX の Web サイト](https://onnx.ai/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c4c68-169">To learn more, visit the [ONNX website](https://onnx.ai/).</span></span> 

![](./media/object-detection-onnx/onnx-frameworks.png)

<span data-ttu-id="c4c68-170">事前トレーニング済みの Tiny YOLOv2 モデルは ONNX 形式で格納されます。これはレイヤーのシリアル化された表現であり、それらのレイヤーの学習済みパターンです。</span><span class="sxs-lookup"><span data-stu-id="c4c68-170">The pre-trained Tiny YOLOv2 model is stored in ONNX format, a serialized representation of the layers and learned patterns of those layers.</span></span> <span data-ttu-id="c4c68-171">ML.NET では、ONNX との相互運用性は [`ImageAnalytics`](xref:Microsoft.ML.Transforms.Image) および [`OnnxTransformer`](xref:Microsoft.ML.Transforms.Onnx.OnnxTransformer) NuGet パッケージを使用して実現されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-171">In ML.NET, interoperability with ONNX is achieved with the [`ImageAnalytics`](xref:Microsoft.ML.Transforms.Image) and [`OnnxTransformer`](xref:Microsoft.ML.Transforms.Onnx.OnnxTransformer) NuGet packages.</span></span> <span data-ttu-id="c4c68-172">[`ImageAnalytics`](xref:Microsoft.ML.Transforms.Image) パッケージには、画像を受け取り、予測またはトレーニング パイプラインへの入力として使用できる数値にエンコードする一連の変換が含まれています。</span><span class="sxs-lookup"><span data-stu-id="c4c68-172">The [`ImageAnalytics`](xref:Microsoft.ML.Transforms.Image) package contains a series of transforms that take an image and encode it into numerical values that can be used as input into a prediction or training pipeline.</span></span> <span data-ttu-id="c4c68-173">[`OnnxTransformer`](xref:Microsoft.ML.Transforms.Onnx.OnnxTransformer) パッケージでは、ONNX ランタイムを利用して ONNX モデルを読み込み、それを使用して、指定された入力に基づいて予測を行います。</span><span class="sxs-lookup"><span data-stu-id="c4c68-173">The [`OnnxTransformer`](xref:Microsoft.ML.Transforms.Onnx.OnnxTransformer) package leverages the ONNX Runtime to load an ONNX model and use it to make predictions based on input provided.</span></span> 

![](./media/object-detection-onnx/onnx-ml-net-integration.png)

## <a name="set-up-the-net-core-project"></a><span data-ttu-id="c4c68-174">.NET Core プロジェクトを設定する</span><span class="sxs-lookup"><span data-stu-id="c4c68-174">Set up the .NET Core project</span></span>

<span data-ttu-id="c4c68-175">ONNX の概要と Tiny YOLOv2 のしくみについて全般的な知識が得られたので、次はアプリケーションをビルドします。</span><span class="sxs-lookup"><span data-stu-id="c4c68-175">Now that you have a general understanding of what ONNX is and how Tiny YOLOv2 works, it's time to build the application.</span></span>

### <a name="create-a-console-application"></a><span data-ttu-id="c4c68-176">コンソール アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="c4c68-176">Create a console application</span></span>

1. <span data-ttu-id="c4c68-177">"ObjectDetection" という名前の **.NET Core コンソール アプリケーション**を作成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-177">Create a **.NET Core Console Application** called "ObjectDetection".</span></span>

1. <span data-ttu-id="c4c68-178">**Microsoft.ML NuGet パッケージ**をインストールします。</span><span class="sxs-lookup"><span data-stu-id="c4c68-178">Install the **Microsoft.ML NuGet Package**:</span></span>

    - <span data-ttu-id="c4c68-179">ソリューション エクスプローラーで、プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-179">In Solution Explorer, right-click on your project and select **Manage NuGet Packages**.</span></span> 
    - <span data-ttu-id="c4c68-180">[パッケージ ソース] として "nuget.org" を選択し、[参照] タブを選択し、"**Microsoft.ML**" を検索します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-180">Choose "nuget.org" as the Package source, select the Browse tab, search for **Microsoft.ML**.</span></span> 
    - <span data-ttu-id="c4c68-181">**[インストール]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-181">Select the **Install** button.</span></span> 
    - <span data-ttu-id="c4c68-182">**[変更のプレビュー]** ダイアログの **[OK]** を選択します。表示されているパッケージのライセンス条項に同意する場合は、 **[ライセンスの同意]** ダイアログの **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-182">Select the **OK** button on the **Preview Changes** dialog and then select the **I Accept** button on the **License Acceptance** dialog if you agree with the license terms for the packages listed.</span></span> 
    - <span data-ttu-id="c4c68-183">**Microsoft.ML.ImageAnalytics** と **Microsoft.ML.OnnxTransformer** に対してこれらの手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-183">Repeat these steps for **Microsoft.ML.ImageAnalytics** and **Microsoft.ML.OnnxTransformer**.</span></span>

### <a name="prepare-your-data-and-pre-trained-model"></a><span data-ttu-id="c4c68-184">データと事前トレーニング済みモデルを準備する</span><span class="sxs-lookup"><span data-stu-id="c4c68-184">Prepare your data and pre-trained model</span></span>

1. <span data-ttu-id="c4c68-185">[プロジェクト資産ディレクトリの zip ファイル](https://github.com/dotnet/machinelearning-samples/raw/master/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/assets.zip)をダウンロードし、解凍します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-185">Download [The project assets directory zip file](https://github.com/dotnet/machinelearning-samples/raw/master/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/assets.zip) and unzip.</span></span>

1. <span data-ttu-id="c4c68-186">`assets` ディレクトリを "*ObjectDetection*" プロジェクト ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="c4c68-186">Copy the `assets` directory into your *ObjectDetection* project directory.</span></span> <span data-ttu-id="c4c68-187">このディレクトリとそのサブディレクトリには、このチュートリアルに必要な画像ファイル (次の手順でダウンロードして追加する Tiny YOLOv2 モデルを除く) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="c4c68-187">This directory and its subdirectories contain the image files (except for the Tiny YOLOv2 model, which you'll download and add in the next step) needed for this tutorial.</span></span>

1. <span data-ttu-id="c4c68-188">[ONNX Model Zoo](https://github.com/onnx/models/tree/master/vision/object_detection_segmentation/tiny_yolov2) から [Tiny YOLOv2 モデル](https://onnxzoo.blob.core.windows.net/models/opset_8/tiny_yolov2/tiny_yolov2.tar.gz)をダウンロードし、解凍します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-188">Download the [Tiny YOLOv2 model](https://onnxzoo.blob.core.windows.net/models/opset_8/tiny_yolov2/tiny_yolov2.tar.gz) from the [ONNX Model Zoo](https://github.com/onnx/models/tree/master/vision/object_detection_segmentation/tiny_yolov2), and unzip.</span></span>

    <span data-ttu-id="c4c68-189">コマンド プロンプトを開き、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-189">Open the command prompt and enter the following command:</span></span>

    ```shell
    tar -xvzf tiny_yolov2.tar.gz 
    ```

1. <span data-ttu-id="c4c68-190">解凍したディレクトリから抽出した `model.onnx` ファイルを "*ObjectDetection*" プロジェクトの `assets\Model` ディレクトリにコピーし、名前を `TinyYolo2_model.onnx` に変更します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-190">Copy the extracted `model.onnx` file from the directory just unzipped into your *ObjectDetection* project `assets\Model` directory and rename it to `TinyYolo2_model.onnx`.</span></span> <span data-ttu-id="c4c68-191">このディレクトリには、このチュートリアルに必要なモデルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c4c68-191">This directory contains the model needed for this tutorial.</span></span>

1. <span data-ttu-id="c4c68-192">ソリューション エクスプローラーで、資産ディレクトリとサブディレクトリ内の各ファイルを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-192">In Solution Explorer, right-click each of the files in the asset directory and subdirectories and select **Properties**.</span></span> <span data-ttu-id="c4c68-193">**[詳細設定]** で、 **[出力ディレクトリにコピー]** の値を **[新しい場合はコピーする]** に変更します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-193">Under **Advanced**, change the value of **Copy to Output Directory** to **Copy if newer**.</span></span>

### <a name="create-classes-and-define-paths"></a><span data-ttu-id="c4c68-194">クラスを作成してパスを定義する</span><span class="sxs-lookup"><span data-stu-id="c4c68-194">Create classes and define paths</span></span>

<span data-ttu-id="c4c68-195">"*Program.cs*" ファイルを開き、ファイルの先頭に次の追加の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-195">Open the *Program.cs* file and add the following additional `using` statements to the top of the file:</span></span>

[!code-csharp [ProgramUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L1-L9)]

<span data-ttu-id="c4c68-196">次に、さまざまな資産のパスを定義します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-196">Next, define the paths of the various assets.</span></span> 

1. <span data-ttu-id="c4c68-197">最初に、`GetAbsolutePath` メソッドを `Program` クラスの `Main` メソッドの下に追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-197">First, add the `GetAbsolutePath` method below the `Main` method in the `Program` class.</span></span> 

    [!code-csharp [GetAbsolutePath](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L66-L74)]

1. <span data-ttu-id="c4c68-198">次に、`Main` メソッド内にアセットの場所を格納するフィールドを作成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-198">Then, inside the `Main` method, create fields to store the location of your assets:</span></span>

    [!code-csharp [AssetDefinition](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L17-L21)]

<span data-ttu-id="c4c68-199">入力データと予測クラスを格納する新しいディレクトリをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-199">Add a new directory to your project to store your input data and prediction classes.</span></span>

<span data-ttu-id="c4c68-200">**ソリューション エクスプローラー**で、プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-200">In **Solution Explorer**, right-click the project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="c4c68-201">ソリューション エクスプローラーに新しいフォルダーが表示されたら、"DataStructures" と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-201">When the new folder appears in the Solution Explorer, name it "DataStructures".</span></span>

<span data-ttu-id="c4c68-202">新しく作成した "*DataStructures*" ディレクトリに入力データ クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-202">Create your input data class in the newly created *DataStructures* directory.</span></span>

1. <span data-ttu-id="c4c68-203">**ソリューション エクスプローラー**で "*DataStructures*" ディレクトリを右クリックし、 **[追加]**  >  **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-203">In **Solution Explorer**, right-click the *DataStructures* directory, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="c4c68-204">**[新しい項目の追加]** ダイアログ ボックスで、 **[クラス]** を選択し、 **[名前]** フィールドを "*ImageNetData.cs*" に変更します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-204">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *ImageNetData.cs*.</span></span> <span data-ttu-id="c4c68-205">次に **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-205">Then, select the **Add** button.</span></span>
     
    <span data-ttu-id="c4c68-206">コード エディターで "*ImageNetData.cs*" ファイルが開きます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-206">The *ImageNetData.cs* file opens in the code editor.</span></span> <span data-ttu-id="c4c68-207">次の `using` ステートメントを "*ImageNetData.cs*" の先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-207">Add the following `using` statement to the top of *ImageNetData.cs*:</span></span>

    [!code-csharp [ImageNetDataUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/DataStructures/ImageNetData.cs#L1-L4)]

    <span data-ttu-id="c4c68-208">既存のクラス定義を削除し、`ImageNetData` クラスの次のコードを "*ImageNetData.cs*" ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-208">Remove the existing class definition and add the following code for the `ImageNetData` class to the *ImageNetData.cs* file:</span></span>
    
    [!code-csharp [ImageNetDataClass](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/DataStructures/ImageNetData.cs#L8-L23)]

    <span data-ttu-id="c4c68-209">`ImageNetData` は、入力画像データ クラスであり、次の <xref:System.String> フィールドがあります。</span><span class="sxs-lookup"><span data-stu-id="c4c68-209">`ImageNetData` is the input image data class and has the following <xref:System.String> fields:</span></span>

    - <span data-ttu-id="c4c68-210">`ImagePath` には、画像が保存されているパスが格納されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-210">`ImagePath` contains the path where the image is stored.</span></span>
    - <span data-ttu-id="c4c68-211">`Label` には、ファイルの名前が格納されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-211">`Label` contains the name of the file.</span></span>

    <span data-ttu-id="c4c68-212">また、`ImageNetData` には、指定された `imageFolder` パスに格納されている複数の画像ファイルを読み込み、それらを `ImageNetData` オブジェクトのコレクションとして返すメソッド `ReadFromFile` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="c4c68-212">Additionally, `ImageNetData` contains a method `ReadFromFile` which loads multiple image files stored in the `imageFolder` path specified and returns them as a collection of `ImageNetData` objects.</span></span>

<span data-ttu-id="c4c68-213">"*DataStructures*" ディレクトリに予測クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-213">Create your prediction class in the *DataStructures* directory.</span></span>

1. <span data-ttu-id="c4c68-214">**ソリューション エクスプローラー**で "*DataStructures*" ディレクトリを右クリックし、 **[追加]**  >  **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-214">In **Solution Explorer**, right-click the *DataStructures* directory, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="c4c68-215">**[新しい項目の追加]** ダイアログ ボックスで、 **[クラス]** を選択し、 **[名前]** フィールドを "*ImageNetPrediction.cs* "に変更します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-215">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *ImageNetPrediction.cs*.</span></span> <span data-ttu-id="c4c68-216">次に **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-216">Then, select the **Add** button.</span></span>

    <span data-ttu-id="c4c68-217">コード エディターで "*ImageNetPrediction.cs*" ファイルが開きます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-217">The *ImageNetPrediction.cs* file opens in the code editor.</span></span> <span data-ttu-id="c4c68-218">"*ImageNetPrediction.cs*" の先頭に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-218">Add the following `using` statement to the top of *ImageNetPrediction.cs*:</span></span>

    [!code-csharp [ImageNetPredictionUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/DataStructures/ImageNetPrediction.cs#L1)]

    <span data-ttu-id="c4c68-219">既存のクラス定義を削除し、`ImageNetPrediction` クラスの次のコードを "*ImageNetPrediction.cs*" ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-219">Remove the existing class definition and add the following code for the `ImageNetPrediction` class to the *ImageNetPrediction.cs* file:</span></span>

    [!code-csharp [ImageNetPredictionClass](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/DataStructures/ImageNetPrediction.cs#L5-L9)]

    <span data-ttu-id="c4c68-220">`ImageNetPrediction` は予測データ クラスであり、次の `float[]` フィールドがあります。</span><span class="sxs-lookup"><span data-stu-id="c4c68-220">`ImageNetPrediction` is the prediction data class and has the following `float[]` field:</span></span>

    - <span data-ttu-id="c4c68-221">`PredictedLabel` には、画像で検出された各境界ボックスの寸法、物体らしさのスコア、およびクラスの確率が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-221">`PredictedLabel` contains the dimensions, objectness score and class probabilities for each of the bounding boxes detected in an image.</span></span>

### <a name="initialize-variables-in-main"></a><span data-ttu-id="c4c68-222">Main で変数を初期化する</span><span class="sxs-lookup"><span data-stu-id="c4c68-222">Initialize variables in Main</span></span>

<span data-ttu-id="c4c68-223">[MLContext クラス](xref:Microsoft.ML.MLContext)は、すべての ML.NET 操作の開始点で、`mlContext` を初期化することで、モデル作成ワークフローのオブジェクト間で共有できる新しい ML.NET 環境が作成されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-223">The [MLContext class](xref:Microsoft.ML.MLContext) is a starting point for all ML.NET operations, and initializing `mlContext` creates a new ML.NET environment that can be shared across the model creation workflow objects.</span></span> <span data-ttu-id="c4c68-224">これは Entity Framework における `DBContext` と概念的には同じです。</span><span class="sxs-lookup"><span data-stu-id="c4c68-224">It's similar, conceptually, to `DBContext` in Entity Framework.</span></span>

<span data-ttu-id="c4c68-225">"*Program.cs*" の `Main` メソッドで `outputFolder` フィールドの下に次の行を追加して、`MLContext` の新しいインスタンスで `mlContext` 変数を初期化します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-225">Initialize the `mlContext` variable with a new instance of `MLContext` by adding the following line to the `Main` method of *Program.cs* below the `outputFolder` field.</span></span>

[!code-csharp [InitMLContext](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L24)]

### <a name="add-helper-methods"></a><span data-ttu-id="c4c68-226">ヘルパー メソッドを追加する</span><span class="sxs-lookup"><span data-stu-id="c4c68-226">Add Helper Methods</span></span>

<span data-ttu-id="c4c68-227">モデルによって予測が行われ (一般的にスコアリングと呼ばれます)、出力が処理された後は、境界ボックスが画像の上に描画される必要があります。</span><span class="sxs-lookup"><span data-stu-id="c4c68-227">After the model has made a prediction, commonly referred to as scoring, and the outputs have been processed, the bounding boxes have to be drawn on the image.</span></span> <span data-ttu-id="c4c68-228">これを行うには、"*Program.cs*" の `GetAbsolutePath` メソッドの下に `DrawBoundingBox` というメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-228">To do so, add a method called `DrawBoundingBox` below the `GetAbsolutePath` method insode of *Program.cs*.</span></span>

```csharp
private static void DrawBoundingBox(string inputImageLocation, string outputImageLocation, string imageName, IList<YoloBoundingBox> filteredBoundingBoxes)
{

}
```

<span data-ttu-id="c4c68-229">まず、`DrawBoundingBox` メソッドで画像を読み込み、高さと幅の寸法を取得します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-229">First, load the image and get the height and width dimensions in the `DrawBoundingBox` method.</span></span>

[!code-csharp [LoadImage](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L78-L81)]

<span data-ttu-id="c4c68-230">次に、for-each ループを作成し、モデルにより検出される各境界ボックスに反復処理を行います。</span><span class="sxs-lookup"><span data-stu-id="c4c68-230">Then, create a for-each loop to iterate over each of the bounding boxes detected by the model.</span></span>

```csharp
foreach (var box in filteredBoundingBoxes)
{

}
```

<span data-ttu-id="c4c68-231">for-each ループ内で、境界ボックスの寸法を取得します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-231">Inside of the for-each loop, get the dimensions of the bounding box.</span></span>

[!code-csharp [GetBBoxDimensions](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L86-L89)]

<span data-ttu-id="c4c68-232">境界ボックスの寸法は `416 x 416` のモデル入力に対応しているため、画像の実際のサイズに合わせて境界ボックスの寸法を拡大縮小します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-232">Because the dimensions of the bounding box correspond to the model input of `416 x 416`, scale the bounding box dimensions to match the actual size of the image.</span></span>

[!code-csharp [ScaleImage](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L92-L95)]

<span data-ttu-id="c4c68-233">次に、各境界ボックスの上に表示されるテキストのテンプレートを定義します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-233">Then, define a template for text that will apear above each bounding box.</span></span> <span data-ttu-id="c4c68-234">テキストには、対応する境界ボックス内のオブジェクトのクラスのほか、信頼度を含めます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-234">The text will contain the class of the object inside of the respective bounding box as well as the confidence.</span></span>

[!code-csharp [DefineBBoxText](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L98)]

<span data-ttu-id="c4c68-235">画像を描画するために、[`Graphics`](xref:System.Drawing.Graphics) オブジェクトに変換します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-235">In order to draw on the image, convert it to a [`Graphics`](xref:System.Drawing.Graphics) object.</span></span>

```csharp
using (Graphics thumbnailGraphic = Graphics.FromImage(image))
{
    
}
```

<span data-ttu-id="c4c68-236">`using` コード ブロック内で、グラフィックの [`Graphics`](xref:System.Drawing.Graphics) オブジェクト設定を調整します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-236">Inside the `using` code block, tune the graphic's [`Graphics`](xref:System.Drawing.Graphics) object settings.</span></span>

[!code-csharp [TuneGraphicSettings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L102-L104)]

<span data-ttu-id="c4c68-237">その下で、テキストと境界ボックスのフォントと色のオプションを設定します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-237">Below that, set the font and color options for the text and bounding box.</span></span>

[!code-csharp [SetColorOptions](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L106-L114)]

<span data-ttu-id="c4c68-238">[`FillRectangle`](xref:System.Drawing.Graphics.FillRectangle*) メソッドを使用してテキストを含めるために、境界ボックスの上に四角形を作成して塗りつぶします。</span><span class="sxs-lookup"><span data-stu-id="c4c68-238">Create and fill a rectangle above the bounding box to contain the text using the [`FillRectangle`](xref:System.Drawing.Graphics.FillRectangle*) method.</span></span> <span data-ttu-id="c4c68-239">これにより、テキストにコントラストを付け、読みやすさを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-239">This will help contrast the text and improve readability.</span></span>

[!code-csharp [DrawTextBackground](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L117)]

<span data-ttu-id="c4c68-240">次に、[`DrawString`](xref:System.Drawing.Graphics.DrawString*) メソッドと [`DrawRectangle`](xref:System.Drawing.Graphics.DrawRectangle*) メソッドを使用して、画像の上にテキストと境界ボックスを描画します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-240">Then, Draw the text and bounding box on the image using the [`DrawString`](xref:System.Drawing.Graphics.DrawString*) and [`DrawRectangle`](xref:System.Drawing.Graphics.DrawRectangle*) methods.</span></span>

[!code-csharp [DrawClassAndBBox](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L118-L121)]

<span data-ttu-id="c4c68-241">for-each ループの外側に、画像を `outputDirectory` に保存するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-241">Outside of the for-each loop, add code to save the images in the `outputDirectory`.</span></span>

[!code-csharp [SaveImage](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L125-L130)]

<span data-ttu-id="c4c68-242">アプリケーションが実行時に想定どおりに予測を行っているという追加のフィードバックを取得するには、"*Program.cs*" ファイルの `DrawBoundingBox` メソッドの下に `LogDetectedObjects` というメソッドを追加して、検出されたオブジェクトをコンソールに出力します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-242">To get additional feedback that the application is making predictions as expected at runtime, add a method called `LogDetectedObjects` below the `DrawBoundingBox` method in the *Program.cs* file to output the detected objects to the console.</span></span>

[!code-csharp [LogOuptuts](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L133-L143)]

<span data-ttu-id="c4c68-243">これらのメソッドは両方とも、モデルから出力が生成され、処理されるときに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-243">Both of these methods will be useful when the model has produced outputs and those have been processed.</span></span> <span data-ttu-id="c4c68-244">最初に、モデルの出力を処理する機能を作成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-244">First though, create the functionality to process the model outputs.</span></span>

## <a name="create-a-parser-to-post-process-model-outputs"></a><span data-ttu-id="c4c68-245">モデル出力を後処理するパーサーを作成する</span><span class="sxs-lookup"><span data-stu-id="c4c68-245">Create a parser to post-process model outputs</span></span>

<span data-ttu-id="c4c68-246">このモデルでは、画像を `13 x 13` グリッドに分割します。各グリッド セルは `32px x 32px` です。</span><span class="sxs-lookup"><span data-stu-id="c4c68-246">The model segments an image into a `13 x 13` grid, where each grid cell is `32px x 32px`.</span></span> <span data-ttu-id="c4c68-247">各グリッド セルには、5 個の潜在的なオブジェクト境界ボックスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-247">Each grid cell contains 5 potential object bounding boxes.</span></span> <span data-ttu-id="c4c68-248">境界ボックスには 25 個の要素があります。</span><span class="sxs-lookup"><span data-stu-id="c4c68-248">A bounding box has  25 elements:</span></span>

![](./media/object-detection-onnx/model-output-description.png)

- <span data-ttu-id="c4c68-249">`x` 関連付けられているグリッド セルを基準にした、境界ボックスの中心の x 位置。</span><span class="sxs-lookup"><span data-stu-id="c4c68-249">`x` the x position of the bounding box center relative to the grid cell it's associated with.</span></span>
- <span data-ttu-id="c4c68-250">`y` 関連付けられているグリッド セルを基準にした、境界ボックスの中心の y 位置。</span><span class="sxs-lookup"><span data-stu-id="c4c68-250">`y` the y position of the bounding box center relative to the grid cell it's associated with.</span></span>
- <span data-ttu-id="c4c68-251">`w` 境界ボックスの幅。</span><span class="sxs-lookup"><span data-stu-id="c4c68-251">`w` the width of the bounding box.</span></span>
- <span data-ttu-id="c4c68-252">`h` 境界ボックスの高さ。</span><span class="sxs-lookup"><span data-stu-id="c4c68-252">`h` the height of the bounding box.</span></span> 
- <span data-ttu-id="c4c68-253">`o` オブジェクトが境界ボックス内に存在する信頼度の値。これは物体らしさのスコアとも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-253">`o` the confidence value that an object exists within the bounding box, also known as objectness score.</span></span>
- <span data-ttu-id="c4c68-254">`p1-p20` モデルによって予測される 20 個のクラスそれぞれのクラスの確率。</span><span class="sxs-lookup"><span data-stu-id="c4c68-254">`p1-p20` class probabilities for each of the 20 classes predicted by the model.</span></span>

<span data-ttu-id="c4c68-255">5 個の境界ボックスそれぞれを 25 個の要素で記述し、合計すると、各グリッド セルに 125 個の要素が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-255">In total, the 25 elements describing each of the 5 bounding boxes make up the 125 elements contained in each grid cell.</span></span>

<span data-ttu-id="c4c68-256">事前トレーニング済みの ONNX モデルによって生成される出力は、長さ `21125` の float 型の配列であり、寸法が `125 x 13 x 13` のテンソルの要素を表します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-256">The output generated by the pre-trained ONNX model is a float array of length `21125`, representing the elements of a tensor with dimensions `125 x 13 x 13`.</span></span> <span data-ttu-id="c4c68-257">モデルによって生成される予測をテンソルに変換するには、処理後の作業が必要です。</span><span class="sxs-lookup"><span data-stu-id="c4c68-257">In order to transform the predictions generated by the model into a tensor, some post-processing work is required.</span></span> <span data-ttu-id="c4c68-258">これを行うには、出力を解析する一連のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-258">To do so, create a set of classes to help parse the output.</span></span>

<span data-ttu-id="c4c68-259">新しいディレクトリをプロジェクトに追加し、一連のパーサー クラスを編成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-259">Add a new directory to your project to organize the set of parser classes.</span></span>

1. <span data-ttu-id="c4c68-260">**ソリューション エクスプローラー**で、プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-260">In **Solution Explorer**, right-click the project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="c4c68-261">ソリューション エクスプローラーに新しいフォルダーが表示されたら、"YoloParser" と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-261">When the new folder appears in the Solution Explorer, name it "YoloParser".</span></span>

### <a name="create-bounding-boxes-and-dimensions"></a><span data-ttu-id="c4c68-262">境界ボックスと寸法を作成する</span><span class="sxs-lookup"><span data-stu-id="c4c68-262">Create bounding boxes and dimensions</span></span>

<span data-ttu-id="c4c68-263">モデルから出力されるデータには、画像内のオブジェクトの境界ボックスの座標と寸法が含まれています。</span><span class="sxs-lookup"><span data-stu-id="c4c68-263">The data output by the model contains coordinates and dimensions of the bounding boxes of objects within the image.</span></span> <span data-ttu-id="c4c68-264">寸法の基底クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-264">Create a base class for dimensions.</span></span>

1. <span data-ttu-id="c4c68-265">**ソリューション エクスプローラー**で、"*YoloParser*" ディレクトリを右クリックし、 **[追加]**  >  **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-265">In **Solution Explorer**, right-click the *YoloParser* directory, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="c4c68-266">**[新しい項目の追加]** ダイアログボックスで **[クラス]** を選択し、 **[名前]** フィールドを "*DimensionsBase.cs*" に変更します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-266">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *DimensionsBase.cs*.</span></span> <span data-ttu-id="c4c68-267">次に **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-267">Then, select the **Add** button.</span></span>

    <span data-ttu-id="c4c68-268">コード エディターで "*DimensionsBase.cs*" ファイルが開きます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-268">The *DimensionsBase.cs* file opens in the code editor.</span></span> <span data-ttu-id="c4c68-269">すべての `using` ステートメントと既存のクラス定義を削除します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-269">Remove all `using` statements and existing class definition.</span></span> 

    <span data-ttu-id="c4c68-270">`DimensionsBase` クラスの次のコードを "*DimensionsBase.cs*" ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-270">Add the following code for the `DimensionsBase` class to the *DimensionsBase.cs* file:</span></span>

    [!code-csharp [DimensionsBaseClass](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/DimensionsBase.cs#L3-L9)]

    <span data-ttu-id="c4c68-271">`DimensionsBase` には、次の `float` フィールドがあります。</span><span class="sxs-lookup"><span data-stu-id="c4c68-271">`DimensionsBase` has the following `float` fields:</span></span>

    - <span data-ttu-id="c4c68-272">`X` には、x 軸に沿ったオブジェクトの位置が格納されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-272">`X` contains the position of the object along the x-axis.</span></span>
    - <span data-ttu-id="c4c68-273">`Y` には、y 軸に沿ったオブジェクトの位置が格納されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-273">`Y` contains the position of the object along the y-axis.</span></span>
    - <span data-ttu-id="c4c68-274">`Height` には、オブジェクトの高さが格納されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-274">`Height` contains the height of the object.</span></span>
    - <span data-ttu-id="c4c68-275">`Width` には、オブジェクトの幅が格納されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-275">`Width` contains the width of the object.</span></span>

<span data-ttu-id="c4c68-276">次に、境界ボックスのクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-276">Next, create a class for your bounding boxes.</span></span>

1. <span data-ttu-id="c4c68-277">**ソリューション エクスプローラー**で、"*YoloParser*" ディレクトリを右クリックし、 **[追加]**  >  **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-277">In **Solution Explorer**, right-click the *YoloParser* directory, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="c4c68-278">**[新しい項目の追加]** ダイアログボックスで **[クラス]** を選択し、 **[名前]** フィールドを "*YoloBoundingBox.cs*" に変更します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-278">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *YoloBoundingBox.cs*.</span></span> <span data-ttu-id="c4c68-279">次に **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-279">Then, select the **Add** button.</span></span>

    <span data-ttu-id="c4c68-280">コード エディターで "*YoloBoundingBox.cs*" ファイルが開きます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-280">The *YoloBoundingBox.cs* file opens in the code editor.</span></span> <span data-ttu-id="c4c68-281">"*YoloBoundingBox.cs*" の先頭に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-281">Add the following `using` statement to the top of *YoloBoundingBox.cs*:</span></span>

    [!code-csharp [YoloBoundingBoxUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloBoundingBox.cs#L1)]

    <span data-ttu-id="c4c68-282">既存のクラス定義のすぐ上に、`BoundingBoxDimensions` という新しいクラス定義を追加します。これは `DimensionsBase` クラスから継承して、それぞれの境界ボックスの寸法を格納します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-282">Just above the existing class definition, add a new class definition called `BoundingBoxDimensions` which inherits from the `DimensionsBase` class to contain the dimensions of the respective bounding box.</span></span>

    [!code-csharp [BoundingBoxDimClass](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloBoundingBox.cs#L5)]

    <span data-ttu-id="c4c68-283">既存の `YoloBoundingBox` クラス定義を削除し、`YoloBoundingBox` クラスの次のコードを "*YoloBoundingBox.cs*" ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-283">Remove the existing `YoloBoundingBox` class definition and add the following code for the `YoloBoundingBox` class to the *YoloBoundingBox.cs* file:</span></span>

    [!code-csharp [YoloBoundingBoxClass](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloBoundingBox.cs#L7-L21)]
    
    <span data-ttu-id="c4c68-284">`YoloBoundingBox` には次のフィールドがあります。</span><span class="sxs-lookup"><span data-stu-id="c4c68-284">`YoloBoundingBox` has the following fields:</span></span>

    - <span data-ttu-id="c4c68-285">`Dimensions` には、境界ボックスの寸法が格納されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-285">`Dimensions` contains dimensions of the bounding box.</span></span>
    - <span data-ttu-id="c4c68-286">`Label` には、境界ボックス内で検出されるオブジェクトのクラスが格納されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-286">`Label` contains the class of object detected within the bounding box.</span></span>
    - <span data-ttu-id="c4c68-287">`Confidence` には、クラスの信頼度が格納されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-287">`Confidence` contains the confidence of the class.</span></span>
    - <span data-ttu-id="c4c68-288">`Rect` には、境界ボックスの寸法の四角形の表現が格納されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-288">`Rect` contains the rectangle representation of the bounding box's dimensions.</span></span>
    - <span data-ttu-id="c4c68-289">`BoxColor` には、画像への描画に使用される各クラスに関連付けられた色が格納されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-289">`BoxColor` contains the color associated with the respective class used to draw on the image.</span></span>

### <a name="create-the-parser"></a><span data-ttu-id="c4c68-290">パーサーを作成する</span><span class="sxs-lookup"><span data-stu-id="c4c68-290">Create the parser</span></span>

<span data-ttu-id="c4c68-291">寸法と境界ボックスのクラスが作成されたので、次はパーサーを作成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-291">Now that the classes for dimensions and bounding boxes are created, it's time to create the parser.</span></span>

1. <span data-ttu-id="c4c68-292">**ソリューション エクスプローラー**で、"*YoloParser*" ディレクトリを右クリックし、 **[追加]**  >  **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-292">In **Solution Explorer**, right-click the *YoloParser* directory, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="c4c68-293">**[新しい項目の追加]** ダイアログボックスで **[クラス]** を選択し、 **[名前]** フィールドを "*YoloOutputParser.cs*" に変更します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-293">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *YoloOutputParser.cs*.</span></span> <span data-ttu-id="c4c68-294">次に **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-294">Then, select the **Add** button.</span></span>

    <span data-ttu-id="c4c68-295">コード エディターで "*YoloOutputParser.cs*" ファイルが開きます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-295">The *YoloOutputParser.cs* file opens in the code editor.</span></span> <span data-ttu-id="c4c68-296">"*YoloOutputParser.cs*" の先頭に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-296">Add the following `using` statement to the top of *YoloOutputParser.cs*:</span></span>

    [!code-csharp [YoloParserUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L1-L4)]

    <span data-ttu-id="c4c68-297">既存の `YoloOutputParser` クラス定義内に、画像の各セルの寸法を格納する入れ子になったクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-297">Inside the existing `YoloOutputParser` class definition, add a nested class that contains the dimensions of each of the cells in the image.</span></span> <span data-ttu-id="c4c68-298">`YoloOutputParser` クラス定義の先頭で `DimensionsBase` クラスから継承する `CellDimensions` クラスに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-298">Add the following code for the `CellDimensions` class which inherits from the `DimensionsBase` class at the top of the `YoloOutputParser` class definition.</span></span>

    [!code-csharp [YoloParserUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L10)]

1. <span data-ttu-id="c4c68-299">`YoloOutputParser` クラス定義内に、次の定数とフィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-299">Inside the `YoloOutputParser` class definition, add the following constant and fields.</span></span>

    [!code-csharp [ParserVarDefinitions](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L12-L21)]    

    - <span data-ttu-id="c4c68-300">`ROW_COUNT` は、画像が分割されるグリッドの行数です。</span><span class="sxs-lookup"><span data-stu-id="c4c68-300">`ROW_COUNT` is the number of rows in the grid the image is divided into.</span></span>
    - <span data-ttu-id="c4c68-301">`COL_COUNT` は、画像が分割されるグリッドの列数です。</span><span class="sxs-lookup"><span data-stu-id="c4c68-301">`COL_COUNT` is the number of columns in the grid the image is divided into.</span></span>
    - <span data-ttu-id="c4c68-302">`CHANNEL_COUNT` は、グリッドの 1 つのセルに含まれる値の合計数です。</span><span class="sxs-lookup"><span data-stu-id="c4c68-302">`CHANNEL_COUNT` is the total number of values contained in one cell of the grid.</span></span>
    - <span data-ttu-id="c4c68-303">`BOXES_PER_CELL` は、セル内の境界ボックスの数です。</span><span class="sxs-lookup"><span data-stu-id="c4c68-303">`BOXES_PER_CELL` is the number of bounding boxes in a cell,</span></span>
    - <span data-ttu-id="c4c68-304">`BOX_INFO_FEATURE_COUNT` は、ボックス内に含まれる特徴の数です (x、y、高さ、幅、信頼度)。</span><span class="sxs-lookup"><span data-stu-id="c4c68-304">`BOX_INFO_FEATURE_COUNT` is the number of features contained within a box (x,y,height,width,confidence).</span></span>
    - <span data-ttu-id="c4c68-305">`CLASS_COUNT` は、各境界ボックスに含まれるクラス予測の数です。</span><span class="sxs-lookup"><span data-stu-id="c4c68-305">`CLASS_COUNT` is the number of class predictions contained in each bounding box.</span></span>
    - <span data-ttu-id="c4c68-306">`CELL_WIDTH` は、画像グリッド内の 1 つのセルの幅です。</span><span class="sxs-lookup"><span data-stu-id="c4c68-306">`CELL_WIDTH` is the width of one cell in the image grid.</span></span>
    - <span data-ttu-id="c4c68-307">`CELL_HEIGHT` は、画像グリッド内の 1 つのセルの高さです。</span><span class="sxs-lookup"><span data-stu-id="c4c68-307">`CELL_HEIGHT` is the height of one cell in the image grid.</span></span>
    - <span data-ttu-id="c4c68-308">`channelStride` は、グリッド内の現在のセルの開始位置です。</span><span class="sxs-lookup"><span data-stu-id="c4c68-308">`channelStride` is the starting position of the current cell in the grid.</span></span>

    <span data-ttu-id="c4c68-309">モデルによって画像のスコアが付けられると、`416px x 416px` の入力が `13 x 13` のサイズのセルのグリッドに分割されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-309">When the model scores an image, it divides the `416px x 416px`input into a grid of cells the size of `13 x 13`.</span></span> <span data-ttu-id="c4c68-310">各セルには `32px x 32px` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-310">Each cell contains is `32px x 32px`.</span></span> <span data-ttu-id="c4c68-311">各セルには 5 個の境界ボックスがあり、それぞれに 5 つの特徴 (x、y、幅、高さ、信頼度) があります。</span><span class="sxs-lookup"><span data-stu-id="c4c68-311">Within each cell, there are 5 bounding boxes each containing 5 features (x, y, width, height, confidence).</span></span> <span data-ttu-id="c4c68-312">また、各境界ボックスには、各クラスの確率 (この例では 20) が格納されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-312">In addition, each bounding box contains the probability of each of the classes which in this case is 20.</span></span> <span data-ttu-id="c4c68-313">したがって、各セルには 125 個の情報が含まれます (5 つの特徴 + 20 個のクラスの確率)。</span><span class="sxs-lookup"><span data-stu-id="c4c68-313">Therefore, each cell contains 125 pieces of information (5 features + 20 class probabilities).</span></span> 

<span data-ttu-id="c4c68-314">5 個の境界ボックスすべてについて、`channelStride` の下にアンカーのリストを作成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-314">Create a list of anchors below `channelStride` for all 5 bounding boxes:</span></span>

[!code-csharp [ParserAnchors](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L23-L26)]   

<span data-ttu-id="c4c68-315">アンカーは、境界ボックスの定義済みの高さと幅の比率です。</span><span class="sxs-lookup"><span data-stu-id="c4c68-315">Anchors are pre-defined height and width ratios of bounding boxes.</span></span> <span data-ttu-id="c4c68-316">モデルによって検出されるほとんどのオブジェクトまたはクラスには、同様の比率があります。</span><span class="sxs-lookup"><span data-stu-id="c4c68-316">Most object or classes detected by a model have similar ratios.</span></span> <span data-ttu-id="c4c68-317">これは、境界ボックスを作成するときに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-317">This is valuable when it comes to creating bounding boxes.</span></span> <span data-ttu-id="c4c68-318">境界ボックスの予測ではなく、事前に定義された寸法からのオフセットが計算されるため、境界ボックスを予測するために必要な計算が少なくなります。</span><span class="sxs-lookup"><span data-stu-id="c4c68-318">Instead of predicting the bounding boxes, the offset from the pre-defined dimensions is calculated therefore reducing the computation required to predict the bounding box.</span></span> <span data-ttu-id="c4c68-319">通常、これらのアンカーの比率は、使用されるデータセットに基づいて計算されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-319">Typically these anchor ratios are calculated based on the dataset used.</span></span> <span data-ttu-id="c4c68-320">この場合は、データセットは既知であり、値が事前に計算されているため、アンカーをハードコーディングすることができます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-320">In this case because the dataset is known and the values have been pre-computed, the anchors can be hard-coded.</span></span>

<span data-ttu-id="c4c68-321">次に、モデルで予測するラベルまたはクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-321">Next, define the labels or classes that the model will predict.</span></span> <span data-ttu-id="c4c68-322">このモデルでは、元の YOLOv2 モデルによって予測されるクラスの合計数のサブセットである 20 個のクラスを予測します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-322">This model predicts 20 classes which is a subset of the total number of classes predicted by the original YOLOv2 model.</span></span>

<span data-ttu-id="c4c68-323">`anchors` の下にラベルのリストを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-323">Add your list of labels below the `anchors`.</span></span> 

[!code-csharp [ParserLabels](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L28-L34)]

<span data-ttu-id="c4c68-324">各クラスには色が関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="c4c68-324">There are colors associated with each of the classes.</span></span> <span data-ttu-id="c4c68-325">`labels` の下にクラスの色を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-325">Assign your class colors below your `labels`:</span></span>

[!code-csharp [ParserColors](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L36-L59)]

### <a name="create-helper-functions"></a><span data-ttu-id="c4c68-326">ヘルパー関数を作成する</span><span class="sxs-lookup"><span data-stu-id="c4c68-326">Create helper functions</span></span>

<span data-ttu-id="c4c68-327">後処理フェーズには、関係する一連の手順が含まれています。</span><span class="sxs-lookup"><span data-stu-id="c4c68-327">There are a series of steps involved in the post-processing phase.</span></span> <span data-ttu-id="c4c68-328">このためには、いくつかのヘルパー メソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-328">To help with that, several helper methods can be employed.</span></span> 

<span data-ttu-id="c4c68-329">パーサーによって使用されるヘルパー メソッドは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c4c68-329">The helper methods used in by the parser are:</span></span>

- <span data-ttu-id="c4c68-330">`Sigmoid` では、0 から 1 までの数値を出力するシグモイド関数が適用されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-330">`Sigmoid` applies the sigmoid function that outputs a number between 0 and 1.</span></span>
- <span data-ttu-id="c4c68-331">`Softmax` では、入力ベクトルが確率分布に正規化されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-331">`Softmax` normalizes an input vector into a probability distribution.</span></span>
- <span data-ttu-id="c4c68-332">`GetOffset` では、1 次元モデルの出力の要素が、`125 x 13 x 13` テンソルの対応する位置にマップされます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-332">`GetOffset` maps elements in the one-dimensional model output to the corresponding position in a `125 x 13 x 13` tensor.</span></span>
- <span data-ttu-id="c4c68-333">`ExtractBoundingBoxes` では、モデル出力から `GetOffset` メソッドを使用して、境界ボックスの寸法が抽出されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-333">`ExtractBoundingBoxes` extracts the bounding box dimensions using the `GetOffset` method from the model output.</span></span>
- <span data-ttu-id="c4c68-334">`GetConfidence` では、モデルで、オブジェクトが検出されたことをどのくらい確信しているかを示す信頼度が抽出され、`Sigmoid` 関数を使用してパーセンテージ値に変換されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-334">`GetConfidence` extracts the confidence value which states how sure the model is that it has detected an object and uses the `Sigmoid` function to turn it into a percentage.</span></span>
- <span data-ttu-id="c4c68-335">`MapBoundingBoxToCell` では、境界ボックスの寸法を使用して、画像内の各セルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-335">`MapBoundingBoxToCell` uses the bounding box dimensions and maps them onto its respective cell within the image.</span></span>
- <span data-ttu-id="c4c68-336">`ExtractClasses` では、`GetOffset` メソッドを使用してモデルの出力から境界ボックスのクラス予測が抽出され、`Softmax` メソッドを使用して確率分布に変換されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-336">`ExtractClasses` extracts the class predictions for the bounding box from the model output using the `GetOffset` method and turns them into a probability distribution using the `Softmax` method.</span></span>
- <span data-ttu-id="c4c68-337">`GetTopResult` では、確率が最も高い予測クラスのリストからクラスが選択されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-337">`GetTopResult` selects the class from the list of predicted classes with the highest probability.</span></span>
- <span data-ttu-id="c4c68-338">`IntersectionOverUnion` では、確率が低い境界ボックスの重なりがフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-338">`IntersectionOverUnion` filters overlapping bounding boxes with lower probabilities.</span></span>

<span data-ttu-id="c4c68-339">すべてのヘルパー メソッドのコードを `classColors` のリストの下に追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-339">Add the code for all the helper methods below your list of `classColors`.</span></span>

[!code-csharp [ParserHelperMethods](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L61-L151)]

<span data-ttu-id="c4c68-340">すべてのヘルパー メソッドを定義したら、次はそれらを使用してモデルの出力を処理します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-340">Once you have defined all of the helper methods, it's time to use them to process the model output.</span></span>

<span data-ttu-id="c4c68-341">`IntersectionOverUnion` メソッドの下に、モデルによって生成される出力を処理する `ParseOutputs` メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-341">Below the `IntersectionOverUnion` method, create the `ParseOutputs` method to process the output generated by the model.</span></span>

```csharp
public IList<YoloBoundingBox> ParseOutputs(float[] yoloModelOutputs, float threshold = .3F)
{

}
```
    
<span data-ttu-id="c4c68-342">境界ボックスを格納するリストを作成し、`ParseOutputs` メソッド内に変数を定義します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-342">Create a list to store your bounding boxes and define variables inside the `ParseOutputs` method.</span></span>

[!code-csharp [BBoxList](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L155)]

<span data-ttu-id="c4c68-343">各画像は、`13 x 13` セルのグリッドに分割されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-343">Each image is divided into a grid of `13 x 13` cells.</span></span> <span data-ttu-id="c4c68-344">各セルには 5 個の境界ボックスがあります。</span><span class="sxs-lookup"><span data-stu-id="c4c68-344">Each cell contains five bounding boxes.</span></span> <span data-ttu-id="c4c68-345">`boxes` 変数の下に、各セルのすべてのボックスを処理するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-345">Below the `boxes` variable, add code to process all of the boxes in each of the cells.</span></span>

```csharp
for (int row = 0; row < ROW_COUNT; row++)
{
    for (int column = 0; column < COL_COUNT; column++)
    {
        for (int box = 0; box < BOXES_PER_CELL; box++)
        {

        }
    }
}
```

<span data-ttu-id="c4c68-346">最も内側のループ内で、1 次元モデルの出力に含まれる現在のボックスの開始位置を計算します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-346">Inside the inner-most loop, calculate the starting position of the current box within the one-dimensional model output.</span></span>

[!code-csharp [ChannelDef](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L163)]

<span data-ttu-id="c4c68-347">その直下で、`ExtractBoundingBoxDimensions` メソッドを使用して、現在の境界ボックスの寸法を取得します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-347">Directly below that, use the `ExtractBoundingBoxDimensions` method to get the dimensions of the current bounding box.</span></span>

[!code-csharp [GetBBoxDims](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L165)]

<span data-ttu-id="c4c68-348">次に、`GetConfidence` メソッドを使用して、現在の境界ボックスの信頼度を取得します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-348">Then, use the `GetConfidence` method to get the confidence for the current bounding box.</span></span>

[!code-csharp [GetConfidence](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L167)]

<span data-ttu-id="c4c68-349">その後、`MapBoundingBoxToCell` メソッドを使用して、現在の境界ボックスを現在処理中のセルにマップします。</span><span class="sxs-lookup"><span data-stu-id="c4c68-349">After that, use the `MapBoundingBoxToCell` method to map the current bounding box to the current cell being processed.</span></span>

[!code-csharp [MapBoundingBoxes](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L169)]

<span data-ttu-id="c4c68-350">さらに処理を進める前に、信頼度の値が、指定されたしきい値より大きいかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-350">Before doing any further processing, check whether your confidence value is greater than the threshold provided.</span></span> <span data-ttu-id="c4c68-351">そうでない場合は、次の境界ボックスを処理します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-351">If not, process the next bounding box.</span></span>

[!code-csharp [CheckThreshold1](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L171-L172)]

<span data-ttu-id="c4c68-352">それ以外の場合は、出力の処理を続行します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-352">Otherwise, continue processing the output.</span></span> <span data-ttu-id="c4c68-353">次の手順では、`ExtractClasses` メソッドを使用して、現在の境界ボックスの予測クラスの確率分布を取得します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-353">The next step is to get the probability distribution of the predicted classes for the current bounding box using the `ExtractClasses` method.</span></span>

[!code-csharp [ExtractClasses](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L174)]

<span data-ttu-id="c4c68-354">次に、`GetTopResult` メソッドを使用して、現在のボックスの確率が最も高いクラスの値とインデックスを取得し、スコアを計算します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-354">Then, use the `GetTopResult` method to get the value and index of the class with the highest probability for the current box and compute its score.</span></span>

[!code-csharp [GetTopResult](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L176-L177)]

<span data-ttu-id="c4c68-355">ここでも `topScore` を使用して、指定されたしきい値を超える境界ボックスのみを保持します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-355">Use the `topScore` to once again keep only those bounding boxes that are above the specified threshold.</span></span>

[!code-csharp [CheckThreshold2](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L179-L180)]

<span data-ttu-id="c4c68-356">最後に、現在の境界ボックスがしきい値を超えている場合は、新しい `BoundingBox` オブジェクトを作成して `boxes` リストに追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-356">Finally, if the current bounding box exceeds the threshold, create a new `BoundingBox` object and add it to the `boxes` list.</span></span>

[!code-csharp [AddBBoxToList](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L182-L194)]

<span data-ttu-id="c4c68-357">画像内のすべてのセルが処理されたら、`boxes` リストを返します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-357">Once all cells in the image have been processed, return the `boxes` list.</span></span> <span data-ttu-id="c4c68-358">`ParseOutputs` メソッドの最も外側の for-loop ループの下に、次の return ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-358">Add the following return statement below the outer-most for-loop in the `ParseOutputs` method.</span></span>

[!code-csharp [ReturnBoxes](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L198)]

### <a name="filter-overlapping-boxes"></a><span data-ttu-id="c4c68-359">重複するボックスをフィルター処理する</span><span class="sxs-lookup"><span data-stu-id="c4c68-359">Filter overlapping boxes</span></span>

<span data-ttu-id="c4c68-360">これで、信頼度の高いすべての境界ボックスがモデルの出力から抽出されたので、重複している画像を削除するために追加のフィルター処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="c4c68-360">Now that all of the highly confident bounding boxes have been extracted from the model output, additional filtering needs to be done to remove overlapping images.</span></span> <span data-ttu-id="c4c68-361">`ParseOutputs` メソッドの下に `FilterBoundingBoxes` というメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-361">Add a method called `FilterBoundingBoxes` below the `ParseOutputs` method:</span></span>

```csharp
public IList<YoloBoundingBox> FilterBoundingBoxes(IList<YoloBoundingBox> boxes, int limit, float threshold)
{

}
```

<span data-ttu-id="c4c68-362">まずは、`FilterBoundingBoxes` メソッド内に、検出されたボックスのサイズと等しい配列を作成し、すべてのスロットをアクティブまたは処理の準備完了にマークします。</span><span class="sxs-lookup"><span data-stu-id="c4c68-362">Inside the `FilterBoundingBoxes` method, start off by creating an array equal to the size of detected boxes and marking all slots as active or ready for processing.</span></span>

[!code-csharp [InitActiveSlots](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L203-L207)]

<span data-ttu-id="c4c68-363">次に、境界ボックスが格納されたリストを信頼度に基づいて降順に並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-363">Then, sort the list containing your bounding boxes in descending order based on confidence.</span></span>

[!code-csharp [SortBoxes](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L209-L211)]

<span data-ttu-id="c4c68-364">その後、フィルター処理された結果を保持するリストを作成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-364">After that, create a list to hold the filtered results.</span></span>

[!code-csharp [InitFilterResult](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L213)]

<span data-ttu-id="c4c68-365">各境界ボックスを反復処理して、各境界ボックスの処理を開始します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-365">Begin processing each bounding box by iterating over each of the bounding boxes.</span></span>

```csharp
for (int i = 0; i < boxes.Count; i++)
{

}
```

<span data-ttu-id="c4c68-366">この for ループ内で、現在の境界ボックスを処理できるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-366">Inside of this for-loop, check whether the current bounding box can be processed.</span></span>

```csharp
if (isActiveBoxes[i])
{
    
}
```

<span data-ttu-id="c4c68-367">その場合は、境界ボックスを結果のリストに追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-367">If so, add the bounding box to the list of results.</span></span> <span data-ttu-id="c4c68-368">結果が、抽出されるボックスの指定された制限を超えると、ループから抜け出します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-368">If the results exceeds the specified limit of boxes to be extracted, break out of the loop.</span></span> <span data-ttu-id="c4c68-369">if ステートメント内に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-369">Add the following code inside the if-statement.</span></span>

[!code-csharp [AddFirstBBoxToResults](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L219-L223)]

<span data-ttu-id="c4c68-370">それ以外の場合は、隣接する境界ボックスを見ます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-370">Otherwise, look at the adjacent bounding boxes.</span></span> <span data-ttu-id="c4c68-371">ボックスの制限チェックの下に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-371">Add the following code below the box limit check.</span></span>

```csharp
for (var j = i + 1; j < boxes.Count; j++)
{

}
```

<span data-ttu-id="c4c68-372">最初のボックスと同様に、隣接するボックスがアクティブまたは処理の準備ができている場合は、`IntersectionOverUnion` メソッドを使用して、最初のボックスと 2 番目のボックスが、指定されたしきい値を超えているかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-372">Like the first box, if the adjacent box is active or ready to be processed, use the `IntersectionOverUnion` method to check whether the first box and the second box exceed the specified threshold.</span></span> <span data-ttu-id="c4c68-373">次のコードを、最も内側の for ループに追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-373">Add the following code to your inner-most for-loop.</span></span>

[!code-csharp [IOUBBox](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L227-L239)]

<span data-ttu-id="c4c68-374">隣接する境界ボックスを確認する最も内側の for ループの外側で、処理する残りの境界ボックスがあるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-374">Outside of the inner-most for-loop that checks adjacent bounding boxes, see whether there are any remaining bounding boxes to be processed.</span></span> <span data-ttu-id="c4c68-375">そうでない場合は、外側の for ループから抜け出します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-375">If not, break out of the outer for-loop.</span></span>

[!code-csharp [CheckActiveSlots](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L242-L243)]

<span data-ttu-id="c4c68-376">最後に、`FilterBoundingBoxes` メソッドの最初の for ループの外側で、次の結果を返します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-376">Finally, outside of the initial for-loop of the `FilterBoundingBoxes` method, return the results:</span></span>

[!code-csharp [ReturnFilteredBBox](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L246)]

<span data-ttu-id="c4c68-377">これでセットアップは終了です。</span><span class="sxs-lookup"><span data-stu-id="c4c68-377">Great!</span></span> <span data-ttu-id="c4c68-378">次は、このコードをモデルと共に使用して、スコアリングを行います。</span><span class="sxs-lookup"><span data-stu-id="c4c68-378">Now it's time to use this code along with the model for scoring.</span></span>

## <a name="use-the-model-for-scoring"></a><span data-ttu-id="c4c68-379">スコアリングにモデルを使用する</span><span class="sxs-lookup"><span data-stu-id="c4c68-379">Use the model for scoring</span></span>

<span data-ttu-id="c4c68-380">後処理の場合と同様に、スコアリングの手順にはいくつかの手順があります。</span><span class="sxs-lookup"><span data-stu-id="c4c68-380">Just like with post-processing, there are a few steps in the scoring steps.</span></span> <span data-ttu-id="c4c68-381">このために、スコアリング ロジックを格納するクラスをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-381">To help with this, add a class that will contain the scoring logic to your project.</span></span>

1. <span data-ttu-id="c4c68-382">**ソリューション エクスプローラー**で、プロジェクトを右クリックし、 **[追加]**  >  **[新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-382">In **Solution Explorer**, right-click the project, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="c4c68-383">**[新しい項目の追加]** ダイアログボックスで **[クラス]** を選択し、 **[名前]** フィールドを "*OnnxModelScorer.cs*" に変更します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-383">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *OnnxModelScorer.cs*.</span></span> <span data-ttu-id="c4c68-384">次に **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-384">Then, select the **Add** button.</span></span>

    <span data-ttu-id="c4c68-385">コード エディターで "*OnnxModelScorer.cs*" ファイルが開きます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-385">The *OnnxModelScorer.cs* file opens in the code editor.</span></span> <span data-ttu-id="c4c68-386">"*OnnxModelScorer.cs*" の先頭に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-386">Add the following `using` statement to the top of *OnnxModelScorer.cs*:</span></span>

    [!code-csharp [ScorerUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L1-L7)]

    <span data-ttu-id="c4c68-387">`OnnxModelScorer` クラス定義内に、次の変数を追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-387">Inside the `OnnxModelScorer` class definition, add the following variables.</span></span>

    [!code-csharp [InitScorerVars](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L13-L17)]

    <span data-ttu-id="c4c68-388">その直下で、以前に定義された変数を初期化する `OnnxModelScorer` クラスのコンストラクターを作成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-388">Directly below that, create a constructor for the `OnnxModelScorer` class that will initialize the previously defined variables.</span></span>

    [!code-csharp [ScorerCtor](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L19-L24)]

    <span data-ttu-id="c4c68-389">コンストラクターを作成したら、画像とモデルの設定に関連する変数を格納する 2 つの構造体を定義します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-389">Once you have created the constructor, define a couple of structs that contain variables related to the image and model settings.</span></span> <span data-ttu-id="c4c68-390">モデルの入力として想定される高さと幅を格納するために、`ImageNetSettings` という名前の構造体を作成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-390">Create a struct called `ImageNetSettings` to contain the height and width expected as input for the model.</span></span>

    [!code-csharp [ImageNetSettingStruct](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L26-L30)]

    <span data-ttu-id="c4c68-391">その後、`TinyYoloModelSettings` という別の構造体を作成します。これには、モデルの入力レイヤーと出力レイヤーの名前を格納します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-391">After that, create another struct called `TinyYoloModelSettings` which contains the names of the input and output layers of the model.</span></span> <span data-ttu-id="c4c68-392">モデルの入力レイヤーと出力レイヤーの名前を視覚化するには、[Netron](https://github.com/lutzroeder/netron) のようなツールを使用できます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-392">To visualize the name of the input and output layers of the model, you can use a tool like [Netron](https://github.com/lutzroeder/netron).</span></span>

    [!code-csharp [YoloSettingsStruct](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L32-L43)]

    <span data-ttu-id="c4c68-393">次に、スコアリングに使用するメソッドの最初のセットを作成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-393">Next, create the first set of methods use for scoring.</span></span> <span data-ttu-id="c4c68-394">`OnnxModelScorer` クラス内に `LoadModel` メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-394">Create the `LoadModel` method inside of your `OnnxModelScorer` class.</span></span>

    ```csharp
    private ITransformer LoadModel(string modelLocation)
    {

    }
    ```

    <span data-ttu-id="c4c68-395">`LoadModel` メソッド内に、ログ記録用に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-395">Inside the `LoadModel` method, add the following code for logging.</span></span>

    [!code-csharp [LoadModelLog](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L47-L49)]

    <span data-ttu-id="c4c68-396">通常、ML.NET パイプラインでは [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit*) メソッドが呼び出されたときにデータが処理されることを想定しています。</span><span class="sxs-lookup"><span data-stu-id="c4c68-396">ML.NET pipelines typically expect data to operate on when the [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit*) method is called.</span></span> <span data-ttu-id="c4c68-397">この場合、トレーニングに似たプロセスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-397">In this case, a process similar to training will be used.</span></span> <span data-ttu-id="c4c68-398">ただし、実際のトレーニングは行われないため、空の [`IDataView`](xref:Microsoft.ML.IDataView) を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-398">However, because no actual training is happening, it is acceptable to use an empty [`IDataView`](xref:Microsoft.ML.IDataView).</span></span> <span data-ttu-id="c4c68-399">空のリストから、パイプラインの新しい [`IDataView`](xref:Microsoft.ML.IDataView) を作成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-399">Create a new [`IDataView`](xref:Microsoft.ML.IDataView) for the pipeline from an empty list.</span></span>

    [!code-csharp [LoadEmptyIDV](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L52)]    

    <span data-ttu-id="c4c68-400">その下にパイプラインを定義します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-400">Below that, define the pipeline.</span></span> <span data-ttu-id="c4c68-401">パイプラインは、4 つの変換で構成されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-401">The pipeline will consist of four transforms.</span></span>

    - <span data-ttu-id="c4c68-402">[`LoadImages`](xref:Microsoft.ML.ImageEstimatorsCatalog.LoadImages*) で、画像がビットマップとして読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-402">[`LoadImages`](xref:Microsoft.ML.ImageEstimatorsCatalog.LoadImages*) loads the image as a Bitmap.</span></span>
    - <span data-ttu-id="c4c68-403">[`ResizeImages`](xref:Microsoft.ML.ImageEstimatorsCatalog.ResizeImages*) で、指定されたサイズ (この場合は `416 x 416`) に画像の再スケーリングが行われます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-403">[`ResizeImages`](xref:Microsoft.ML.ImageEstimatorsCatalog.ResizeImages*) rescales the image to the size specified (in this case, `416 x 416`).</span></span>
    - <span data-ttu-id="c4c68-404">[`ExtractPixels`](xref:Microsoft.ML.ImageEstimatorsCatalog.ExtractPixels*) で、画像のピクセル表現がビットマップから数値ベクターに変更されます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-404">[`ExtractPixels`](xref:Microsoft.ML.ImageEstimatorsCatalog.ExtractPixels*) changes the pixel representation of the image from a Bitmap to a numerical vector.</span></span>
    - <span data-ttu-id="c4c68-405">[`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel*) で、ONNX モデルが読み込まれ、それを使用して、指定されたデータでスコアリングされます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-405">[`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel*) loads the ONNX model and uses it to score on the data provided.</span></span>

    <span data-ttu-id="c4c68-406">`LoadModel` メソッドで、`data` 変数の下にパイプラインを定義します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-406">Define your pipeline in the `LoadModel` method below the `data` variable.</span></span>

    [!code-csharp [ScoringPipeline](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L55-L58)]

    <span data-ttu-id="c4c68-407">次に、スコアリングできるようにモデルをインスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-407">Now it's time to instantiate the model for scoring.</span></span> <span data-ttu-id="c4c68-408">パイプラインの [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit*) メソッドを呼び出し、後続の処理のためにそれを返します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-408">Call the [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit*) method on the pipeline and return it for further processing.</span></span>

    [!code-csharp [FitReturnModel](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L61-L63)]

<span data-ttu-id="c4c68-409">モデルが読み込まれたら、それを使用して予測を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-409">Once the model is loaded, it can then be used to make predictions.</span></span> <span data-ttu-id="c4c68-410">このプロセスを容易にするために、`LoadModel` メソッドの下に `PredictDataUsingModel` というメソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-410">To facilitate that process, create a method called `PredictDataUsingModel` below the `LoadModel` method.</span></span>

```csharp
private IEnumerable<float[]> PredictDataUsingModel(IDataView testData, ITransformer model)
{

}
```

<span data-ttu-id="c4c68-411">`PredictDataUsingModel` 内に、ログを記録できるように、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-411">Inside the `PredictDataUsingModel`, add the following code for logging.</span></span>

[!code-csharp [PredictDataLog](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L68-L71)]

<span data-ttu-id="c4c68-412">次に、[`Transform`](xref:Microsoft.ML.ITransformer.Transform*) メソッドを使用してデータをスコアリングします。</span><span class="sxs-lookup"><span data-stu-id="c4c68-412">Then, use the [`Transform`](xref:Microsoft.ML.ITransformer.Transform*) method to score the data.</span></span>

[!code-csharp [ScoreImages](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L73)]

<span data-ttu-id="c4c68-413">予測される確率を抽出し、追加の処理が行えるように確率を返します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-413">Extract the predicted probabilities and return them for additional processing.</span></span>

[!code-csharp [ReturnModelOutput](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L75-L77)]

<span data-ttu-id="c4c68-414">これで両方の手順が設定されたので、結合して 1 つのメソッドにします。</span><span class="sxs-lookup"><span data-stu-id="c4c68-414">Now that both steps are set up, combine them into a single method.</span></span> <span data-ttu-id="c4c68-415">`PredictDataUsingModel` メソッドの下に、`Score` という新しいメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-415">Below the `PredictDataUsingModel` method, add a new method called `Score`.</span></span>

[!code-csharp [ScoreMethod](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L80-L85)]

<span data-ttu-id="c4c68-416">もう一息です!</span><span class="sxs-lookup"><span data-stu-id="c4c68-416">Almost there!</span></span> <span data-ttu-id="c4c68-417">これで、すべてを使用できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="c4c68-417">Now it's time to put it all to use.</span></span>

## <a name="detect-objects"></a><span data-ttu-id="c4c68-418">オブジェクトを検出する</span><span class="sxs-lookup"><span data-stu-id="c4c68-418">Detect objects</span></span>

<span data-ttu-id="c4c68-419">すべての設定が完了したので、次はいくつかのオブジェクトを検出します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-419">Now that all of the setup is complete, it's time to detect some objects.</span></span> <span data-ttu-id="c4c68-420">"*Program.cs*" クラスの `Main` メソッド内に、try-catch ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-420">Inside the `Main` method of your *Program.cs* class, add a try-catch statement.</span></span>

```csharp
try
{

}
catch (Exception ex)
{
    Console.WriteLine(ex.ToString());
}
```

<span data-ttu-id="c4c68-421">`try` ブロック内で、オブジェクト検出ロジックの実装を開始します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-421">Inside of the `try` block, start implementing the object detection logic.</span></span> <span data-ttu-id="c4c68-422">まず、データを [`IDataView`](xref:Microsoft.ML.IDataView) に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-422">First, load the data into an [`IDataView`](xref:Microsoft.ML.IDataView).</span></span>

[!code-csharp [LoadData](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L29-L30)]

<span data-ttu-id="c4c68-423">次に、`OnnxModelScorer` のインスタンスを作成し、読み込まれるデータのスコアリングに使用します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-423">Then, create an instance of `OnnxModelScorer` and use it to score the loaded data.</span></span>

[!code-csharp [ScoreData](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L33-L36)]

<span data-ttu-id="c4c68-424">次は、後処理の手順です。</span><span class="sxs-lookup"><span data-stu-id="c4c68-424">Now it's time for the post-processing step.</span></span> <span data-ttu-id="c4c68-425">`YoloOutputParser` のインスタンスを作成し、モデル出力の処理に使用します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-425">Create an instance of `YoloOutputParser` and use it to process the model output.</span></span>

[!code-csharp [ParsePredictions](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L39-L44)]

<span data-ttu-id="c4c68-426">モデル出力が処理されたら、次は画像上に境界ボックスを描画します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-426">Once the model output has been processed, it's time to draw the bounding boxes on the images.</span></span> <span data-ttu-id="c4c68-427">for ループを作成して、スコア付けされた各画像を反復処理します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-427">Create a for-loop to iterate over each of the scored images.</span></span>

```csharp
for (var i = 0; i < images.Count(); i++)
{

}
```

<span data-ttu-id="c4c68-428">for ループ内で、画像ファイルの名前と、画像に関連付けられている境界ボックスを取得します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-428">Inside of the for-loop, get the name of the image file and the bounding boxes associated with it.</span></span>

[!code-csharp [GetImageFileName](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L49-L50)]

<span data-ttu-id="c4c68-429">その後、`DrawBoundingBox` メソッドを使用して、画像の上に境界ボックスを描画します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-429">Below that, use the `DrawBoundingBox` method to draw the bounding boxes on the image.</span></span>

[!code-csharp [DrawBBoxes](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L52)]

<span data-ttu-id="c4c68-430">最後に、`LogDetectedObjects` メソッドを使用して、ログ記録ロジックを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-430">Lastly, add some logging logic with the `LogDetectedObjects` method.</span></span>

[!code-csharp [LogPredictionsOutput](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L54)]

<span data-ttu-id="c4c68-431">try-catch ステートメントの後に、プロセスの実行が完了したことを示すロジックを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-431">After the try-catch statement, add additional logic to indicate the process is done running.</span></span>

[!code-csharp [EndProcessLog](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L62-L63)]

<span data-ttu-id="c4c68-432">これで完了です。</span><span class="sxs-lookup"><span data-stu-id="c4c68-432">That's it!</span></span> 

## <a name="results"></a><span data-ttu-id="c4c68-433">結果</span><span class="sxs-lookup"><span data-stu-id="c4c68-433">Results</span></span> 

<span data-ttu-id="c4c68-434">上記の手順を実行した後、コンソール アプリを実行します (Ctrl + F5 キー)。</span><span class="sxs-lookup"><span data-stu-id="c4c68-434">After following the previous steps, run your console app (Ctrl + F5).</span></span> <span data-ttu-id="c4c68-435">結果は以下の出力のようになるはずです。</span><span class="sxs-lookup"><span data-stu-id="c4c68-435">Your results should be similar to the following output.</span></span> <span data-ttu-id="c4c68-436">警告メッセージまたは処理中のメッセージが表示される場合がありますが、わかりやすくするため、これらのメッセージは結果から削除してあります。</span><span class="sxs-lookup"><span data-stu-id="c4c68-436">You may see warnings or processing messages, but these messages have been removed from the following results for clarity.</span></span>

```console
=====Identify the objects in the images=====

.....The objects in the image image1.jpg are detected as below....
car and its Confidence score: 0.9697262
car and its Confidence score: 0.6674225
person and its Confidence score: 0.5226039
car and its Confidence score: 0.5224892
car and its Confidence score: 0.4675332

.....The objects in the image image2.jpg are detected as below....
cat and its Confidence score: 0.6461141
cat and its Confidence score: 0.6400049

.....The objects in the image image3.jpg are detected as below....
chair and its Confidence score: 0.840578
chair and its Confidence score: 0.796363
diningtable and its Confidence score: 0.6056048
diningtable and its Confidence score: 0.3737402

.....The objects in the image image4.jpg are detected as below....
dog and its Confidence score: 0.7608147
person and its Confidence score: 0.6321323
dog and its Confidence score: 0.5967442
person and its Confidence score: 0.5730394
person and its Confidence score: 0.5551759

========= End of Process..Hit any Key ========
```

<span data-ttu-id="c4c68-437">画像を境界ボックスと共に表示するには、`assets/images/output/` ディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-437">To see the images with bounding boxes, navigate to the `assets/images/output/` directory.</span></span> <span data-ttu-id="c4c68-438">処理された画像の 1 つのサンプルを次に示します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-438">Below is a sample from one of the processed images.</span></span> 

![](./media/object-detection-onnx/image3.jpg)

<span data-ttu-id="c4c68-439">おめでとうございます!</span><span class="sxs-lookup"><span data-stu-id="c4c68-439">Congratulations!</span></span> <span data-ttu-id="c4c68-440">これで、ML.NET でトレーニング済みの `ONNX` モデルを再利用してオブジェクト検出用の機械学習モデルを構築できました。</span><span class="sxs-lookup"><span data-stu-id="c4c68-440">You've now successfully built a machine learning model for object detection by reusing a pre-trained `ONNX` model in ML.NET.</span></span>

<span data-ttu-id="c4c68-441">このチュートリアルのソース コードは [dotnet/samples](https://github.com/dotnet/machinelearning-samples/tree/master/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx) リポジトリで確認できます。</span><span class="sxs-lookup"><span data-stu-id="c4c68-441">You can find the source code for this tutorial at the [dotnet/samples](https://github.com/dotnet/machinelearning-samples/tree/master/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx) repository.</span></span>

<span data-ttu-id="c4c68-442">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="c4c68-442">In this tutorial, you learned how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="c4c68-443">問題を把握する</span><span class="sxs-lookup"><span data-stu-id="c4c68-443">Understand the problem</span></span>
> * <span data-ttu-id="c4c68-444">ONNX の概要と ML.NET でどのように動作するかについて説明します。</span><span class="sxs-lookup"><span data-stu-id="c4c68-444">Learn what ONNX is and how it works with ML.NET</span></span>
> * <span data-ttu-id="c4c68-445">モデルの概要</span><span class="sxs-lookup"><span data-stu-id="c4c68-445">Understand the model</span></span>
> * <span data-ttu-id="c4c68-446">事前トレーニング済みモデルを再利用する</span><span class="sxs-lookup"><span data-stu-id="c4c68-446">Reuse the pre-trained model</span></span>
> * <span data-ttu-id="c4c68-447">読み込まれたモデルを使用してオブジェクトを検出する</span><span class="sxs-lookup"><span data-stu-id="c4c68-447">Detect objects with a loaded model</span></span>

<span data-ttu-id="c4c68-448">Machine Learning サンプルの GitHub リポジトリを確認し、拡張されたオブジェクト検出サンプルを探索してください。</span><span class="sxs-lookup"><span data-stu-id="c4c68-448">Check out the Machine Learning samples GitHub repository to explore an expanded object detection sample.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="c4c68-449">dotnet/machinelearning-samples GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="c4c68-449">dotnet/machinelearning-samples GitHub repository</span></span>](https://github.com/dotnet/machinelearning-samples/tree/master/samples/csharp/end-to-end-apps/DeepLearning_ObjectDetection_Onnx)