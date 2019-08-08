---
title: Azure Container Instances futtatása
titleSuffix: Azure Cognitive Services
description: Telepítse az anomália detektor tárolót egy Azure Container-példányra, és tesztelje egy böngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: cdcf701c6356303c84d3f79ee4230271f64ace78
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854235"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Rendellenesség-Kiderítő tároló üzembe helyezése Azure Container Instances

Megtudhatja, hogyan helyezheti üzembe a Cognitive Services [anomália detektor](../anomaly-detector-container-howto.md) tárolót az Azure [Container Instancesban](https://docs.microsoft.com/azure/container-instances/). Ez az eljárás egy anomália-detektor erőforrás létrehozását mutatja be. Ezután megbeszéljük a társított tároló-rendszerkép kihúzását. Végezetül kiemeljük a két böngészőből való összehangolás lehetőségét. A tárolók használatával az alkalmazások fejlesztésére összpontosíthat a fejlesztők figyelmét arra, hogy az infrastruktúra kezelése helyett.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>A privát tárolóregisztrációs hozzáférés kérése

Először be kell fejeznie és el kell küldenie az anomália- [detektor tárolójának kérelmi űrlapját](https://aka.ms/adcontainer) , hogy hozzáférést Kérjen a tárolóhoz.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>További lépések

* A tároló rendszerképének áthúzásához és a tároló futtatásához tekintse át a tárolók [telepítése és futtatása](../anomaly-detector-container-configuration.md)
* Felülvizsgálat [tárolók konfigurálása](../anomaly-detector-container-configuration.md) a konfigurációs beállítások
* [További információ az anomália-érzékelő API szolgáltatásáról](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
