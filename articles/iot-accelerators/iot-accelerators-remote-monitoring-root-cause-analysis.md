---
title: Riasztás kiváltó okainak elemzése – Azure | Microsoft Docs
description: Ebben az oktatóanyagban a riasztások kiváltó okainak az Azure Time Series Insights használatával végzett elemzését sajátíthatja el.
author: Philmea
ms.author: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: c3cb940583677d813873c07fbfa679fdcc1dff59
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77565468"
---
# <a name="tutorial-conduct-a-root-cause-analysis-on-an-alert"></a>Oktatóanyag: Riasztás kiváltó okainak elemzése

Ez az oktatóanyag a távoli monitorozási megoldásgyorsító riasztások kiváló okainak elemzésére való használatát ismerteti. A távoli monitorozási megoldás irányítópultján láthatja, ha egy riasztás aktiválva lett, majd az Azure Time Series Insights Explorerrel kivizsgálhatja annak kiváltó okát.

Az oktatóanyag két szimulált, teherautóban működő eszközt használ, amelyek helyre, magasságra, sebességre és rakomány-hőmérsékletre vonatkozó telemetriát küldenek. A teherautókat egy Contoso nevű cég kezeli, és kapcsolódnak a távoli monitorozási megoldásgyorsítóhoz. A Contoso üzemeltetési munkatársaként tisztáznia kell, hogy miért rögzített a teherautók egyike (delivery-truck-02) alacsony hőmérsékletet jelző riasztást.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

>[!div class="checklist"]
> * Az irányítópulton található eszközök szűrése
> * Valós idejű telemetriai adatok megtekintése
> * Adatok vizsgálata a Time Series Insights Explorerben
> * A kiváltó okok elemzése
> * Új szabály létrehozása az eredmények alapján

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>A megjelenítendő eszközök kiválasztása

Az **Irányítópult** oldalon megjelenítendő csatlakoztatott eszközök kiválasztásához használjon szűrőket. Ha kizárólag a **Teherautó** típusú eszközöket szeretné megjeleníteni, válassza a beépített **Teherautók** szűrőt a legördülő szűrőlistában:

[![Teherautók szűrése az irányítópulton](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-expanded.png#lightbox)

Szűrő alkalmazása esetén csak a szűrési feltételeknek megfelelő eszközök jelennek meg a térképen és az **irányítópult**telemetria paneljén. Láthatja, hogy a megoldásgyorsítóhoz két teherautó csatlakozik, beleértve a **truck-02** teherautót is.

## <a name="view-real-time-telemetry"></a>Valós idejű telemetriai adatok megtekintése

A megoldásgyorsító valós idejű telemetriai adatokat ábrázol az **Irányítópult** lapon található diagramon. A diagram alapértelmezés szerint az időben változó magassági telemetriai adatokat mutatja:

[![A tehergépkocsi magasságának telemetria ábrázolása](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-expanded.png#lightbox)

A teherautók hőmérsékleti telemetriájának megtekintéséhez kattintson **Telemetria panel****Hőmérséklet** elemére. Láthatja, hogyan változott a két teherautó hőmérséklete az elmúlt 15 perc során. A riasztási panelen az is látható, hogy a delivery-truck-02 esetén alacsony hőmérsékleti riasztás lett aktiválva.

[![RM-irányítópult alacsony Temp riasztással](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-expanded.png#lightbox)

## <a name="explore-the-data"></a>Az adatok vizsgálata

Az alacsony hőmérsékleti riasztás okának megállapításához nyissa meg a teherautó telemetriai adatait a Time Series Insights Explorerben. Kattintson az irányítópulton lévő **Vizsgálat a Time Series Insightsban** hivatkozások bármelyikére:

[![RM-irányítópult kiemelve az ÁME-hivatkozásokkal](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-expanded.png#lightbox)

Amikor az Explorer megnyílik, megjelenik az eszközök teljes listája:

[![ÁME Explorer kezdeti nézete](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-expanded.png#lightbox)

Az eszközök szűréséhez írja be a **Delivery-Truck** kifejezést a szűrő mezőbe, és **válassza a** **hőmérséklet** lehetőséget a bal oldali panelen:

[![ÁME Explorer – tehergépkocsi hőmérséklete](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-expanded.png#lightbox)

Ugyanazt a nézetet látja, amelyet a távoli figyelési irányítópulton látott. Emellett most már közelebb is nagyíthatja azt az időkeretet, amelyen a riasztást aktiválták:

[![Az ÁME Explorer nagyítása](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-expanded.png#lightbox)

Hozzáadhat a teherautóktól származó további telemetria-streameket is. Kattintson a **Hozzáadás** gombra a bal felső sarokban. Ekkor új panel nyílik meg:

[![Az ÁME Explorer új ablaktáblával](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-expanded.png#lightbox)

Az új panelen módosítsa az új címke nevét az **Eszközök** névre, hogy megegyezzen az előzővel. Válassza a **magasság** lehetőséget a **mérték** és a **iothub-Device-ID** érték szerint a **felosztás** értékeként, hogy hozzáadja a magassági telemetria a nézethez:

[![ÁME-tallózó hőmérséklettel és magassággal](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-expanded.png#lightbox)

## <a name="diagnose-the-alert"></a>A riasztás diagnosztizálása

Ha az aktuális nézetben megtekinti a streameket, láthatja, hogy a két teherautó magassági profiljai eltérőek. Az is látható, hogy a **delivery-truck-02** esetében a hőmérséklet-csökkenés akkor következik be, amikor a teherautó nagy magasságot ér el. Az eredmény meglepő, hiszen a két teherautó számára ugyanaz az útvonal volt kijelölve.

Sejtését, hogy a teherautók más útvonalon haladtak, úgy igazolhatja, hogy újabb panelt vesz fel az oldalpanelre a **Hozzáadás** gomb használatával. Az új panelen módosítsa az új címke nevét az **Eszközök** névre, hogy megegyezzen az előzővel. A földrajzi hosszúsági telemetriát úgy veheti fel ebbe a nézetbe, hogy **Mértékként** a **földrajzi hosszúságot**, a **Felosztás** szempontjaként pedig az **iothub-connection-device-id** értéket választja. A **földrajzi hosszúsági** stream alapján megállapítható, hogy a teherautók különböző útvonalon haladtak:

[![ÁME-tallózó hőmérséklettel, magassággal és hosszúsággal](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-expanded.png#lightbox)

## <a name="create-a-new-rule"></a>Új szabály létrehozása

Míg a tehergépkocsi-útvonalakat általában előre optimalizálták, a forgalmi minták, az időjárás és az egyéb előre nem látható események késéseket okozhatnak, és a legjobb döntésük alapján elhagyják az utolsó perc útvonalra vonatkozó döntéseket a tehergépkocsi-járművezetők számára. Mivel azonban az eszközeinek a járművön belüli hőmérséklete kritikus, egy további szabályt kell létrehoznia a távoli figyelési megoldásban. Ez a szabály annak biztosítására szolgál, hogy figyelmeztetést kapjon, ha egy 1 perces intervallum átlagos magassága 350 méternél magasabb:

[![Távoli figyelési szabályok lapon beállított magassági szabály](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-expanded.png#lightbox)

A szabályok létrehozását és módosítását az [Eszközökkel kapcsolatos problémák észlelése](iot-accelerators-remote-monitoring-automate.md) című előző oktatóanyagban sajátíthatja el.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag azt mutatta be, hogyan használható a Time Series Insights Explorer a távoli monitorozási megoldásgyorsítóval riasztások kiváltó okainak elemzésére. A következő oktatóanyagból azt tudhatja meg, hogyan használhatja a megoldásgyorsítót a csatlakoztatott eszközök problémáinak azonosítására és javítására.

> [!div class="nextstepaction"]
> [Eszközriasztások használata a monitorozási megoldáshoz csatlakoztatott eszközök problémáinak azonosítására és javítására](iot-accelerators-remote-monitoring-maintain.md)
