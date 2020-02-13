---
title: Erőforráscsoportok használata az Azure IoT Central alkalmazásban | Microsoft Docs
description: Kezelőként megtudhatja, hogyan használhatja az telemetria az Azure IoT Central-alkalmazás eszközeiből származó eszközök elemzéséhez.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 758ac037fcd224d02f62239b3408b41b50390147
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77167226"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Oktatóanyag: eszköz-csoportok használata az eszközök telemetria elemzéséhez

Ez a cikk azt ismerteti, hogyan használhatók az eszközök a telemetria az Azure IoT Central-alkalmazásban található eszközökhöz.

Az eszközcsoport azoknak az eszközöknek a listája, amelyek egy csoportba vannak csoportosítva, mert megfelelnek néhány megadott feltételnek. Az eszközcsoport megkönnyíti az eszközök méretezését, megjelenítését és elemzését az eszközök kisebb és logikai csoportokba csoportosításával. Létrehozhat például egy eszközt a Seattle-ben lévő összes légkondicionáló eszköz listázásához, hogy a technikus megkeresse azokat az eszközöket, amelyekhez felelősek.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Eszközcsoport létrehozása
> * Eszköz-csoport használata az eszközök telemetria elemzéséhez

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, hajtsa végre az [Azure IoT Central-alkalmazás létrehozása](./quick-deploy-iot-central.md) és [egy szimulált eszköz hozzáadása a IoT Central alkalmazás](./quick-create-pnp-device.md) -gyors útmutatóhoz a **MXChip IoT fejlesztői készlet** -sablon létrehozásához.

## <a name="create-simulated-devices"></a>Szimulált eszközök létrehozása

Az eszközcsoport létrehozása előtt adjon hozzá legalább öt szimulált eszközt a **MXChip IoT fejlesztői készlet** -sablonhoz az oktatóanyagban való használathoz:

![Öt szimulált érzékelő eszköz](./media/tutorial-use-device-groups/simulated-devices.png)

Négy szimulált érzékelő eszköz esetében használja az **eszköz kezelése** nézetet az ügyfél nevének a *contoso*-ba történő beállításához:

![Ügyfél nevének beállítása a contoso-ra](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Eszközcsoport létrehozása

Eszközcsoport létrehozása:

1. Válassza ki az eszközcsoport **elemet a bal** oldali ablaktáblán.

1. **+** kiválasztása:

    ![Új eszközcsoport](media/tutorial-use-device-groups/image1.png)

1. Adja meg az eszköz csoportjának a *contoso-eszközök*nevet. Hozzáadhat egy leírást is. Az eszközcsoport csak egyetlen eszköz sablonból származó eszközöket tartalmazhat. Válassza ki a csoporthoz használni kívánt **MXChip-IoT fejlesztői készlet** .

1. Ha úgy szeretné testre szabni az erőforráscsoportot, hogy csak a **contosohoz**tartozó eszközöket tartalmazza, válassza a **+ szűrő**lehetőséget. Válassza ki az **ügyfél neve** tulajdonságot, az **Equals** összehasonlító operátort és a **contoso** értéket. Több szűrőt és eszközt is hozzáadhat, amelyek megfelelnek az **összes** szűrési feltételnek az eszköz csoportban. Az Ön által létrehozott eszközcsoport mindenki számára elérhető, aki hozzáfér az alkalmazáshoz, így bárki megtekintheti, módosíthatja vagy törölheti az eszköz csoportját:

    ![Eszközcsoport-lekérdezés](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > Az eszközcsoport dinamikus lekérdezés. Minden alkalommal, amikor megtekinti az eszközök listáját, lehet, hogy a listában különböző eszközök szerepelnek. A lista attól függ, hogy mely eszközök felelnek meg a lekérdezés feltételeinek.

1. Válassza a **Mentés** elemet.

> [!NOTE]
> Azure IoT Edge eszközök esetében válassza ki Azure IoT Edge sablonokat az eszközcsoport létrehozásához.

## <a name="analytics"></a>Elemzés

Az **elemzési** eszköz segítségével a csoport eszközeiből elemezheti a telemetria. Például a contoso környezeti érzékelők által jelentett átlagos hőmérsékletet ábrázolhatja.

Eszközcsoport telemetria elemzése:

1. Válassza az **elemzés** lehetőséget a bal oldali ablaktáblán.

1. Válassza ki a **contoso-eszközök** létrehozott csoportját. Ezután adja hozzá a **hőmérséklet** és a **páratartalom** telemetria típusát:

    ![Elemzés létrehozása](./media/tutorial-use-device-groups/create-analysis.png)

    Az összesítési típus kiválasztásához használja a telemetria-típusok melletti fogaskerék ikonokat. Az alapértelmezett érték az **átlag**. Az összesített adatmegjelenítés módjának megváltoztatásához használja a **felosztási** lehetőséget. Ha például az eszköz AZONOSÍTÓját választja, akkor az **elemzés**lehetőség kiválasztásakor megjelenik az egyes eszközökre vonatkozó ábra.

1. Az átlagos telemetria-értékek megtekintéséhez válassza az **elemzés** lehetőséget:

    ![Elemzés megtekintése](./media/tutorial-use-device-groups/view-analysis.png)

    Testreszabhatja a nézetet, megváltoztathatja a megjelenített időszakot, és exportálhatja az adategységeket.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan használhatja az eszközök csoportjait az Azure IoT Central-alkalmazásban, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Telemetria-szabályok létrehozása](tutorial-create-telemetry-rules.md)
