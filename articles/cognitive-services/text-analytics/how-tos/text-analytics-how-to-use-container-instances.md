---
title: Azure Container Instances-Text Analytics futtatása
titleSuffix: Azure Cognitive Services
description: Telepítse a Text Analytics-tárolókat az Azure Container Instanceba, és tesztelje egy böngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: 35812ec4585e3189282c5acf0aa09d309c33c7ed
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051930"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Text Analytics tároló üzembe helyezése Azure Container Instances

Megtudhatja, hogyan helyezheti üzembe a Cognitive Services [text Analytics][install-and-run-containers] tárolót az Azure [Container Instancesban][container-instances]. Ez az eljárás egy Text Analytics erőforrás létrehozását, egy kapcsolódó Hangulatelemzés-rendszerkép létrehozását, valamint a két böngészőből való e felállításának lehetőségét példázza. A tárolók használatával az alkalmazások fejlesztésére összpontosíthat a fejlesztők figyelmét arra, hogy az infrastruktúra kezelése helyett.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés használata. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extractiontabkeyphrase"></a>[Kulcskifejezések kinyerése](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detectiontablanguage"></a>[Nyelvfelismerés](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysistabsentiment"></a>[Hangulatelemzés](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>További lépések 

* További [Cognitive Services tárolók](../../cognitive-services-container-support.md) használata
* A [text Analytics csatlakoztatott szolgáltatás](../vs-text-connected-service.md) használata

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances