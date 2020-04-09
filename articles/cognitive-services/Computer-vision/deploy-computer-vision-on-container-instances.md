---
title: Computer Vision-tároló futtatása az Azure Container-példányokban
titleSuffix: Azure Cognitive Services
description: Telepítse a Computer Vision tárolót egy Azure Container Instance, és tesztelje azt egy webböngészőben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: ecb0b6a6577161a5349d9d701dd6c116f69411f0
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876085"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>A Computer Vision tároló üzembe helyezése az Azure Container Instances-ba

Ismerje meg, hogyan telepítheti a Cognitive Services [computer vision](computer-vision-how-to-install-containers.md) tárolóját az Azure [Container Instances szolgáltatásba.](https://docs.microsoft.com/azure/container-instances/) Ez az eljárás a Computer Vision erőforrás létrehozását mutatja be. Ezután megbeszéljük a kapcsolódó tárolókép húzását. Végül kiemeljük a két vezénylési lehetőségének gyakorlását egy böngészőből. A tárolók használatával a fejlesztők figyelmét elterelheti az infrastruktúra kezeléséről az alkalmazásfejlesztésre való összpontosításra.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Hozzáférés kérése a privát tároló beállításjegyzékéhez

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]