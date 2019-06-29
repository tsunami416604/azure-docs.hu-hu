---
title: Futtassa az Azure Container Instances szolgáltatásban
titleSuffix: Text Analytics - Azure Cognitive Services
description: Helyezze üzembe a text analytics tárolókat a vélemények elemzése lemezképpel, az Azure-Tárolópéldányon, és tesztelje a szolgáltatást egy webböngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: a82b5a1cbed662289d3a406a61fdd19a3ca8861b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454982"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances-aci"></a>Hangulatelemzés a tároló üzembe helyezése az Azure Container Instances (ACI)

Ismerje meg, hogyan helyezhet üzembe a Cognitive Services [Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) a Hangulatelemzés lemezképet az Azure-tárolóban [Container Instances](https://docs.microsoft.com/azure/container-instances/) (aci Szolgáltatásban). Ez az eljárás exemplifies a egy erőforrás létrehozását a Text Analytics, a társított Hangulatelemzés lemezkép és az azon képessége, hogy a két közvetlenül a böngészőből a vezénylési létrehozását. Tárolók használatával is shift figyelmét a fejlesztők számára az alkalmazásfejlesztés inkább összpontosító infrastruktúra kezelése.

## <a name="prerequisites"></a>Előfeltételek

* Használja az Azure-előfizetéssel. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances (ACI)](../../containers/includes/create-aci-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>További lépések 

* Több [Cognitive Services-tárolók](../../cognitive-services-container-support.md)
* Használja a [Szövegelemzés csatlakoztatott szolgáltatás](../vs-text-connected-service.md)
