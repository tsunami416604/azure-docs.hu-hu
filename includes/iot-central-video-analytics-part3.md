---
title: fájlbefoglalás
description: fájlbefoglalás
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 60a62733a17d1a3dcc4ba80ed7ceb1c37c8ac5d6
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877188"
---
## <a name="create-the-azure-iot-edge-gateway-device"></a>Az Azure IoT Edge Gateway-eszköz létrehozása

A video Analytics – Object és Motion észlelési alkalmazás tartalmaz egy **LVA Edge Object detektor** -eszközt és egy **LVA Edge mozgásérzékelő-észlelési** eszköz sablonját. Ebben a szakaszban egy átjáró-eszköz sablont hoz létre a központi telepítési jegyzékfájl használatával, és hozzáadja az átjáró eszközt a IoT Central alkalmazáshoz.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>Eszköz sablonjának létrehozása a LVA Edge-átjáróhoz

Az üzembe helyezési jegyzék importálása és az **LVA Edge Gateway** -sablon létrehozása:

1. A IoT Central alkalmazásban navigáljon az **eszközök sablonjaihoz**, és válassza az **+ új**lehetőséget.

1. A **sablon típusának kiválasztása** lapon válassza a **Azure IoT Edge** csempét. Ezután válassza a **Tovább: testreszabás**lehetőséget.

1. Az **Azure IoT Edge üzembe helyezési jegyzék feltöltése** lapon adja meg a *LVA Edge Gateway* nevet a sablon neveként, és tekintse meg az **átjáró eszközét az alsóbb rétegbeli eszközökkel**.

    Az üzembe helyezési jegyzékfájlt még ne keresse meg. Ha így tesz, a központi telepítési varázsló minden modulhoz egy felületet vár, de csak a **LvaEdgeGatewayModule**felületét kell kitennie. A jegyzékfájlt egy későbbi lépésben töltheti fel.

    :::image type="content" source="./media/iot-central-video-analytics-part3/upload-deployment-manifest.png" alt-text="Ne töltse fel az üzembe helyezési jegyzéket":::

    Válassza a **Next: Review** (Tovább: Áttekintés) lehetőséget.

1. Az **Áttekintés** lapon válassza a **Létrehozás**lehetőséget.

### <a name="import-the-device-capability-model"></a>Az eszköz képességi modell importálása

Az eszköz sablonjának tartalmaznia kell egy eszköz-képesség modellt. Az **LVA Edge-átjáró** lapon válassza az **importálási képesség modellje** csempét. Navigáljon a korábban létrehozott *LVA-konfigurációs* mappához, és válassza ki a *LvaEdgeGatewayDcm.js* fájlt.

Az **LVA Edge Gateway** -eszköz most már tartalmazza a **LVA Edge-átjáró modulját** , valamint három felületet: az **eszköz információi**, a **LVA Edge-átjáró beállításai**és az **LVA Edge Gateway felülete**.
