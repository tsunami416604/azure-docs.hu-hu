---
title: IoT-eszközök monitorozása Azure-megoldásokból | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan monitorozhatja IoT-eszközeit a távoli monitorozási megoldásgyorsítóval.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 5f42ed0fa5362959e5619f2d550ca1ae3711ed65
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097461"
---
# <a name="tutorial-monitor-your-iot-devices"></a>Oktatóanyag: IoT-eszközök monitorozása

Ebben az oktatóanyagban a távoli monitorozási megoldásgyorsítóval monitorozhatja a csatlakoztatott IoT-eszközöket. A megoldás irányítópultján megtekintheti a telemetriai adatokat, az eszközadatokat, a riasztásokat és a fő teljesítménymutatókat.

A monitorozási funkciók bemutatásához az oktatóanyag két teherautóban található szimulált eszközt használ. A teherautókat egy Contoso nevű cég kezeli, és kapcsolódnak a távoli monitorozási megoldásgyorsítóhoz. A Contoso egyik operátoraként Önnek figyelemmel kell kísérnie a teherautók helyzetét és viselkedését a terepen.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

>[!div class="checklist"]
> * Az irányítópulton található eszközök szűrése
> * Valós idejű telemetriai adatok megtekintése
> * Eszközadatok megtekintése
> * Eszközökről érkező riasztások megtekintése
> * A rendszer fő teljesítménymutatóinak megtekintése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag követéséhez rendelkeznie kell a távoli monitorozási megoldásgyorsító üzembe helyezett példányával az Azure-előfizetésében.

Ha még nem helyezte üzembe a távoli monitorozási megoldásgyorsítót, végezze el [Távoli felhőalapú monitorozási megoldás üzembe helyezése](quickstart-remote-monitoring-deploy.md) rövid útmutatóban leírtakat.

## <a name="choose-the-devices-to-display"></a>A megjelenítendő eszközök kiválasztása

Az **Irányítópult** oldalon megjelenítendő csatlakoztatott eszközök kiválasztásához használjon szűrőket. Ha kizárólag a **Teherautó** típusú eszközöket szeretné megjeleníteni, válassza a beépített **Teherautók** szűrőt a legördülő szűrőlistában:

[![Teherautók keresése szűréssel az irányítópulton](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

Szűrő alkalmazásakor csak azok az eszközök jelennek meg az **Irányítópult** lap térképén, amelyek megfelelnek a szűrés feltételeinek:

[![Kizárólag teherautók jelennek meg a térképen](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

A szűrő is meghatározza, hogy mely eszközök láthatók a **Telemetriai adatok** diagramon:

[![A teherautó telemetriai adatai jelennek meg az irányítópulton](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-expanded.png#lightbox)

A szűrők létrehozásához, szerkesztéséhez és törléséhez válassza az **Eszközcsoportok kezelése** lehetőséget.

## <a name="view-real-time-telemetry"></a>Valós idejű telemetriai adatok megtekintése

A megoldásgyorsító valós idejű telemetriai adatokat ábrázol az **Irányítópult** lapon található diagramon. A telemetriai adatok diagram tetején a jelenlegi szűrővel kiválasztott eszközökhöz elérhető telemetria-típusokat láthatja:

[![Teherautótelemetria-típusok](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

A hőmérsékleti telemetria megtekintéséhez kattintson a **Hőmérséklet** elemre:

[![Teherautó hőmérsékleti telemetria-diagramja](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="use-the-map"></a>A térkép használata

A térkép az aktuális szűrővel kiválasztott szimulált teherautókról jelenít meg információkat. A térkép nagyításával és pásztázásával nagyobb vagy kisebb részletességgel tekintheti meg a helyeket. A térképen található eszközikon színe azt mutatja meg, hogy az adott eszköz rendelkezik-e aktív **Riasztásokkal** vagy **Figyelmeztetésekkel**. A térképtől balra láthatja a **Riasztások** és **Figyelmeztetések** összesített számát.

Az eszközadatok megtekintéséhez keresse meg az eszközt a térkép nagyításával és pásztázásával, majd válassza ki az eszközt a térképen. Ezután kattintson az eszköz címkéjére az **Eszközadatok** panel megnyitásához. Az eszközadatok a következők:

* Új telemetria-értékek
* Az eszköz által támogatott metódusok
* Eszköztulajdonságok

[![Eszközadatok megtekintése az irányítópulton](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>Riasztások megtekintése

A **Riasztások** panel az eszközökről érkező legújabb riasztások részletes adatait jeleníti meg:

[![Eszközriasztások megtekintése az irányítópulton](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

A szűrőkkel beállíthatja az új riasztások időtartományát. Alapértelmezés szerint a panel az elmúlt óra riasztásait jeleníti meg:

[![Riasztások idő szerinti szűrése](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-expanded.png#lightbox)

## <a name="view-the-system-kpis"></a>A rendszer fő teljesítménymutatóinak megtekintése

Az **Irányítópult** lap megjeleníti a megoldásgyorsító által kiszámított fő rendszer-teljesítménymutatókat az **Elemzés** panelen:

[![Irányítópulton látható fő teljesítménymutatók](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

Az irányítópult három fő teljesítménymutatót jelenít meg az aktuális eszköz- és időtartamszűrőkkel kiválasztott riasztásokról:

* A legtöbb riasztást kiváltó szabályokhoz tartozó aktív riasztások száma.
* A riasztások aránya eszköztípusonként.
* A kritikus riasztások százalékos aránya.

A riasztások időtartamát és a megjelenítendő eszközöket megadó szűrők határozzák meg a fő teljesítménymutatók összesítésének módját is. Alapértelmezés szerint a panel az elmúlt órában összesített fő teljesítménymutatókat jeleníti meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább kíván lépni a következő oktatóanyagra, ne kapcsolja ki a távoli monitorozási megoldásgyorsítót. Ha csökkenteni szeretné a megoldásgyorsító futtatásának költségeit, amíg nem használja, állítsa le a szimulált eszközöket a beállítások panelen:

[![Telemetria szüneteltetése](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-expanded.png#lightbox)

Ha készen áll a következő oktatóanyag megkezdésére, újraindíthatja a szimulált eszközöket.

Ha már nincs szüksége a megoldásgyorsítóra, törölje a [Kiépített megoldások](https://www.azureiotsolutions.com/Accelerators#dashboard) lapról:

![Megoldás törlése](media/iot-accelerators-remote-monitoring-monitor/deletesolution.png)

## <a name="next-steps"></a>További lépések

Ezen oktatóanyag azt mutatta be, hogyan használhatja az **Irányítópult** oldalt a Távoli monitorozás megoldásgyorsítóban a szimulált teherautók szűréséhez és monitorozásához. A következő oktatóanyagból megtudhatja, hogyan használhatja a megoldásgyorsítót a csatlakoztatott eszközök problémáinak észlelésére.

> [!div class="nextstepaction"]
> [A monitorozási megoldáshoz csatlakoztatott eszközök problémáinak észlelése](iot-accelerators-remote-monitoring-automate.md)