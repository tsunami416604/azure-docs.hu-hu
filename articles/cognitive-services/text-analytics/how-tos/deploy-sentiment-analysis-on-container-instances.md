---
title: Azure Container Instances-Text Analytics futtatása
titleSuffix: Azure Cognitive Services
description: A Text Analytics-tárolókat a hangulat-elemzési képpel, az Azure Container Instanceba helyezheti üzembe, és tesztelheti egy böngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 9ef529c9d505e5b305602c80a8dbef906f52269c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552534"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances"></a>Hangulatelemzés tároló üzembe helyezése Azure Container Instances

Megtudhatja, hogyan helyezheti üzembe a Cognitive Services [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) tárolót az Hangulatelemzés rendszerkép használatával az Azure [Container Instancesba](https://docs.microsoft.com/azure/container-instances/). Ez az eljárás egy Text Analytics erőforrás létrehozását, egy kapcsolódó Hangulatelemzés-rendszerkép létrehozását, valamint a két böngészőből való e felállításának lehetőségét példázza. A tárolók használatával az alkalmazások fejlesztésére összpontosíthat a fejlesztők figyelmét arra, hogy az infrastruktúra kezelése helyett.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés használata. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>További lépések 

* További [Cognitive Services tárolók](../../cognitive-services-container-support.md) használata
* A [text Analytics csatlakoztatott szolgáltatás](../vs-text-connected-service.md) használata
