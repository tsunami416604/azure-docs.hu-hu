---
title: A távoli felügyeleti megoldás - Azure figyelés speciális |} Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan figyelheti a távoli felügyeleti megoldás irányítópultja rendelkező eszközök.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 02/22/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 63d5d8de82d97e7f8ca65ad04cdd4357cace0be1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="perform-advanced-monitoring-using-the-remote-monitoring-solution"></a>Hajtsa végre a távoli felügyeleti megoldást használni speciális figyelésére

Ez az oktatóanyag bemutatja a távoli figyelési irányítópult képességeit. Ezek a képességek szemléltetésére az oktatóprogram egy olyan forgatókönyvet a Contoso IoT-alkalmazásban.

Ebben az oktatóanyagban a két szimulált Contoso teherautó eszköz áttekintésével megismerheti, hogyan figyelheti az eszközök a megoldás gyorsító irányítópultról használhatja. Contoso operátorként szeretné figyelni a hely és a mezőben a teherautók viselkedését.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

>[!div class="checklist"]
> * Az irányítópult eszközök szűréséhez
> * Nézet valós idejű telemetriai adat
> * Eszköz részleteinek megtekintése
> * Az eszközök a riasztások megtekintése
> * A rendszer a KPI-k megtekintése

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag van szükség a távoli felügyeleti megoldás telepített példányát az Azure-előfizetésben.

Ha még nem telepítette a távoli figyelési megoldást igényelnek, még el kell végeznie a [telepíteni a távoli felügyeleti megoldásgyorsító](iot-suite-remote-monitoring-deploy.md) oktatóanyag.

## <a name="choose-the-devices-to-display"></a>Válassza ki az eszközök megjelenítéséhez

Jelölje be, mely eszközök megjelenjenek a **irányítópult** lapján szűrők használata. Csak a megjelenítendő a **teherautó** eszközökre, válassza ki a beépített **teherautók** a szűrő legördülő szűrő:

![Az irányítópult teherautók szűrője](media/iot-suite-remote-monitoring-monitor/dashboardtruckfilter.png)

Ha szűrőt alkalmaz, akkor csak a szűrési feltételeknek megfelelő eszközöket megjelenítése a térkép a **irányítópult** lap:

![A térképen teherautók megjelenítése](media/iot-suite-remote-monitoring-monitor/dashboardtruckmap.png)

A szűrő is meghatározza, hogy mely eszközök látható a **Telemetriai** diagram:

![Teherautó telemetriai adatokat jeleníti meg az irányítópulton](media/iot-suite-remote-monitoring-monitor/dashboardtelemetry.png)

Létrehozásához, szerkesztéséhez és szűrők törlése, válassza a **szűrők kezelése**.

## <a name="view-real-time-telemetry"></a>Nézet valós idejű telemetriai adat

A megoldásgyorsító részletes valós idejű telemetriai adatokat a diagramon rajzolja meg a **irányítópult** lap. A telemetriai adatokat a diagram az eszközök, az aktuális szűrőkészlet szerint telemetriai információit jeleníti meg:

![Teherautó telemetriai rajzot](media/iot-suite-remote-monitoring-monitor/dashboardtelemetryview.png)

Válassza ki a telemetriai adatok értékeit, megtekintéséhez, a diagram tetején telemetriai adat típusának kiválasztása:

![Teherautó telemetriai rajzot](media/iot-suite-remote-monitoring-monitor/dashboardselecttelemetry.png)

<!-- 05/01 - this features appears to have been removed
To pause the live telemetry display, choose **Flowing**. To re-enable the live display, choose **Pause**:

![Pause and restart telemetry display](media/iot-suite-remote-monitoring-monitor/dashboardtelemetrypause.png)-->

## <a name="use-the-map"></a>A térkép használata

A térkép a szimulált teherautók szerint az aktuális szűrőkészlet információit jeleníti meg. Nagyítás, és több vagy kevesebb részletes helyeket jelenítsen meg a térképen pásztázáshoz. A térképen az eszköz ikonok jelzik bármely **riasztások** vagy **figyelmeztetések** , amelyek aktívak az eszközön. Hány összegzését **riasztások** és **figyelmeztetések** bal oldalán a térképen jeleníti meg.

<!-- 05/01 - cannot select a deice on the map
To view the device details, pan and zoom the map to locate the devices, then click the device on the map. The details include:

* Recent telemetry values
* Methods the device supports
* Device properties

![View device details on the dashboard](media/iot-suite-remote-monitoring-monitor/dashboarddevicedetail.png)-->

## <a name="view-alerts-from-your-devices"></a>Az eszközök a riasztások megtekintése

A térkép kiemeli az aktuális szűrővel rendelkező eszközök **riasztások** és **figyelmeztetések**. A **riasztások** panel az eszközökről a legfrissebb riasztások részletes információit jeleníti meg:

![Az irányítópult rendszer riasztások megtekintése](media/iot-suite-remote-monitoring-monitor/dashboardsystemalarms.png)

Használhatja a **irányítópult** úgy, hogy a legújabb riasztások időtartamának szűrő. Alapértelmezés szerint a panel megjeleníti a riasztások az elmúlt egy óra:

![A riasztások szűrése idő](media/iot-suite-remote-monitoring-monitor/dashboardalarmsfilter.png)

## <a name="view-the-system-kpis"></a>A rendszer a KPI-k megtekintése

A **irányítópult** lap KPI-k system jelenik meg:

![Irányítópult KPI-k](media/iot-suite-remote-monitoring-monitor/dashboardkpis.png)

Használhatja a **irányítópult** úgy, hogy a KPI-összesítési időtartamának szűrő. A panel alapértelmezés szerint a KPI-k az elmúlt egy óra alatt összesített értéket jelenít meg.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan használható a **irányítópult** lap szűrésére és a távoli felügyeleti megoldás kiépítve a szimulált teherautók figyelése:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Az irányítópult eszközök szűréséhez
> * Nézet valós idejű telemetriai adat
> * Eszköz részleteinek megtekintése
> * Az eszközök a riasztások megtekintése
> * A rendszer a KPI-k megtekintése

Most, hogy az eszközök figyelése megtanulhatta,-e a javasolt lépések megtudhatja, hogyan:

* [Küszöbérték-alapú szabályok használatával kapcsolatos problémák észlelése](./iot-suite-remote-monitoring-automate.md).
* [Kezelése és az eszközök](./iot-suite-remote-monitoring-manage.md).
* [Hibaelhárítás és szervizelheti azokat a eszközökkel kapcsolatos problémákat](./iot-suite-remote-monitoring-maintain.md).
* [A megoldás tesztelése szimulált eszközökkel](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->