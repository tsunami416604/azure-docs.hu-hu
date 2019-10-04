---
title: Futtassa az Azure Container Instances szolgáltatásban
titleSuffix: Azure Cognitive Services
description: A Computer Vision tároló üzembe egy Azure-Tárolópéldányon, és tesztelje a szolgáltatást egy webböngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 859147d23ea78abac2da4a4c2f1fa26a8d976d02
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711614"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>A Computer Vision-tároló üzembe helyezése az Azure Container Instances szolgáltatásban

Ismerje meg, hogyan helyezhet üzembe a Cognitive Services [Computer Vision](computer-vision-how-to-install-containers.md) tárolót az Azure-bA [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ez az eljárás bemutatja a Computer Vision erőforrást. Majd bemutatjuk, hogy a társított tároló rendszerképének lekérése. Végül azt jelölje ki a azon képessége, hogy a böngésző a két vezénylését. Tárolók használatával is shift figyelmét a fejlesztők számára az alkalmazásfejlesztés inkább összpontosító infrastruktúra kezelése.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>A privát tárolóregisztrációs hozzáférés kérése

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]