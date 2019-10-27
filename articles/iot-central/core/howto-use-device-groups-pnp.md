---
title: Erőforráscsoportok használata az Azure IoT Central alkalmazásban | Microsoft Docs
description: Kezelőként az Azure IoT Central-alkalmazásban található eszközosztály használata.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 91aae53ae81d2bc7cfda54cefa786e4b1a1aab67
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949663"
---
# <a name="use-device-groups-in-your-azure-iot-central-application-preview-features"></a>Erőforráscsoportok használata az Azure IoT Central alkalmazásban (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ez a cikk azt ismerteti, hogyan használhatók az erőforráscsoportok az Azure IoT Central-alkalmazásban.

Az eszközcsoport azoknak az eszközöknek a listája, amelyek egy csoportba vannak csoportosítva, mert megfelelnek néhány megadott feltételnek. Az eszközcsoport megkönnyíti az eszközök méretezését, megjelenítését és elemzését az eszközök kisebb és logikai csoportokba csoportosításával. Létrehozhat például egy eszközt a Seattle-ben lévő összes légkondicionáló eszköz listázásához, hogy a technikus megkeresse azokat az eszközöket, amelyekhez felelősek. Ebből a cikkből megtudhatja, hogyan hozhat létre és konfigurálhat eszközosztály-csoportokat.

>  [!NOTE] 
> Azure IoT Edge eszközökhöz ki kell választania Azure IoT Edge sablonokat az eszközcsoport létrehozásához.

## <a name="create-a-device-group"></a>Eszközcsoport létrehozása

Eszközcsoport létrehozása:

1. Válassza ki az eszközcsoport **elemet a bal** oldali ablaktáblán.

1. Válassza az **+ új**lehetőséget.

    ![Új eszközcsoport](media/howto-use-device-groups-pnp/image1.png)

1. Adja meg az eszköz csoportjának a teljes alkalmazásban egyedi nevet. Leírást is hozzáadhat a szolgáltatáshoz. Az eszközcsoport csak egyetlen eszköz sablonból származó eszközöket tartalmazhat. Válassza ki a csoporthoz használni kívánt sablont.

1. A lekérdezés létrehozásával azonosíthatja az eszközcsoport eszközeit egy tulajdonság, egy összehasonlító operátor és egy érték kiválasztásával. Több olyan lekérdezést és eszközt is hozzáadhat, amelyek megfelelnek az **összes** feltételnek az eszköz csoportjának. Az Ön által létrehozott eszközcsoport mindenki számára elérhető, aki hozzáfér az alkalmazáshoz, így bárki megtekintheti, módosíthatja vagy törölheti az eszközt.

    ![Eszközcsoport-lekérdezés](media/howto-use-device-groups-pnp/image2.png)

    > [!NOTE]
    > Az eszközcsoport dinamikus lekérdezés. Minden alkalommal, amikor megtekinti az eszközök listáját, lehet, hogy a listában különböző eszközök szerepelnek. A lista attól függ, hogy mely eszközök felelnek meg a lekérdezés feltételeinek.

1. Válassza a **Mentés** elemet.

## <a name="analytics"></a>Elemzés

A bal oldali panelen a fő elemzési lapon megjelenő elemzési eszközök. Az elemzéssel kapcsolatos további információkért tekintse meg az [elemzések létrehozásáról](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)szóló cikket.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan használhatja az eszközök csoportjait az Azure IoT Central alkalmazásban, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Telemetria-szabályok létrehozása](howto-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
