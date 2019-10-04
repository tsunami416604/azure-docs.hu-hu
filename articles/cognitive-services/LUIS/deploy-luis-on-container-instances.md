---
title: Futtassa az Azure Container Instances szolgáltatásban
titleSuffix: Azure Cognitive Services
description: A LUIS-tároló üzembe helyezése az Azure-Tárolópéldányon, és tesztelje a szolgáltatást egy webböngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 1d19d80bbc334a376f77eb285349fb1a87a91a54
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711570"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>A Language Understanding (LUIS)-tároló üzembe helyezése az Azure Container Instances szolgáltatásban

Ismerje meg, hogyan helyezhet üzembe a Cognitive Services [LUIS](luis-container-howto.md) tárolót az Azure-bA [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ez az eljárás bemutatja egy Anomáliadetektálási detector használatával erőforrást. Majd bemutatjuk, hogy a társított tároló rendszerképének lekérése. Végül azt jelölje ki a azon képessége, hogy a böngésző a két vezénylését. Tárolók használatával is shift figyelmét a fejlesztők számára az alkalmazásfejlesztés inkább összpontosító infrastruktúra kezelése.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Create LUIS Container Instance resource](../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
