---
title: Űrlap-felismerő tároló futtatása a Azure Container Instancesban
titleSuffix: Azure Cognitive Services
description: Üzembe helyezheti az űrlap-felismerő tárolót egy Azure Container-példányon, és tesztelheti egy böngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: c45d0e316cf67d22095911fadfd3d84ef407e1b0
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515633"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Az űrlap-felismerő tároló üzembe helyezése Azure Container Instances

Megtudhatja, hogyan helyezheti üzembe a Cognitive Services [űrlap-felismerő](form-recognizer-container-howto.md) tárolót az Azure [Container Instancesban](https://docs.microsoft.com/azure/container-instances/). Ez az eljárás bemutatja egy Azure Form-felismerő erőforrás létrehozását. Ezután megbeszéljük a társított tároló-rendszerkép kihúzását. Végezetül kiemeljük a két böngészőből való összehangolás lehetőségét. A tárolók használatával az alkalmazások fejlesztésére összpontosíthat a fejlesztők figyelmét arra, hogy az infrastruktúra kezelése helyett.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Hozzáférés kérése a tároló beállításjegyzékéhez

Először be kell fejeznie és el kell küldenie a [Cognitive Services űrlap-felismerő tárolók hozzáférési kérelmének űrlapját](https://aka.ms/FormRecognizerRequestAccess) , hogy hozzáférést Kérjen a tárolóhoz. Emellett aláírja a Computer Vision. Nem kell külön regisztrálnia a Computer Vision kérelem űrlapján. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
