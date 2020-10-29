---
title: Face tároló futtatása Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Üzembe helyezheti a Face tárolót egy Azure Container-példányon, és tesztelheti egy böngészőben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: a5354581be519172c498e57d25510f9fc5c0daa4
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911256"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>A Face tároló üzembe helyezése Azure Container Instances

> [!IMPORTANT]
> Elérte a Face-tároló felhasználóinak korlátját. Jelenleg nem fogadunk el új alkalmazásokat a Face-tárolóhoz.

Megtudhatja, hogyan helyezheti üzembe a Cognitive Services [Face](../face-how-to-install-containers.md) tárolót az Azure [Container Instancesban](../../../container-instances/index.yml). Ez az eljárás egy Azure Face-erőforrás létrehozását mutatja be. Ezután megbeszéljük a társított tároló-rendszerkép kihúzását. Végezetül kiemeljük a két böngészőből való összehangolás lehetőségét. A tárolók használatával az alkalmazások fejlesztésére összpontosíthat a fejlesztők figyelmét arra, hogy az infrastruktúra kezelése helyett.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]