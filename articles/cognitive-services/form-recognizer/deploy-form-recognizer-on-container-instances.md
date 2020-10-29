---
title: Űrlap-felismerő tároló futtatása a Azure Container Instancesban
titleSuffix: Azure Cognitive Services
description: Üzembe helyezheti az űrlap-felismerő tárolót egy Azure Container-példányon, és tesztelheti egy böngészőben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: fb837dfc72a148d20d382a5dc356c99306aa400f
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913186"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Az űrlap-felismerő tároló üzembe helyezése Azure Container Instances

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Megtudhatja, hogyan helyezheti üzembe a Cognitive Services [űrlap-felismerő](form-recognizer-container-howto.md) tárolót az Azure [Container Instancesban](../../container-instances/index.yml). Ez az eljárás bemutatja egy Azure Form-felismerő erőforrás létrehozását. Ezután megbeszéljük a társított tároló-rendszerkép kihúzását. Végezetül kiemeljük a két böngészőből való összehangolás lehetőségét. A tárolók használatával az alkalmazások fejlesztésére összpontosíthat a fejlesztők figyelmét arra, hogy az infrastruktúra kezelése helyett.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]