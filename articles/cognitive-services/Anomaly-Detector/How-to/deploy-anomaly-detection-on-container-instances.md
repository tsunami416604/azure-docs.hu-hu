---
title: Futtassa az Azure Container Instances szolgáltatásban
titleSuffix: Azure Cognitive Services
description: Az Anomáliadetektálási detector használatával-tároló üzembe helyezése az Azure-Tárolópéldányon, és tesztelje a szolgáltatást egy webböngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: b5adc3ed9234050d3977e812202717a0ce83e842
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711691"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Az Azure Container Instances-Anomáliadetektálási detector használatával tároló üzembe helyezése

Ismerje meg, hogyan helyezhet üzembe a Cognitive Services [Anomáliadetektálási detector használatával](../anomaly-detector-container-howto.md) tárolót az Azure-bA [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ez az eljárás bemutatja egy Anomáliadetektálási detector használatával erőforrást. Majd bemutatjuk, hogy a társított tároló rendszerképének lekérése. Végül azt jelölje ki a azon képessége, hogy a böngésző a két vezénylését. Tárolók használatával is shift figyelmét a fejlesztők számára az alkalmazásfejlesztés inkább összpontosító infrastruktúra kezelése.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>A privát tárolóregisztrációs hozzáférés kérése

Először végezze el, és küldje el a [Anomáliadetektálási detector használatával tároló űrlapot](https://aka.ms/adcontainer) hozzáférés kéréséhez a tárolóhoz.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]