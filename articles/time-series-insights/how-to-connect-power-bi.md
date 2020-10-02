---
title: A környezet összekötése a Power BI-Azure Time Series Insightsrel | Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat a Azure Time Series Insights a Power BIhoz a szervezeten belüli adatmegosztáshoz, diagramhoz és megjelenítéshez.
author: deepakpalled
ms.author: dpalled
manager: diviso
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: 105faf796583eb0f272eea7a316648993fdafd0d
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653774"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>Adatok megjelenítése Azure Time Series Insightsról Power BI

A Azure Time Series Insights egy platform az idősoros adatsorozatok felhőben történő tárolásához, kezeléséhez, lekérdezéséhez és megjelenítéséhez. A [Power bi](https://powerbi.microsoft.com) egy üzleti elemzési eszköz, amely sokoldalú vizualizációs képességekkel rendelkezik, amelyekkel megoszthatja az elemzéseket és az eredményeket a szervezeten belül. Mindkét szolgáltatás integrálható a Azure Time Series Insights rejlő vizualizációs képességek és a Power BI.

A következőket fogja megtanulni:

* Azure Time Series Insights összekötése a Power BI a Cloud Connector használatával
* Vizualizációk létrehozása az adataival Power BI
* A jelentés közzététele Power BI és megosztás a szervezet többi részével

Ebből a lépésből megtudhatja, hogyan jelenítheti meg az idősoros adatsorozatokat a Azure Time Series Insightson keresztül, és hogyan növelhető az adatvizualizációk és a Power BI egyszerű megosztási képességeivel.

Ha még nem rendelkezik ilyennel, hozzon létre egy [ingyenes Azure-előfizetést](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

* Töltse le és telepítse a [Power bi Desktop](https://powerbi.microsoft.com/downloads/) legújabb verzióját
* [Azure Time Series Insights Gen2-környezet](time-series-insights-update-how-to-manage.md) létrehozása vagy létrehozása

> [!IMPORTANT]
>
> * Az összekötő jelenleg **csak a meleg tárolással**konfigurált Azure Time Series Insights Gen2 környezetekben támogatott.
> * Ha egy másik Azure AD-Bérlővel rendelkezik vendég hozzáféréssel a Azure Time Series Insights Gen2-környezethez, nem fog tudni hozzáférni az összekötőhöz. További információ a [környezeti hozzáférési házirendekről](./concepts-access-policies.md).

## <a name="connect-data-from-azure-time-series-insights-to-power-bi"></a>Adatok összekötése Azure Time Series Insightsról Power BI

A Azure Time Series Insights-környezet Power BIhoz való összekapcsolásához kövesse az alábbi lépéseket:

1. Azure Time Series Insights Explorer megnyitása
1. Az adatexportálás lekérdezésként vagy nyers adatként
1. A Power BI Desktop megnyitása
1. Betöltés egyéni lekérdezésből

### <a name="export-data-into-power-bi-desktop"></a>Az adatexportálás Power BI asztalra

Első lépések:

1. Nyissa meg az Azure Time Series Insights Explorert, és adja meg az adatait.
1. Miután létrehozta a megfelelő nézetet, navigáljon a **További műveletek** legördülő menüre, és válassza a **Kapcsolódás Power bi**lehetőséget.

    [![Azure Time Series Insights Explorer exportálás](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Állítsa be a paramétereket a lapon belül:

   1. A megtekinteni kívánt relatív időkeretet határozza meg. Ha elégedett a meglévő nézettel, hagyja meg ezt a **meglévő időkeretként**.

   1. Válasszon az **összesített** és a **nyers események**közül.

       > [!NOTE]
       > Az adatokat később is összesítheti Power BIban, de az Összesítés után nem lehet visszaállítani a nyers adatokat.

       > [!NOTE]
       > A nyers események szintjének maximális száma 250 000.

       [![Kapcsolódás](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Ha nem konfigurálta a Azure Time Series Insights-környezetet a **meleg tárolással**, a rendszer figyelmeztetést küld.

       [![Meleg tárolás – figyelmeztetés](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > A meglévő példányt a Azure Portal **meleg tárolására** is konfigurálhatja.

1. Válassza **a lekérdezés másolása a vágólapra**lehetőséget.
1. Most indítsa el Power BI Desktop.
1. A **kezdőlapon** Power bi Desktop kattintson a bal felső sarokban található **adatlekérdezés** elemre, majd a **tovább**gombra.

    [![Kezdőlap legördülő lista](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Keressen **Azure Time Series Insights**, válassza a **Azure Time Series Insights (bétaverzió)**, majd a **kapcsolat**lehetőséget.

    [![Power BI összekötése a Azure Time Series Insights](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    Másik lehetőségként navigáljon az **Azure** lapra, válassza a **Azure Time Series Insights (bétaverzió)**, majd a **Kapcsolódás**lehetőséget.

1. Az üzenet párbeszéde ablak megjeleníti a harmadik féltől származó erőforrásokhoz való kapcsolódásra vonatkozó engedélyt. Válassza a **Folytatás**lehetőséget.

    [![Válassza az egyéni lekérdezés létrehozása lehetőséget.](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. Az **adatforrás**legördülő menüjében válassza az **egyéni lekérdezés létrehozása**lehetőséget. Illessze be a vágólapról az alábbi opcionális **egyéni lekérdezés (opcionális)** mezőbe, majd kattintson **az OK gombra**.

    [![Adja meg az egyéni lekérdezést, és kattintson az OK gombra.](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. Az adattábla ekkor betöltődik. A betöltéshez nyomja meg a **Load** (betöltés) Power bi

    [![Tekintse át a betöltött adatsorokat a táblázatban, és válassza a betöltés lehetőséget](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Ha végrehajtotta ezeket a lépéseket, ugorjon a következő szakaszra.

## <a name="create-a-report-with-visuals"></a>Vizualizációkat tartalmazó jelentés létrehozása

Most, hogy importálta az adatPower BIba, ideje, hogy készítsen egy jelentést a vizualizációkkal.

1. Győződjön meg arról, hogy az ablak bal oldalán a **jelentés** nézet van kiválasztva.

    [![Képernyőfelvétel: a jelentés nézet ikonja.](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. A vizualizációk oszlopban válassza ki a kívánt **vizualizációt** . Válassza például a **vonal diagram**elemet. Ez egy üres vonalas diagramot ad hozzá a vászonhoz.

1. A **mezők** listában válassza a **_Timestamp** lehetőséget, majd húzza a **tengely** mezőre az X tengely mentén megjelenített elemek megjelenítéséhez. Ügyeljen arra, hogy **_Timestampre** váltson a **tengely** értékeként (az alapértelmezett dátum- **hierarchia**).

    [![Képernyőfelvétel: az időbélyegző menü _Timestamp kiválasztva.](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1. A **mezők** listában kattintson a **TimeSeriesId** elemre, majd húzza a **Values (értékek** ) mezőbe az Y tengely melletti elemek megjelenítéséhez.

    [![Vonaldiagram létrehozása](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. Ha egy másik diagramot szeretne hozzáadni a vászonhoz, válassza a vászonon bárhol a vásznon, és ismételje meg a folyamatot.

    [![További megosztható diagramok létrehozása](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Miután létrehozta a jelentést, közzéteheti a Power BI Reporting Services szolgáltatásban.

## <a name="advanced-editing"></a>Speciális szerkesztés

Ha már betöltött egy adatkészletet a Power BIban, de módosítani szeretné a lekérdezést (például a dátum/idő vagy a környezeti azonosító paramétereit), ezt a Power BI Speciális szerkesztő funkcióján keresztül teheti meg. További információért tekintse meg a [Power bi dokumentációját](https://docs.microsoft.com/power-bi/desktop-query-overview) .

Áttekintés:

1. A Power BI Desktop területen válassza a **lekérdezések szerkesztése**lehetőséget.
1. Nyomja meg az **speciális szerkesztő**gombot.

    [![Lekérdezések szerkesztése a Speciális szerkesztőban](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Módosítsa a JSON-adattartalmat a kívánt módon.
1. Válassza a **kész** lehetőséget, majd a **Power Query szerkesztő ablakában**kattintson a **& Bezárás** gombra.

A kezelőfelület ekkor az alkalmazott kívánt módosításokat tükrözi.  

## <a name="next-steps"></a>Következő lépések

* További információ a Azure Time Series Insights [Power bi-összekötővel kapcsolatos fogalmakról](https://docs.microsoft.com/power-bi/desktop-query-overview) .

* További információ a [Power bi asztalról](https://docs.microsoft.com/power-bi/desktop-query-overview).
