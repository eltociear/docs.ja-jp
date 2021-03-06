---
title: SOA アプリケーション
description: コンテナーも SOA アプリケーションの便利なデプロイ オプションになる可能性があることを留意してください。
ms.date: 02/15/2019
ms.openlocfilehash: aa56ada7b14a465fb3dafd02b03b815782ac765b
ms.sourcegitcommit: f20dd18dbcf2275513281f5d9ad7ece6a62644b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68672359"
---
# <a name="service-oriented-applications"></a>サービス指向アプリケーション

サービス指向アーキテクチャ (SOA) は乱用されている用語であり、人によって異なる意味で使われています。 ただし、共通の基準としての SOA は、アプリケーションのアーキテクチャをサブシステムなどのさまざまな種類、場合によっては階層に分類できるいくつかのサービス (通常は HTTP サービス) に分解して、アプリケーションを構築することを意味します。

現在では、これらのサービスを Docker コンテナーとしてデプロイできます。依存関係のすべてがコンテナー イメージに含まれているため、デプロイ関連の問題が解決されます。 ただし、SOA をスケールアウトする必要がある場合、1 つのインスタンスに基づいてデプロイしている場合は課題にぶつかる可能性があります。 この課題は、Docker クラスタリング ソフトウェアまたはオーケストレーターを使用して対処できます。 次のセクションでは、マイクロサービスのアプローチを説明するときに、オーケストレーターについてさらに詳しく見ていきます。

Docker コンテナーは、従来のサービス指向アーキテクチャと高度なマイクロサービス アーキテクチャの両方にとって便利な機能です (ただし、必須ではありません)。

結局のところ、コンテナー クラスタリング ソリューションは、従来の SOA アーキテクチャと、各マイクロサービスがデータ モデルを所有するより高度なマイクロサービス アーキテクチャの両方に役立ちます。 また、複数のデータベースがあるため、SOA サービスで共有されているモノリシック データベースを扱う代わりに、データ層をスケールアウトすることもできます。 ただし、データの分割に関する説明は、純粋にアーキテクチャと設計に関するものです。

>[!div class="step-by-step"]
>[前へ](state-and-data-in-docker-applications.md)
>[次へ](orchestrate-high-scalability-availability.md)
