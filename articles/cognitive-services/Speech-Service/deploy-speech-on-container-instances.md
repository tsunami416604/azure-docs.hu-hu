---
title: Futtassa az Azure Container Instances szolgáltatásban
titleSuffix: Azure Cognitive Services
description: A Speech Service-tároló üzembe helyezése az Azure-Tárolópéldányon, és tesztelje a szolgáltatást egy webböngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 062765be22135b12abb29ff6f7ce8a772c67adae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711515"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>A Speech Service-tároló üzembe helyezése az Azure Container Instances szolgáltatásban

Ismerje meg, hogyan helyezhet üzembe a Cognitive Services [beszédszolgáltatás](speech-container-howto.md) tárolót az Azure-bA [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ez az eljárás bemutatja egy Azure Speech Service-erőforrás létrehozása. Majd bemutatjuk, hogy a társított tároló rendszerképének lekérése. Végül azt jelölje ki a azon képessége, hogy a böngésző a két vezénylését. Tárolók használatával is shift figyelmét a fejlesztők számára az alkalmazásfejlesztés inkább összpontosító infrastruktúra kezelése.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>A tároló-beállításjegyzék hozzáférés kérése

Először végezze el, és küldje el a [Cognitive Services beszéd tárolók űrlapot](https://aka.ms/speechcontainerspreview/) hozzáférés kéréséhez a tárolóhoz. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech Service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create an Speech Service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
