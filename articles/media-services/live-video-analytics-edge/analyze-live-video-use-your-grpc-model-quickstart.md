---
title: Élő videó elemzése saját gRPC-modell használatával – Azure
description: Ebben a rövid útmutatóban a számítógép jövőképét fogja alkalmazni az élő videó hírcsatornájának (szimulált) IP-kamerából való elemzéséhez.
ms.topic: quickstart
ms.date: 08/14/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: ac11ced68ab8463da26b9978a5b0b02c4cd1a402
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691792"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-grpc-model"></a>Rövid útmutató: élő videó elemzése saját gRPC-modell használatával

Ez a rövid útmutató bemutatja, hogyan használható a Live Video Analytics a IoT Edge egy élő videó-hírcsatorna (szimulált) IP-kamerából való elemzéséhez. Megtudhatja, hogyan alkalmazhatja a Computer látási modellt az objektumok észlelésére. Az élő video-hírcsatornában található keretek egy részhalmazát a rendszer egy következtetési szolgáltatásnak továbbítja. Az eredményeket a rendszer IoT Edge hubhoz továbbítja.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-grpc-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-grpc-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Előfeltételek

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-grpc-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-grpc-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>A minta videó áttekintése

::: zone pivot="programming-language-csharp"
[!INCLUDE [review sample video](includes/analyze-live-video-your-grpc-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review sample video](includes/analyze-live-video-your-grpc-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Áttekintés

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-grpc-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-grpc-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>A Media Graph létrehozása és üzembe helyezése

::: zone pivot="programming-language-csharp"
[!INCLUDE [create and deply the media graph](includes/analyze-live-video-your-grpc-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create and deply the media graph](includes/analyze-live-video-your-grpc-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>Eredmények értelmezése

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret results](includes/analyze-live-video-your-grpc-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret results](includes/analyze-live-video-your-grpc-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha más gyors útmutatókat szeretne kipróbálni, tartsa meg a létrehozott erőforrásokat. Ellenkező esetben lépjen a Azure Portalra, nyissa meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben ezt a rövid útmutatót futtatta, és törölje az összes erőforrást.

## <a name="next-steps"></a>További lépések

Próbálja meg különböző Media Graph-topológiákat futtatni a gRPC protokoll használatával.
