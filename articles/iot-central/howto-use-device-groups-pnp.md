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
ms.openlocfilehash: 07f1df101442e8ae43b26cebc60c8452d475d0f3
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879746"
---
# <a name="use-device-groups-in-your-azure-iot-central-application-preview-features"></a>Erőforráscsoportok használata az Azure IoT Central alkalmazásban (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Ez a cikk azt ismerteti, hogyan használhatók az erőforráscsoportok az Azure IoT Central-alkalmazásban.

Az eszközcsoport azoknak az eszközöknek a listája, amelyek egy csoportba vannak csoportosítva, mert megfelelnek néhány megadott feltételnek. Az eszközcsoport megkönnyíti az eszközök méretezését, megjelenítését és elemzését az eszközök kisebb és logikai csoportokba csoportosításával. Létrehozhat például egy eszközt a Seattle-ben lévő összes légkondicionáló eszköz listázásához, hogy a technikus megkeresse azokat az eszközöket, amelyekhez felelősek. Ebből a cikkből megtudhatja, hogyan hozhat létre és konfigurálhat eszközosztály-csoportokat.

## <a name="create-a-device-group"></a>Eszközcsoport létrehozása

Eszközcsoport létrehozása:

1. A bal oldali navigációs menüben válassza az **erőforráscsoportok** lehetőséget.

1. Válassza az **+ új**lehetőséget.

    ![Új eszközcsoport](media/howto-use-device-groups-pnp/image1.png)

1. Adja meg az eszköz csoportjának a teljes alkalmazásban egyedi nevet. Hozzáadhat egy leírást is. Az eszközcsoport csak egyetlen eszköz sablonból származó eszközöket tartalmazhat. Válassza ki a csoporthoz használni kívánt sablont.

1. A lekérdezés létrehozásával azonosíthatja az eszközcsoport eszközeit egy tulajdonság, egy összehasonlító operátor és egy érték kiválasztásával. Több olyan lekérdezést és eszközt is hozzáadhat, amelyek megfelelnek az **összes** feltételnek az eszköz csoportjának. Az Ön által létrehozott eszközcsoport mindenki számára elérhető, aki hozzáfér az alkalmazáshoz, így bárki megtekintheti, módosíthatja vagy törölheti az eszközt.

    ![Eszközcsoport-lekérdezés](media/howto-use-device-groups-pnp/image2.png)

    > [!NOTE]
    > Az eszközcsoport dinamikus lekérdezés. Minden alkalommal, amikor megtekinti az eszközök listáját, lehet, hogy a listában különböző eszközök szerepelnek. A lista attól függ, hogy mely eszközök felelnek meg a lekérdezés feltételeinek.

1. Válassza a **Mentés** elemet.

## <a name="analytics"></a>Elemzés

A bal oldali navigációs menüben a fő elemzési lapon megjelenő elemzési eszközök. Az elemzéssel kapcsolatos további információkért tekintse meg az [elemzések létrehozásáról](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)szóló cikket.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan használhatja az eszközök csoportjait az Azure IoT Central alkalmazásban, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Telemetria-szabályok létrehozása](howto-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
