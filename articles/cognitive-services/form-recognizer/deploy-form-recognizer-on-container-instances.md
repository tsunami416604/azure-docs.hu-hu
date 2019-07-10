---
title: Futtassa az Azure Container Instances szolgáltatásban
titleSuffix: Azure Cognitive Services
description: A képernyő felismerő tároló üzembe egy Azure-Tárolópéldányon, és tesztelje a szolgáltatást egy webböngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 3c424465678a9989940d92910c5d288fa2fb1cab
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711394"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Az űrlap felismerő-tároló üzembe helyezése az Azure Container Instances szolgáltatásban

Ismerje meg, hogyan helyezhet üzembe a Cognitive Services [űrlap felismerő](form-recognizer-container-howto.md) tárolót az Azure-bA [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ez az eljárás bemutatja egy űrlap felismerő Azure-erőforrás létrehozása. Majd bemutatjuk, hogy a társított tároló rendszerképének lekérése. Végül azt jelölje ki a azon képessége, hogy a böngésző a két vezénylését. Tárolók használatával is shift figyelmét a fejlesztők számára az alkalmazásfejlesztés inkább összpontosító infrastruktúra kezelése.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>A tároló-beállításjegyzék hozzáférés kérése

Először végezze el, és küldje el a [Cognitive Services űrlap felismerő tárolók eléréséhez űrlapot](https://aka.ms/FormRecognizerRequestAccess) hozzáférés kéréséhez a tárolóhoz. Így is bejelentkezik a Computer Vision regisztrálásához. Nem kell külön Regisztráljon a Computer Vision űrlapot. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
