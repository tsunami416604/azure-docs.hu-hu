---
title: Azure container-példányok futtatása – beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Telepítse a beszédszolgáltatás-tárolót egy Azure Container-példányra, és tesztelje azt egy webböngészőben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 610d5ce095097a31ee92c67f0112d1657424858e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878748"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>A beszédfelismerési szolgáltatás tárolójának üzembe helyezése az Azure Container Instances szolgáltatásba

Ismerje meg, hogyan telepítheti a Cognitive Services [beszédszolgáltatás-tárolóját](speech-container-howto.md) az Azure [Container Instances szolgáltatásba.](https://docs.microsoft.com/azure/container-instances/) Ez az eljárás bemutatja egy Azure Speech szolgáltatás-erőforrás létrehozását. Ezután megbeszéljük a kapcsolódó tárolókép húzását. Végül kiemeljük a két vezénylési lehetőségének gyakorlását egy böngészőből. A tárolók használatával a fejlesztők figyelmét elterelheti az infrastruktúra kezeléséről az alkalmazásfejlesztésre való összpontosításra.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Hozzáférés kérése a tároló beállításjegyzékéhez

Először ki kell töltenie és el kell küldenie a [Cognitive Services beszédtárolók kérelem űrlap](https://aka.ms/speechcontainerspreview/) hozzáférést kell kérnie a tárolóhoz. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create a Speech service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
