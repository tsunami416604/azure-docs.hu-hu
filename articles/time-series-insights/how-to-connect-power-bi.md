---
title: A környezet összekötése a Power BI-Azure Time Series Insightsrel | Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat a Azure Time Series Insights a Power BIhoz a szervezeten belüli adatmegosztáshoz, diagramhoz és megjelenítéshez.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: e218877ee0d29e493a31091ccbce406ed888f8cc
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114733"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Adatok megjelenítése Time Series Insightsról Power BI

A Azure Time Series Insights egy platform az idősoros adatsorozatok felhőben történő tárolásához, kezeléséhez, lekérdezéséhez és megjelenítéséhez. A [Power bi](https://powerbi.microsoft.com) egy üzleti elemzési eszköz, amely sokoldalú vizualizációs képességekkel rendelkezik, amelyekkel megoszthatja az elemzéseket és az eredményeket a szervezeten belül. Mindkét szolgáltatás integrálható a Time Series Insights rejlő vizualizációs képességek és a Power BI.

A következőket fogja megtanulni:

* Time Series Insights összekötése a Power BI a Cloud Connector használatával
* Vizualizációk létrehozása az adataival Power BI
* A jelentés közzététele Power BI és megosztás a szervezet többi részével

Ebből a lépésből megtudhatja, hogyan jelenítheti meg az idősoros adatsorozatokat a Azure Time Series Insightson keresztül, és hogyan növelhető az adatvizualizációk és a Power BI egyszerű megosztási képességeivel.

Ha még nem rendelkezik ilyennel, hozzon létre egy [ingyenes Azure-előfizetést](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

* Töltse le és telepítse a [Power bi Desktop](https://powerbi.microsoft.com/downloads/) legújabb verzióját
* [Azure Time Series INSIGHTS GA-példány](time-series-insights-get-started.md) vagy [Azure Time Series Insights előzetes verziójú példányának](time-series-insights-update-how-to-manage.md) létrehozása

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Adatok összekötése Time Series Insightsról Power BI

A Time Series Insights-környezet Power BIhoz való összekapcsolásához kövesse az alábbi lépéseket:

1. Time Series Insights Explorer megnyitása                      
1. Az adatexportálás lekérdezésként vagy nyers adatként                       
1. Power BI Desktop megnyitása
1. Betöltés egyéni lekérdezésből

### <a name="export-data-into-power-bi-desktop"></a>Az adatexportálás Power BI asztalra

Első lépések:

1. Nyissa meg a Time Series Insights Preview Explorer alkalmazást, és adja meg az adatait.
1. Miután létrehozta a megfelelő nézetet, navigáljon a **További műveletek** legördülő menüre, és kattintson a **Kapcsolódás Power bi**elemre. 

    [![Time Series Insights Preview Explorer exportálás](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Állítsa be a paramétereket a lapon belül:

   1. A megtekinteni kívánt relatív időkeretet határozza meg. Ha elégedett a meglévő nézettel, hagyja meg ezt a **meglévő időkeretként**. 
   1. Válasszon az **összesített** és a **nyers események**közül. 
   
       > [!NOTE]
       > Az adatokat később is összesítheti Power BIban, de az Összesítés után nem lehet visszaállítani a nyers adatokat. 
       
       > [!NOTE]
       > A nyers események szintjének maximális száma 100 – K.

       [![-kapcsolat](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Ha nem konfigurálta a Time Series Insights-példányát a meleg tároláshoz, a rendszer figyelmeztetést küld.

       [![-kapcsolat](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > A meglévő példányt a Azure Portal meleg tárolására is konfigurálhatja.

1. Válassza **a lekérdezés másolása a vágólapra**lehetőséget.
1. Most indítsa el Power BI Desktop.
1. A **kezdőlapon** Power bi Desktop kattintson a bal felső sarokban található **adatlekérdezés** elemre, majd a **tovább**gombra.

    [![Kezdőlap legördülő lista](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Keressen **Time Series Insights**, válassza a **Azure Time Series Insights (bétaverzió)** , majd a **kapcsolat**lehetőséget.

    [Power BI ![kapcsolódás a Time Series Insights](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    Másik lehetőségként navigáljon az **Azure** lapra, válassza a **Azure Time Series Insights (bétaverzió)** , majd a **Kapcsolódás**lehetőséget.
    
1. Az üzenet párbeszéde ablak megjeleníti a harmadik féltől származó erőforrásokhoz való kapcsolódásra vonatkozó engedélyt. Válassza a **Folytatás**lehetőséget.

    [![válassza az egyéni lekérdezés létrehozása lehetőséget.](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. Az **adatforrás**legördülő menüjében válassza az **egyéni lekérdezés létrehozása**lehetőséget. Illessze be a vágólapról az alábbi opcionális **egyéni lekérdezés (opcionális)** mezőbe, majd kattintson **az OK gombra**.

    [![adja meg az egyéni lekérdezést, és kattintson az OK gombra.](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. Az adattábla ekkor betöltődik. A betöltéshez nyomja meg a **Load** (betöltés) Power bi

    [![tekintse át a táblában betöltött adatfájlokat, és válassza a betöltés lehetőséget.](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Ha végrehajtotta ezeket a lépéseket, ugorjon a következő szakaszra.

## <a name="create-a-report-with-visuals"></a>Vizualizációkat tartalmazó jelentés létrehozása

Most, hogy importálta az adatPower BIba, ideje, hogy készítsen egy jelentést a vizualizációkkal.

1. Győződjön meg arról, hogy az ablak bal oldalán a **jelentés** nézet van kiválasztva.

    [![válassza ki a jelentés nézetet](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  A vizualizációk oszlopban válassza ki a kívánt **vizualizációt** . Válassza például a **vonal diagram**elemet. Ez egy üres vonalas diagramot ad hozzá a vászonhoz.
 
1.  A **mezők** listában válassza az **időbélyegző** lehetőséget, majd húzza a **tengely** mezőre az X tengely mentén lévő elemek megjelenítéséhez.

1.  A **mezők** listában kattintson a **TimeSeriesId** elemre, majd húzza a **Values (értékek** ) mezőbe az Y tengely melletti elemek megjelenítéséhez.

    [![diagram létrehozása](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  Ha egy másik diagramot szeretne felvenni a vászonra, kattintson a vászonon a vásznon kívül bárhová, majd ismételje meg a folyamatot.

    [![további diagramokat hozhat létre a megosztáshoz](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Miután létrehozta a jelentést, közzéteheti a Power BI Reporting Services szolgáltatásban.

## <a name="advanced-editing"></a>Speciális szerkesztés

Ha már betöltött egy adatkészletet a Power BIban, de módosítani szeretné a lekérdezést (például a dátum/idő vagy a környezeti azonosító paramétereit), ezt a Power BI Speciális szerkesztő funkcióján keresztül teheti meg. További információért tekintse meg a [Power bi dokumentációját](https://docs.microsoft.com/power-bi/desktop-query-overview) .

Áttekintés:

1. A Power BI Desktop területen válassza a **lekérdezések szerkesztése**lehetőséget.
1. Nyomja meg az **speciális szerkesztő**gombot.

    [![lekérdezések szerkesztése a Speciális szerkesztő](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Módosítsa a JSON-adattartalmat a kívánt módon.
1. Válassza a **kész** lehetőséget, majd a **Power Query szerkesztő ablakában**kattintson a **& Bezárás** gombra.

Ekkor meg kell jelennie a kívánt változtatásoknak.  

## <a name="next-steps"></a>További lépések

* További információ a Azure Time Series Insights [Power bi-összekötővel kapcsolatos fogalmakról](https://docs.microsoft.com/power-bi/desktop-query-overview) .

* További információ a [Power bi asztalról](https://docs.microsoft.com/power-bi/desktop-query-overview).

* Lásd: [Time Series INSIGHTS GA Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) és [Time Series Insights Preview Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).
