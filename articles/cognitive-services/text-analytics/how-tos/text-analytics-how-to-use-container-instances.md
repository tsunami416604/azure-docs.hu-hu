---
title: Azure Container Instances-Text Analytics futtatása
titleSuffix: Azure Cognitive Services
description: Telepítse a Text Analytics-tárolókat az Azure Container Instanceba, és tesztelje egy böngészőben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 1dde59456126ce1221bdb71f09957c0fd3609427
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108709"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Text Analytics tároló üzembe helyezése Azure Container Instances

Megtudhatja, hogyan helyezheti üzembe a Cognitive Services [text Analytics][install-and-run-containers] tárolót az Azure [Container Instancesban][container-instances]. Ez az eljárás egy Text Analytics erőforrás létrehozását, egy kapcsolódó Hangulatelemzés-rendszerkép létrehozását, valamint a két böngészőből való e felállításának lehetőségét példázza. A tárolók használatával az alkalmazások fejlesztésére összpontosíthat a fejlesztők figyelmét arra, hogy az infrastruktúra kezelése helyett.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés használata. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[Kulcsszókeresés](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Nyelvfelismerés](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

#### <a name="text-analytics-for-health"></a>[Text Analytics állapota](#tab/health)

[!INCLUDE [Verify the health container instance](../includes/verify-health-container.md)]

***

## <a name="next-steps"></a>Következő lépések 

* További [Cognitive Services tárolók](../../cognitive-services-container-support.md) használata
* A [text Analytics csatlakoztatott szolgáltatás](../vs-text-connected-service.md) használata

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances