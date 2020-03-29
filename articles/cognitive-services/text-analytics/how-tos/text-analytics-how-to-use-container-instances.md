---
title: Azure Container-példányok futtatása – Szövegelemzés
titleSuffix: Azure Cognitive Services
description: Telepítse a szövegelemzési tárolókat az Azure Container Instance-ba, és tesztelje azt egy webböngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 7ae8fbd404c9c2b650e4eed30c219e8ffafe55f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383531"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Text Analytics-tároló üzembe helyezése az Azure Container-példányokba

Ismerje meg, hogyan telepítheti a Cognitive Services [szövegelemzési][install-and-run-containers] tárolóját az Azure [Container Instances szolgáltatásba.][container-instances] Ez az eljárás példázza egy Text Analytics-erőforrás létrehozását, a kapcsolódó hangulatelemzési rendszer létrehozását és a kettő böngészőből történő vezénylésének gyakorlását. A tárolók használatával a fejlesztők figyelmét elterelheti az infrastruktúra kezeléséről az alkalmazásfejlesztésre való összpontosításra.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés használata. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[Kulcskifejezések kinyerése](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Nyelvfelismerés](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>További lépések 

* További [Cognitive Services-tárolók használata](../../cognitive-services-container-support.md)
* A [Text Analytics csatlakoztatott szolgáltatás használata](../vs-text-connected-service.md)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances