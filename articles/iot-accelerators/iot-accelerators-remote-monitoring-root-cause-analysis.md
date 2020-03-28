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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
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

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>A megjelenítendő eszközök kiválasztása

Az **Irányítópult** oldalon megjelenítendő csatlakoztatott eszközök kiválasztásához használjon szűrőket. Ha kizárólag a **Teherautó** típusú eszközöket szeretné megjeleníteni, válassza a beépített **Teherautók** szűrőt a legördülő szűrőlistában:

[![Teherautók szűrése a műszerfalon](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-expanded.png#lightbox)

Szűrő alkalmazásakor csak a szűrőfeltételeknek megfelelő eszközök jelennek meg a térképen és az irányítópult telemetriai **paneljén.** Láthatja, hogy a megoldásgyorsítóhoz két teherautó csatlakozik, beleértve a **truck-02** teherautót is.

## <a name="view-real-time-telemetry"></a>Valós idejű telemetriai adatok megtekintése

A megoldásgyorsító valós idejű telemetriai adatokat ábrázol az **Irányítópult** lapon található diagramon. A diagram alapértelmezés szerint az időben változó magassági telemetriai adatokat mutatja:

[![Teherautó magasságának telemetriai rajza](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-expanded.png#lightbox)

A teherautók hőmérsékleti telemetriájának megtekintéséhez kattintson **Telemetria panel****Hőmérséklet** elemére. Láthatja, hogyan változott a két teherautó hőmérséklete az elmúlt 15 perc során. A riasztási panelen az is látható, hogy a delivery-truck-02 esetén alacsony hőmérsékleti riasztás lett aktiválva.

[![RM-irányítópult alacsony hőmérsékletű riasztással](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-expanded.png#lightbox)

## <a name="explore-the-data"></a>Az adatok vizsgálata

Az alacsony hőmérsékleti riasztás okának megállapításához nyissa meg a teherautó telemetriai adatait a Time Series Insights Explorerben. Kattintson az irányítópulton lévő **Vizsgálat a Time Series Insightsban** hivatkozások bármelyikére:

[![RM-irányítópult kiemelt TSI-hivatkozásokkal](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-expanded.png#lightbox)

Amikor az Explorer megnyílik, megjelenik az eszközök teljes listája:

[![Az MSI Explorer kezdeti nézete](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-expanded.png#lightbox)

Szűrje az eszközöket úgy, hogy beírja a **szállítóteherautót** a szűrőmezőbe, és a bal oldali panelen a **hőmérsékletet** **választja mértékként:**

[![Az 1SI Explorer teherautó hőmérséklete](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-expanded.png#lightbox)

Ugyanazt a nézetet látja, mint a Távoli figyelés irányítópulton. Emellett most már közelebbi rekedhet ahhoz az időkerethez, amelyen belül a riasztás aktiválódott:

[![TSI Explorer nagyítás](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-expanded.png#lightbox)

Hozzáadhat a teherautóktól származó további telemetria-streameket is. Kattintson a Bal felső sarokban a **Hozzáadás** gombra. Ekkor új panel nyílik meg:

[![TSI Explorer új ablaktáblával](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-expanded.png#lightbox)

Az új panelen módosítsa az új címke nevét az **Eszközök** névre, hogy megegyezzen az előzővel. Válassza ki a **magasságot** **mértékként** és **iothub-connection-device-id-ként** a **Felosztás értékként,** hogy a magassági telemetriai adatokat hozzáadja a nézethez:

[![TSI Explorer hőmérséklettel és magassággal](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-expanded.png#lightbox)

## <a name="diagnose-the-alert"></a>A riasztás diagnosztizálása

Ha az aktuális nézetben megtekinti az adatfolyamokat, láthatja, hogy a két teherautó magassági profilja eltérő. Az is látható, hogy a **delivery-truck-02** esetében a hőmérséklet-csökkenés akkor következik be, amikor a teherautó nagy magasságot ér el. Az eredmény meglepő, hiszen a két teherautó számára ugyanaz az útvonal volt kijelölve.

Sejtését, hogy a teherautók más útvonalon haladtak, úgy igazolhatja, hogy újabb panelt vesz fel az oldalpanelre a **Hozzáadás** gomb használatával. Az új panelen módosítsa az új címke nevét az **Eszközök** névre, hogy megegyezzen az előzővel. A földrajzi hosszúsági telemetriát úgy veheti fel ebbe a nézetbe, hogy **Mértékként** a **földrajzi hosszúságot**, a **Felosztás** szempontjaként pedig az **iothub-connection-device-id** értéket választja. A **földrajzi hosszúsági** stream alapján megállapítható, hogy a teherautók különböző útvonalon haladtak:

[![TSI Explorer hőmérséklet, magasság és hosszúság](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-expanded.png#lightbox)

## <a name="create-a-new-rule"></a>Új szabály létrehozása

Bár a teherautó-útvonalakat általában előre optimalizálják, rájössz, hogy a forgalmi minták, az időjárás és más előre nem látható események késéseket okozhatnak, és az utolsó pillanatban a teherautó-vezetőkre bízzák a döntéseket a legjobb megítélésük alapján. Mivel azonban a járműben lévő eszközök hőmérséklete kritikus, hozzon létre egy további szabályt a távoli figyelési megoldásban. Ez a szabály biztosítja, hogy figyelmeztetést kapjon, ha az 1 perces intervallumátlagos magassága meghaladja a 350 lábat:

[![Távoli figyelési szabályok lap magassági szabálya](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-expanded.png#lightbox)

A szabályok létrehozását és módosítását az [Eszközökkel kapcsolatos problémák észlelése](iot-accelerators-remote-monitoring-automate.md) című előző oktatóanyagban sajátíthatja el.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag azt mutatta be, hogyan használható a Time Series Insights Explorer a távoli monitorozási megoldásgyorsítóval riasztások kiváltó okainak elemzésére. A következő oktatóanyagból azt tudhatja meg, hogyan használhatja a megoldásgyorsítót a csatlakoztatott eszközök problémáinak azonosítására és javítására.

> [!div class="nextstepaction"]
> [Eszközriasztások használata a monitorozási megoldáshoz csatlakoztatott eszközök problémáinak azonosítására és javítására](iot-accelerators-remote-monitoring-maintain.md)
