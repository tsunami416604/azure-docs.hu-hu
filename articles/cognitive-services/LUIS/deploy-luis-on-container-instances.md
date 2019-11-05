---
title: Azure Container instances futtatása
titleSuffix: Azure Cognitive Services
description: Telepítse a LUIS-tárolót egy Azure Container instanceba, és tesztelje egy böngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 6a714178c9f8ec3f9239ba64bccbcf25b4aecf60
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465929"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>A Language Understanding (LUIS) tároló üzembe helyezése az Azure Container instances szolgáltatásban

Megtudhatja, hogyan helyezheti üzembe a Cognitive Services [Luis](luis-container-howto.md) -tárolót az Azure [Container instances](https://docs.microsoft.com/azure/container-instances/)szolgáltatásban. Ez az eljárás egy anomália-detektor erőforrás létrehozását mutatja be. Ezután megbeszéljük a társított tároló-rendszerkép kihúzását. Végezetül kiemeljük a két böngészőből való összehangolás lehetőségét. A tárolók használatával az alkalmazások fejlesztésére összpontosíthat a fejlesztők figyelmét arra, hogy az infrastruktúra kezelése helyett.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
