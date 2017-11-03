---
title: "A távoli felügyeleti megoldás - Azure figyelés speciális |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan figyelheti a távoli felügyeleti megoldás irányítópultja rendelkező eszközök."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/28/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: d523902505e38ef3fccf60e7648ea5333daae4f7
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="perform-advanced-monitoring-using-the-remote-monitoring-solution"></a>Hajtsa végre a távoli felügyeleti megoldást használni speciális figyelésére

Ez az oktatóanyag bemutatja a távoli figyelési irányítópult képességeit. Ezek a képességek szemléltetésére az oktatóprogram egy olyan forgatókönyvet a Contoso IoT-alkalmazásban.

Az oktatóanyag segítségével két szimulált Contoso teherautó eszköz megtudhatja, hogyan figyelheti az eszközök az előkonfigurált megoldás irányítópulton. Contoso operátorként szeretné figyelni a hely és a mezőben a teherautók viselkedését.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

>[!div class="checklist"]
> * Az irányítópult eszközök szűréséhez
> * Nézet valós idejű telemetriai adat
> * Eszköz részleteinek megtekintése
> * Nézet riasztások adott területen működő eszközök
> * A rendszer a KPI-k megtekintése

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag van szükség a távoli felügyeleti megoldás telepített példányát az Azure-előfizetésben.

Ha még nem telepítette a távoli figyelési megoldást igényelnek, még el kell végeznie a [a távoli felügyeleti előkonfigurált megoldás üzembe helyezéséhez](iot-suite-remote-monitoring-deploy.md) oktatóanyag.

## <a name="choose-the-devices-to-display"></a>Válassza ki az eszközök megjelenítéséhez

Jelölje be, mely eszközök megjelenjenek a **irányítópult** lapján szűrők használata. Csak a megjelenítendő a **teherautó** eszközökre, válassza ki a beépített **teherautók** a szűrő legördülő szűrő:

![Az irányítópult teherautók szűrője](media/iot-suite-remote-monitoring-monitor/dashboardtruckfilter.png)

Ha szűrőt alkalmaz, akkor csak a szűrési feltételeknek megfelelő eszközöket megjelenítése a térkép a **irányítópult** lap:

![A térképen teherautók megjelenítése](media/iot-suite-remote-monitoring-monitor/dashboardtruckmap.png)

A szűrő is meghatározza, hogy mely eszközök látható a **Telemetriai** diagram:

![Teherautó telemetriai adatokat jeleníti meg az irányítópulton](media/iot-suite-remote-monitoring-monitor/dashboardtelemetry.png)

Létrehozásához, szerkesztéséhez és szűrők törlése, válassza a **szűrők kezelése**.

## <a name="view-real-time-telemetry"></a>Nézet valós idejű telemetriai adat

Az előkonfigurált megoldás részletes valós idejű telemetriai adatokat a diagramon rajzolja meg a **irányítópult** lap. A telemetriai adatokat a diagram az eszközök, az aktuális szűrőkészlet szerint telemetriai információit jeleníti meg:

![Teherautó telemetriai rajzot](media/iot-suite-remote-monitoring-monitor/dashboardtelemetryview.png)

Válassza ki a telemetriai adatok értékeit, megtekintéséhez, a diagram tetején telemetriai adat típusának kiválasztása:

![Teherautó telemetriai rajzot](media/iot-suite-remote-monitoring-monitor/dashboardselecttelemetry.png)

Az élő telemetriai megjelenítési szüneteltethető, válassza a **Flowing**. Az élő megjelenítési újbóli engedélyezéséhez válassza **szünet**:

![Szüneteltetése, és indítsa újra a telemetriai adatok megjelenítése](media/iot-suite-remote-monitoring-monitor/dashboardtelemetrypause.png)

## <a name="use-the-map"></a>A térkép használata

A térkép a szimulált teherautók szerint az aktuális szűrőkészlet információit jeleníti meg. Nagyítás, és több vagy kevesebb részletes helyeket jelenítsen meg a térképen pásztázáshoz. A térképen az eszköz ikonok jelzik bármely **riasztások** vagy **figyelmeztetések** , amelyek aktívak az eszközön. Hány összegzését **riasztások** és **figyelmeztetések** bal oldalán a térképen jeleníti meg.

Az eszköz részletes adatainak megtekintéséhez pásztázáshoz Nagyítás a térképen keresse meg az eszközöket, majd kattintson az eszközre a térképen. A részletek a következők:

* Legutóbbi telemetriai értékek
* Az eszköz támogatja metódusok
* Eszköztulajdonságok

![Az irányítópulton lévő eszközök részleteinek megtekintése](media/iot-suite-remote-monitoring-monitor/dashboarddevicedetail.png)

## <a name="view-alarms-from-your-devices"></a>Nézet riasztások adott területen működő eszközök

A térkép kiemeli az aktuális szűrővel rendelkező eszközök **riasztások** és **figyelmeztetések**. A **rendszer riasztások** panel az eszközökről a legutóbbi riasztás részletes adatait jeleníti meg:

![Az irányítópult nézet rendszer riasztások](media/iot-suite-remote-monitoring-monitor/dashboardsystemalarms.png)

Használhatja a **rendszer riasztások** úgy, hogy a legutóbbi riasztások időtartamának szűrő. Alapértelmezés szerint a panel megjeleníti az elmúlt órában a riasztás:

![A riasztások szűrése idő](media/iot-suite-remote-monitoring-monitor/dashboardalarmsfilter.png)

> [!NOTE]
> Görgessen jobbra span időszűrője megjelenítéséhez.

## <a name="view-the-system-kpis"></a>A rendszer a KPI-k megtekintése

A **irányítópult** lap KPI-k system jelenik meg:

![A riasztások szűrése idő](media/iot-suite-remote-monitoring-monitor/dashboardkpis.png)

Használhatja a **rendszer KPI** úgy, hogy a KPI-összesítési időtartamának szűrő. A panel alapértelmezés szerint a KPI-k az elmúlt egy óra alatt összesített értéket jelenít meg.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan használható a **irányítópult** lap szűrésére és a távoli felügyeleti megoldás kiépítve a szimulált teherautók figyelése:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Az irányítópult eszközök szűréséhez
> * Nézet valós idejű telemetriai adat
> * Eszköz részleteinek megtekintése
> * Nézet riasztások adott területen működő eszközök
> * A rendszer a KPI-k megtekintése

Most, hogy az eszközök figyelése megtanulhatta,-e a javasolt lépések megtudhatja, hogyan:

* [Küszöbérték-alapú szabályok használatával kapcsolatos problémák észlelése](./iot-suite-remote-monitoring-automate.md).
* [Kezelése és az eszközök](./iot-suite-remote-monitoring-manage.md).
* [Hibaelhárítás és szervizelheti azokat a eszközökkel kapcsolatos problémákat](./iot-suite-remote-monitoring-maintain.md).
* [A megoldás tesztelése szimulált eszközökkel](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->