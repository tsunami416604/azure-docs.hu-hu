---
title: Anomália-érzékelő tároló futtatása az Azure-tárolópéldányokban
titleSuffix: Azure Cognitive Services
description: Telepítse az Anomália-érzékelő tárolóegy Azure Container Instance, és tesztelje azt egy webböngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 2fba0a0d64502a30b6dfbc9f4f109bca65cca8b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76716353"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Anomália-érzékelő tároló üzembe helyezése az Azure Container-példányokba

Ismerje meg, hogyan telepítheti a Cognitive Services [anomália-érzékelő](../anomaly-detector-container-howto.md) tárolóját az Azure [Container Instances szolgáltatásba.](https://docs.microsoft.com/azure/container-instances/) Ez az eljárás egy anomáliadetektor-erőforrás létrehozását mutatja be. Ezután megbeszéljük a kapcsolódó tárolókép húzását. Végül kiemeljük a két vezénylési lehetőségének gyakorlását egy böngészőből. A tárolók használatával a fejlesztők figyelmét elterelheti az infrastruktúra kezeléséről az alkalmazásfejlesztésre való összpontosításra.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Hozzáférés kérése a privát tároló beállításjegyzékéhez

Először ki kell töltenie, és el kell küldenie az [Anomália-detektor tároló kérelem űrlap](https://aka.ms/adcontainer) hozzáférést kérni a tárolóhoz.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>További lépések

* Tekintse [át a Tárolórendszerkép](../anomaly-detector-container-configuration.md) lehúzásához és futtatásához szükséges tárolók telepítését és futtatását
* Tekintse át [a tárolók konfigurálása](../anomaly-detector-container-configuration.md) a konfigurációs beállításokat
* [További információ az Anomáliadetektor API-szolgáltatásról](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
