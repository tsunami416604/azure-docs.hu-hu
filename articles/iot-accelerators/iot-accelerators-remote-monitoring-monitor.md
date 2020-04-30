---
title: Eszközök figyelése a távoli figyelési megoldásban – Azure | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan monitorozhatja IoT-eszközeit a távoli monitorozási megoldásgyorsítóval.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 400a71b11fde210b889d938041e88c5ebe73c1dc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "73890868"
---
# <a name="tutorial-monitor-your-iot-devices"></a>Oktatóanyag: IoT-eszközök monitorozása

Ebben az oktatóanyagban a távoli monitorozási megoldásgyorsítóval monitorozhatja a csatlakoztatott IoT-eszközöket. A megoldás irányítópultján megtekintheti a telemetriai adatokat, az eszközadatokat, a riasztásokat és a fő teljesítménymutatókat.

Az oktatóanyag két teherautóban található szimulált eszközt használ, amelyek helyre, sebességre és rakomány-hőmérsékletre vonatkozó telemetriát küldenek. A teherautókat egy Contoso nevű cég kezeli, és kapcsolódnak a távoli monitorozási megoldásgyorsítóhoz. A Contoso egyik operátoraként Önnek figyelemmel kell kísérnie az egyik teherautó (truck-02) helyzetét és viselkedését a terepen.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

>[!div class="checklist"]
> * Az irányítópulton található eszközök szűrése
> * Valós idejű telemetriai adatok megtekintése
> * Eszközadatok megtekintése
> * Eszközökről érkező riasztások megtekintése
> * A rendszer fő teljesítménymutatóinak megtekintése

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>A megjelenítendő eszközök kiválasztása

Az **Irányítópult** oldalon megjelenítendő csatlakoztatott eszközök kiválasztásához használjon szűrőket. Ha kizárólag a **Teherautó** típusú eszközöket szeretné megjeleníteni, válassza a beépített **Teherautók** szűrőt a legördülő szűrőlistában:

[![Teherautók szűrése az irányítópulton](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

Szűrő alkalmazásakor csak azok az eszközök jelennek meg a térképen és a telemetria panelen, amelyek megfelelnek a szűrés feltételeinek. Láthatja, hogy a megoldásgyorsítóhoz két teherautó csatlakozik, beleértve a truck-02 teherautót is:

[![Csak a teherautók jelennek meg a térképen](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

A szűrők létrehozásához, szerkesztéséhez és törléséhez kattintson az **Eszközcsoportok kezelése** lehetőségre.

## <a name="view-real-time-telemetry"></a>Valós idejű telemetriai adatok megtekintése

A megoldásgyorsító valós idejű telemetriai adatokat ábrázol az **Irányítópult** lapon található diagramon. A telemetriai adatok diagram tetején a jelenlegi szűrővel kiválasztott eszközökhöz, így a truck-02 teherautóhoz is elérhető telemetria-típusokat láthatja: Alapértelmezés szerint a diagram a teherautók földrajzi szélességét mutatja, és a truck-02 teherautó mozdulatlannak tűnik:

[![Truck telemetria-típusok](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

A teherautók hőmérsékleti telemetriájának megtekintéséhez kattintson a **Hőmérséklet** elemre. Láthatja, hogyan változott a truck-02 teherautó hőmérséklete az elmúlt órában:

[![Truck hőmérséklet telemetria ábrázolása](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="view-the-map"></a>A térkép megtekintése

A térkép az aktuális szűrővel kiválasztott szimulált teherautókról jelenít meg információkat. A térkép nagyításával és pásztázásával nagyobb vagy kisebb részletességgel tekintheti meg a helyeket. A térképen található eszközikon színe azt mutatja meg, hogy az adott eszköz rendelkezik-e aktív **Riasztásokkal**(sötétkék) vagy **Figyelmeztetésekkel** (vörös). A térképtől balra láthatja a **Riasztások** és **Figyelmeztetések** összesített számát.

A truck-02 teherautó részletes adatainak megtekintéséhez keresse meg azt a térkép nagyításával és pásztázásával, majd válassza ki a teherautót a térképen. Ezután kattintson az eszköz címkéjére az **Eszközadatok** panel megnyitásához. Az eszközadatok a következők:

* Új telemetria-értékek
* Az eszköz által támogatott metódusok
* Eszköztulajdonságok

[![Eszköz adatainak megtekintése az irányítópulton](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>Riasztások megtekintése

A **riasztások** panel részletes információkat jelenít meg az eszközökről érkező legutóbbi riasztásokról. A truck-02 teherautóból érkező riasztások a megszokottnál magasabb rakomány-hőmérsékletet jeleznek:

[![Eszköz-riasztások megtekintése az irányítópulton](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

A szűrőkkel beállíthatja az új riasztások időtartományát. Alapértelmezés szerint a panel az elmúlt óra riasztásait jeleníti meg.

## <a name="view-the-system-kpis"></a>A rendszer fő teljesítménymutatóinak megtekintése

Az **Irányítópult** lap megjeleníti a megoldásgyorsító által kiszámított fő rendszer-teljesítménymutatókat az **Elemzés** panelen:

[![Irányítópult KPI-k](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

Az irányítópult három fő teljesítménymutatót jelenít meg az aktuális eszköz- és időtartamszűrőkkel kiválasztott riasztásokról:

* A legtöbb riasztást kiváltó szabályokhoz tartozó aktív riasztások száma.
* A riasztások aránya eszköztípusonként.
* A kritikus riasztások százalékos aránya.

A truck-02 teherautónál minden riasztás a megszokottnál magasabb rakomány-hőmérsékletre figyelmeztet.

A riasztások időtartamát és a megjelenítendő eszközöket megadó szűrők határozzák meg a fő teljesítménymutatók összesítésének módját is. Alapértelmezés szerint a panel az elmúlt órában összesített fő teljesítménymutatókat jeleníti meg.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ezen oktatóanyag azt mutatta be, hogyan használhatja az **Irányítópult** oldalt a Távoli monitorozás megoldásgyorsítóban a szimulált teherautók szűréséhez és monitorozásához. A következő oktatóanyagból megtudhatja, hogyan használhatja a megoldásgyorsítót a csatlakoztatott eszközök problémáinak észlelésére.

> [!div class="nextstepaction"]
> [A monitorozási megoldáshoz csatlakoztatott eszközök problémáinak észlelése](iot-accelerators-remote-monitoring-automate.md)