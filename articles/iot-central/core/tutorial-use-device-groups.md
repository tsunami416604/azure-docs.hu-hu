---
title: Eszközcsoportok használata az Azure IoT Central alkalmazásban | Microsoft dokumentumok
description: Operátorként megtudhatja, hogyan használhatja az eszközcsoportokat az Azure IoT Central-alkalmazás eszközeiről származó telemetriai adatok elemzésére.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 758ac037fcd224d02f62239b3408b41b50390147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167226"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Oktatóanyag: Eszközcsoportok használata az eszköztelemetria elemzéséhez

Ez a cikk ismerteti, hogyan, operátorként eszközcsoportok segítségével elemezheti az eszköz telemetriai adatait az Azure IoT Central-alkalmazásban.

Az eszközcsoport azoknak az eszközöknek a listája, amelyek azért vannak csoportosítva, mert megfelelnek bizonyos megadott feltételeknek. Az eszközcsoportok segítségével kezelheti, vizualizálhatja és elemezheti az eszközöket, ha kisebb, logikai csoportokba csoportosítja az eszközöket. Létrehozhat például egy eszközcsoportot, amely felsorolja az összes seattle-i légkondicionáló eszközt, hogy a technikus megtalálja azokat az eszközöket, amelyekért felelősek.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Eszközcsoport létrehozása
> * Eszközcsoport használata az eszköztelemetria elemzéséhez

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, töltse ki az [Azure IoT Central-alkalmazás létrehozása](./quick-deploy-iot-central.md) és [egy szimulált eszköz hozzáadása az IoT Central alkalmazás](./quick-create-pnp-device.md) rövid útmutatói hozhatja létre az **MXChip IoT DevKit** eszközsablon dolgozni.

## <a name="create-simulated-devices"></a>Szimulált eszközök létrehozása

Eszközcsoport létrehozása előtt adjon hozzá legalább öt szimulált eszközt az **MXChip IoT DevKit** eszközsablonból, amelyet ebben az oktatóanyagban kell használnia:

![Öt szimulált érzékelőeszköz](./media/tutorial-use-device-groups/simulated-devices.png)

Négy szimulált érzékelőeszköz esetében az **Eszköz kezelése** nézetben állítsa az ügyfél nevét *Contoso-ra:*

![Vevő nevének beállítása Contoso-ra](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Eszközcsoport létrehozása

Eszközcsoport létrehozása:

1. Válassza **az Eszközcsoportok lehetőséget** a bal oldali ablaktáblán.

1. Válassza **+** ki a következőt:

    ![Új eszközcsoport](media/tutorial-use-device-groups/image1.png)

1. Adja meg az eszközcsoportnak a *Contoso-eszközök*nevet. Leírást is hozzáadhat. Egy eszközcsoport csak egyetlen eszközsablonból származó eszközöket tartalmazhat. Válassza ki az **MXChip IoT DevKit** eszközsablont, amelyet ehhez a csoporthoz szeretne használni.

1. Ha úgy szeretné testreszabni az eszközcsoportot, hogy csak a **Contoso-hoz**tartozó eszközök szerepeljenek, válassza a **+ Szűrő lehetőséget.** Válassza ki a **Vevő neve** tulajdonságot, az Egyenlő összehasonlító **operátort** és a **Contoso értéket.** Több szűrőt is hozzáadhat, és az **összes** szűrőfeltételnek megfelelő eszköz az eszközcsoportba kerül. A létrehozott eszközcsoport mindenki számára elérhető, aki hozzáfér az alkalmazáshoz, így bárki megtekintheti, módosíthatja vagy törölheti az eszközcsoportot:

    ![Eszközcsoport lekérdezése](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > Az eszközcsoport dinamikus lekérdezés. Minden alkalommal, amikor megtekinti az eszközök listáját, előfordulhat, hogy különböző eszközök vannak a listában. A lista attól függ, hogy jelenleg mely eszközök felelnek meg a lekérdezés feltételeinek.

1. Válassza a **Mentés** elemet.

> [!NOTE]
> Az Azure IoT Edge-eszközök esetén válassza az Azure IoT Edge-sablonokat egy eszközcsoport létrehozásához.

## <a name="analytics"></a>Elemzés

Az **Analytics** eszközcsoporttal a csoportban lévő eszközök telemetriai adatainak elemzéséhez használható. Például ábrázolhatja az összes Contoso-környezetérzékelő által jelentett átlaghőmérsékletet.

Eszközcsoport telemetriai adatainak elemzése:

1. Válassza az **Analytics** lehetőséget a bal oldali ablaktáblán.

1. Válassza ki a **contoso eszközök** eszközcsoportot létrehozott. Ezután adja hozzá a **hőmérséklet-** és páratartalom-telemetriai típusokat is: **Humidity**

    ![Elemzés létrehozása](./media/tutorial-use-device-groups/create-analysis.png)

    A telemetriai típusok melletti fogaskerék-ikonok segítségével válasszon ki egy összesítési típust. Az alapértelmezett érték az **Átlag**. A **Felosztás alapja** segítségével módosíthatja az összesített adatok jelenítse meg. Ha például eszközazonosító szerint osztja fel a felosztást, az **Elemzés**lehetőséget választva minden eszközhöz megjelenik egy nyomtatás.

1. Az **Elemzés** lehetőséget választva megtekintheti az átlagos telemetriai értékeket:

    ![Elemzés megtekintése](./media/tutorial-use-device-groups/view-analysis.png)

    Testreszabhatja a nézetet, módosíthatja a megjelenített időszakot, és exportálhatja az adatokat.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta az eszközcsoportok használatát az Azure IoT Central alkalmazásban, az alábbiakat a javasolt következő lépésként ismerte el:

> [!div class="nextstepaction"]
> [Telemetriai szabályok létrehozása](tutorial-create-telemetry-rules.md)
