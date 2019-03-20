---
title: Egy Azure-megoldás – oktatóanyag – Azure IoT-eszköz figyelése |} A Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan monitorozhatja IoT-eszközeit a távoli monitorozási megoldásgyorsítóval.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: d6d850fa8f896809318be77529e10abddaf6ea9a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58173821"
---
# <a name="tutorial-monitor-your-iot-devices"></a>Oktatóanyag: Az IoT-eszközök figyelése

Ebben az oktatóanyagban a távoli monitorozási megoldásgyorsítóval monitorozhatja a csatlakoztatott IoT-eszközöket. A megoldás irányítópultján megtekintheti a telemetriai adatokat, az eszközadatokat, a riasztásokat és a fő teljesítménymutatókat.

Az oktatóanyag két teherautóban található szimulált eszközt használ, amelyek helyre, sebességre és rakomány-hőmérsékletre vonatkozó telemetriát küldenek. A teherautókat egy Contoso nevű cég kezeli, és kapcsolódnak a távoli monitorozási megoldásgyorsítóhoz. A Contoso egyik operátoraként Önnek figyelemmel kell kísérnie az egyik teherautó (truck-02) helyzetét és viselkedését a terepen.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

>[!div class="checklist"]
> * Az irányítópulton található eszközök szűrése
> * Valós idejű telemetriai adatok megtekintése
> * Eszközadatok megtekintése
> * Eszközökről érkező riasztások megtekintése
> * A rendszer fő teljesítménymutatóinak megtekintése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>A megjelenítendő eszközök kiválasztása

Az **Irányítópult** oldalon megjelenítendő csatlakoztatott eszközök kiválasztásához használjon szűrőket. Ha kizárólag a **Teherautó** típusú eszközöket szeretné megjeleníteni, válassza a beépített **Teherautók** szűrőt a legördülő szűrőlistában:

[![Teherautók keresése szűréssel az irányítópulton](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

Szűrő alkalmazásakor csak azok az eszközök jelennek meg a térképen és a telemetria panelen, amelyek megfelelnek a szűrés feltételeinek. Láthatja, hogy a megoldásgyorsítóhoz két teherautó csatlakozik, beleértve a truck-02 teherautót is:

[![Kizárólag teherautók jelennek meg a térképen](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

A szűrők létrehozásához, szerkesztéséhez és törléséhez kattintson az **Eszközcsoportok kezelése** lehetőségre.

## <a name="view-real-time-telemetry"></a>Valós idejű telemetriai adatok megtekintése

A megoldásgyorsító valós idejű telemetriai adatokat ábrázol az **Irányítópult** lapon található diagramon. A telemetriai adatok diagram tetején a jelenlegi szűrővel kiválasztott eszközökhöz, így a truck-02 teherautóhoz is elérhető telemetria-típusokat láthatja: Alapértelmezés szerint a diagram a teherautók földrajzi szélességét mutatja, és a truck-02 teherautó mozdulatlannak tűnik:

[![Teherautótelemetria-típusok](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

A teherautók hőmérsékleti telemetriájának megtekintéséhez kattintson a **Hőmérséklet** elemre. Láthatja, hogyan változott a truck-02 teherautó hőmérséklete az elmúlt órában:

[![Teherautó hőmérsékleti telemetria-diagramja](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="view-the-map"></a>A térkép megtekintése

A térkép az aktuális szűrővel kiválasztott szimulált teherautókról jelenít meg információkat. A térkép nagyításával és pásztázásával nagyobb vagy kisebb részletességgel tekintheti meg a helyeket. A térképen található eszközikon színe azt mutatja meg, hogy az adott eszköz rendelkezik-e aktív **Riasztásokkal**(sötétkék) vagy **Figyelmeztetésekkel** (vörös). A térképtől balra láthatja a **Riasztások** és **Figyelmeztetések** összesített számát.

A truck-02 teherautó részletes adatainak megtekintéséhez keresse meg azt a térkép nagyításával és pásztázásával, majd válassza ki a teherautót a térképen. Ezután kattintson az eszköz címkéjére az **Eszközadatok** panel megnyitásához. Az eszközadatok a következők:

* Új telemetria-értékek
* Az eszköz által támogatott metódusok
* Eszköztulajdonságok

[![Eszközadatok megtekintése az irányítópulton](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>Riasztások megtekintése

A **Riasztások** panel az eszközökről érkező legújabb riasztások részletes adatait jeleníti meg. A truck-02 teherautóból érkező riasztások a megszokottnál magasabb rakomány-hőmérsékletet jeleznek:

[![Eszközriasztások megtekintése az irányítópulton](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

A szűrőkkel beállíthatja az új riasztások időtartományát. Alapértelmezés szerint a panel az elmúlt óra riasztásait jeleníti meg.

## <a name="view-the-system-kpis"></a>A rendszer fő teljesítménymutatóinak megtekintése

Az **Irányítópult** lap megjeleníti a megoldásgyorsító által kiszámított fő rendszer-teljesítménymutatókat az **Elemzés** panelen:

[![Irányítópulton látható fő teljesítménymutatók](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

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