---
title: Azure Container Instances-Speech szolgáltatás futtatása
titleSuffix: Azure Cognitive Services
description: Üzembe helyezheti a Speech Service-tárolót egy Azure Container-példányon, és tesztelheti egy böngészőben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 610d5ce095097a31ee92c67f0112d1657424858e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80878748"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>A Speech Service-tároló üzembe helyezése Azure Container Instances

Megtudhatja, hogyan helyezheti üzembe a Cognitive Services [Speech Service](speech-container-howto.md) -tárolót az Azure [Container Instancesban](https://docs.microsoft.com/azure/container-instances/). Ez az eljárás az Azure Speech Service-erőforrások létrehozását mutatja be. Ezután megbeszéljük a társított tároló-rendszerkép kihúzását. Végezetül kiemeljük a két böngészőből való összehangolás lehetőségét. A tárolók használatával az alkalmazások fejlesztésére összpontosíthat a fejlesztők figyelmét arra, hogy az infrastruktúra kezelése helyett.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Hozzáférés kérése a tároló beállításjegyzékéhez

Először be kell fejeznie és el kell küldenie a [Cognitive Services Speech containers kérelem űrlapját](https://aka.ms/speechcontainerspreview/) , hogy hozzáférést Kérjen a tárolóhoz. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create a Speech service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
