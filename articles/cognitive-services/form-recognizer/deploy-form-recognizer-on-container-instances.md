---
title: Űrlapfelismerő tároló futtatása az Azure Container-példányokban
titleSuffix: Azure Cognitive Services
description: Telepítse az űrlapfelismerő tárolót egy Azure Container Instance-ba, és tesztelje azt egy webböngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: dapine
ms.openlocfilehash: 9a3456eb4d30aa8d163488f558b571dd97c73bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605116"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Az űrlapfelismerő tároló üzembe helyezése az Azure Container Instances szolgáltatásba

Ismerje meg, hogyan telepítheti a Cognitive Services [űrlapfelismerő](form-recognizer-container-howto.md) tárolót az Azure [Container Instances szolgáltatásba.](https://docs.microsoft.com/azure/container-instances/) Ez az eljárás bemutatja egy Azure Form Recognizer erőforrás létrehozását. Ezután megbeszéljük a kapcsolódó tárolókép húzását. Végül kiemeljük a két vezénylési lehetőségének gyakorlását egy böngészőből. A tárolók használatával a fejlesztők figyelmét elterelheti az infrastruktúra kezeléséről az alkalmazásfejlesztésre való összpontosításra.

> [!IMPORTANT]
> A Form Recognizer tárolók jelenleg a Form Recognizer API 1.0-s verzióját használják. Az API legújabb verzióját a felügyelt szolgáltatás használatával érheti el.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Hozzáférés kérése a tároló beállításjegyzékéhez

Először ki kell töltenie és el kell küldenie a [Cognitive Services űrlapfelismerő tárolók hozzáférési kérelem űrlap](https://aka.ms/FormRecognizerContainerRequestAccess) hozzáférést a tárolóhoz. Ezzel is feliratkozik a Computer Vision. Nem kell külön regisztrálnia a Computer Vision kérelem űrlapra. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
