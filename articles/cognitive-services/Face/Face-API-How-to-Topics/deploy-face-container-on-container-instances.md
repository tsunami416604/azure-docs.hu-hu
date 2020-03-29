---
title: Face-tároló futtatása az Azure Container-példányokban
titleSuffix: Azure Cognitive Services
description: Telepítse a Face-tárolót egy Azure Container-példányra, és tesztelje azt egy webböngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 4f8a08697443ec6920b3fa73de19a950e54e7f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76716257"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>A Face-tároló üzembe helyezése az Azure Container-példányok ba

Ismerje meg, hogyan [Face](../face-how-to-install-containers.md) telepítheti a Cognitive Services Face-tárolót az Azure [Container Instances szolgáltatásba.](https://docs.microsoft.com/azure/container-instances/) Ez az eljárás bemutatja egy Azure Face-erőforrás létrehozását. Ezután megbeszéljük a kapcsolódó tárolókép húzását. Végül kiemeljük a két vezénylési lehetőségének gyakorlását egy böngészőből. A tárolók használatával a fejlesztők figyelmét elterelheti az infrastruktúra kezeléséről az alkalmazásfejlesztésre való összpontosításra.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Hozzáférés kérése a privát tároló beállításjegyzékéhez

[!INCLUDE [Request access to private container registry](../../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]