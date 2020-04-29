---
title: Anomália-detektor tárolójának futtatása Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Telepítse az anomália detektor tárolót egy Azure Container-példányra, és tesztelje egy böngészőben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a4973254c8a53917d99daf38b0d11e5dbecbb731
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80875119"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Rendellenesség-Kiderítő tároló üzembe helyezése Azure Container Instances

Megtudhatja, hogyan helyezheti üzembe a Cognitive Services [anomália detektor](../anomaly-detector-container-howto.md) tárolót az Azure [Container Instancesban](https://docs.microsoft.com/azure/container-instances/). Ez az eljárás egy anomália-detektor erőforrás létrehozását mutatja be. Ezután megbeszéljük a társított tároló-rendszerkép kihúzását. Végezetül kiemeljük a két böngészőből való összehangolás lehetőségét. A tárolók használatával az alkalmazások fejlesztésére összpontosíthat a fejlesztők figyelmét arra, hogy az infrastruktúra kezelése helyett.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Hozzáférés kérése a Private Container registryhez

Először be kell fejeznie és el kell küldenie az [anomália-detektor tárolójának kérelmi űrlapját](https://aka.ms/adcontainer) , hogy hozzáférést Kérjen a tárolóhoz.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>További lépések

* A tároló rendszerképének áthúzásához és a tároló futtatásához tekintse át a tárolók [telepítése és futtatása](../anomaly-detector-container-configuration.md)
* A [tárolók konfigurálásának](../anomaly-detector-container-configuration.md) áttekintése konfigurációs beállításokhoz
* [További információ az anomália-érzékelő API szolgáltatásáról](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
