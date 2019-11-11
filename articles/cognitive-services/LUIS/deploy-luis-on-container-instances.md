---
title: LUIS-tároló üzembe helyezése az Azure Container instances szolgáltatásban
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
ms.openlocfilehash: aecbb9bb94fc251ee0142b611c54d16304793e50
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73901814"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>A Language Understanding (LUIS) tároló üzembe helyezése az Azure Container instances szolgáltatásban

Megtudhatja, hogyan helyezheti üzembe a Cognitive Services [Luis](luis-container-howto.md) -tárolót az Azure [Container instances](https://docs.microsoft.com/azure/container-instances/)szolgáltatásban. Ez az eljárás egy anomália-detektor erőforrás létrehozását mutatja be. Ezután megbeszéljük a társított tároló-rendszerkép kihúzását. Végezetül kiemeljük a két böngészőből való összehangolás lehetőségét. A tárolók használatával az alkalmazások fejlesztésére összpontosíthat a fejlesztők figyelmét arra, hogy az infrastruktúra kezelése helyett.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
