---
title: A környezet csatlakoztatása a Power BI-hoz – Azure Time Series Insights | Microsoft dokumentumok
description: Megtudhatja, hogy miként kapcsolhatja össze az Azure Time Series Insightsot a Power BI-val az adatok megosztása, diagramja és megjelenítése érdekében a szervezeten belül.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 22053bdc3a9836b76aa92303234a095cac6448ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863842"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>A Time Series Insights adatainak megjelenítése a Power BI-ban

Az Azure Time Series Insights egy olyan platform, amely az idősorozat-adatok tárolását, kezelését, lekérdezését és megjelenítését szolgála a felhőben. [A Power BI](https://powerbi.microsoft.com) egy gazdag vizualizációs képességekkel rendelkező üzleti elemző eszköz, amely lehetővé teszi az elemzések és az eredmények megosztását a szervezeten belül. Mostantól mindkét szolgáltatás integrálható, hogy a lehető legjobbat hozhassa ki a Time Series Insights belső vizualizációs képességeiből, valamint a Power BI-ból is.

A következőket fogja megtanulni:

* A Time Series Insights csatlakoztatása a Power BI-hoz a felhőösszekötő használatával
* Vizualizációk létrehozása az adatokkal a Power BI-ban
* A jelentés közzététele a Power BI-ban és megosztás a szervezet többi tagjával

A végén megtudhatja, hogyan jelenítheti meg az idősorozatadatait az Azure Time Series Insights segítségével, és hogyan javíthatja azokat a Power BI erős adatvizualizációjával és egyszerű megosztási lehetőségeivel.

Ha még nem rendelkezik [ingyenes Azure-előfizetéssel,](https://azure.microsoft.com/free/) regisztráljon.

## <a name="prerequisites"></a>Előfeltételek

* A [Power BI Desktop](https://powerbi.microsoft.com/downloads/) legújabb verziójának letöltése és telepítése
* Azure Time [Series Insights előzetes példány](time-series-insights-update-how-to-manage.md) ának létrehozása vagy létrehozása

> [!IMPORTANT]
> A Power *BI-összekötőjelenleg* a Warm **Store-hoz**konfigurált Time Series Insights előzetes verziós használatra szóló környezetekben támogatott.

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Adatok csatlakoztatása a Time Series Insights és a Power BI adataihoz

Ha a Time Series Insights-környezetet a Power BI-hoz szeretné csatlakoztatni, kövesse az alábbi lépéseket:

1. A Time Series Insights Explorer megnyitása
1. Adatok exportálása lekérdezésként vagy nyers adatként
1. A Power BI Desktop megnyitása
1. Betöltés egyéni lekérdezésből

### <a name="export-data-into-power-bi-desktop"></a>Adatok exportálása a Power BI asztali verzióba

Első lépések:

1. Nyissa meg a Time Series Insights előzetes kezelőt, és vesszen az adatokat.
1. Miután létrehozott egy elégedett nézetet, keresse meg a **További műveletek** legördülő menüt, és válassza a Csatlakozás a **Power BI-hoz**lehetőséget.

    [![Time Series Insights Előzetes intéző exportálása](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Állítsa be a paramétereket ezen a lapon:

   1. Adja meg a megtekinteni kívánt relatív időkeretet. Ha elégedett a meglévő nézet, hagyja ezt **a meglévő időkeret**.
   
   1. Válasszon **az összesített** és a nyers események **közül.** 
   
       > [!NOTE]
       > Az adatokat később bármikor összesítheti a Power BI-ban, de az összesítés után nem állíthatja vissza a nyers adatokat. 
       
       > [!NOTE]
       > A nyers eseményszint-adatokhoz 100 K eseményszámlálási korlát van.

       [![Csatlakoztassa](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Ha még nem konfigurálta a Time Series Insights-példányt a **Meleg tárhoz,** figyelmeztetést fog kapni.

       [![Meleg bolt figyelmeztetés](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > Konfigurálhatja a meglévő példányát a **Warm Store-ra** az Azure Portalon.

1. Válassza **a Lekérdezés másolása a vágólapra**lehetőséget.
1. Most indítsa el a Power BI Desktopot.
1. A **Kezdőlap** lap Power BI Desktop lapján válassza az **Adatok bekéselése** lehetőséget a bal felső sarokban, majd az **Egyebek**lehetőséget.

    [![Kezdőlap legördülő menü](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Keressen a **Time Series Insights (Time Series Insights)** elemre, válassza az **Azure Time Series Insights (Béta)** lehetőséget, majd **a Csatlakozás**lehetőséget.

    [![A Power BI csatlakoztatása a Time Series Insightshoz](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    Másik lehetőségként keresse meg az **Azure** lapot, válassza az **Azure Time Series Insights (Béta)** lehetőséget, majd **a Csatlakozás lehetőséget.**
    
1. Egy üzenet párbeszédablak jelenik meg, amely engedélyt kér a külső erőforrásokhoz való csatlakozáshoz. Válassza a **Folytatás**lehetőséget.

    [![Egyéni lekérdezés létrehozása lehetőséget választva](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. Az **Adatforrás**legördülő menüjében válassza az **Egyéni lekérdezés létrehozása lehetőséget.** Illessze be a vágólapról az alábbi választható **Egyéni lekérdezés (nem kötelező)** mezőbe, majd nyomja **le az OK gombot.**

    [![Adja át az egyéni lekérdezést, és válassza az OK gombot](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. Az adattábla betöltődik. Nyomja le a **Load** billentyűt a Power BI betöltéséhez.

    [![Tekintse át a táblázatban betöltött adatokat, és válassza a Betöltés lehetőséget](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Ha elvégezte ezeket a lépéseket, ugorjon előre a következő szakaszra.

## <a name="create-a-report-with-visuals"></a>Vizualizációkat tartalmazó jelentés létrehozása

Most, hogy importálta az adatokat a Power BI-ba, itt az ideje, hogy vizualizációkkal készítsön jelentést.

1. Győződjön meg arról, hogy az ablak bal oldalán kijelölte a **Jelentés** nézetet.

    [![A Jelentésnézet kiválasztása](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  A **Vizualizációk** oszlopban válassza ki a kívánt vizualizációt. Válassza például **a Vonal diagram lehetőséget.** Ezzel üres vonaldiagramot ad a vászonhoz.
 
1.  A **Mezők** listában jelölje ki az **Időbélyeg lehetőséget,** és húzza a **Tengely** mezőre az X tengely mentén lévő elemek megjelenítéséhez.

1.  A **Mezők** listában jelölje ki a **TimeSeriesId** elemet, és húzza az **Értékek** mezőre az Y tengely mentén lévő elemek megjelenítéséhez.

    [![Vonaldiagram létrehozása](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  Ha egy másik diagramot szeretne hozzáadni a vászonhoz, jelölje ki a vászon bármely pontját a vonaldiagramon kívül, és ismételje meg ezt a folyamatot.

    [![További megosztott diagramok létrehozása](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Miután létrehozta a jelentést, közzéteheti azt a Power BI Reporting Services szolgáltatásban.

## <a name="advanced-editing"></a>Speciális szerkesztés

Ha már betöltött egy adatkészletet a Power BI-ban, de módosítani szeretné a lekérdezést (például a dátum/idő vagy a környezeti azonosító paramétereit), ezt a Power BI Advanced Editor funkcióin keresztül teheti meg. További információt a [Power BI dokumentációjában](https://docs.microsoft.com/power-bi/desktop-query-overview) talál.

Áttekintésként:

1. A Power BI Desktopban válassza **a Lekérdezések szerkesztése**lehetőséget.
1. Nyomja **meg a Speciális szerkesztőt**.

    [![Lekérdezések szerkesztése a Speciális szerkesztőben](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Módosítsa a JSON-hasznos terhet a kívánt módon.
1. Válassza a **Kész,** majd **& Apply bezárása lehetőséget** a Power Query Editor **ablakban.**

A felület most tükrözi a kívánt módosításokat alkalmazott.  

## <a name="next-steps"></a>Következő lépések

* Olvassa el az Azure Time Series Insights [Power BI-összekötővel kapcsolatos fogalmait.](https://docs.microsoft.com/power-bi/desktop-query-overview)

* További információ a [Power BI desktopról.](https://docs.microsoft.com/power-bi/desktop-query-overview)

* Olvassa el [a Time Series Insights GA Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) és a Time Series Insights előzetes [kezelőt.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)
