---
title: 'Oktatóanyag: A Power bi-ban az Azure Data Explorer adatainak megjelenítése'
description: Ebben az oktatóanyagban megtanulhatja, hogyan csatlakoztathatja az Azure Data Explorert a Power BI-hoz, és hogyan jelenítheti meg az adatait.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: 70a06b75db9ff49222c2de4aa78519b32d863478
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049880"
---
# <a name="tutorial-visualize-data-from-azure-data-explorer-in-power-bi"></a>Oktatóanyag: A Power bi-ban az Azure Data Explorer adatainak megjelenítése

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. A Power BI egy üzleti elemzési megoldás, amellyel megjelenítheti adatait, és a teljes szervezettel megoszthatja az eredményeket. Ebben az oktatóanyagban először elsajátíthatja a vizualizációk az Azure Data Explorerben történő renderelését. Ezután csatlakoztathatja az Azure Data Explorert a Power BI-hoz, létrehozhat egy jelentést mintaadatok alapján, majd közzéteheti a jelentést a Power BI szolgáltatásban.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt. Ha nem regisztrált a Power BI Próra, a kezdés előtt [regisztráljon az ingyenes próbaverzióra](https://app.powerbi.com/signupredirect?pbi_source=web).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Vizualizációk renderelése az Azure Data Explorerben
> * Csatlakozás az Azure Data Explorerhez a Power BI Desktopból
> * Adatokkal való munka a Power BI Desktopban
> * Vizualizációkat tartalmazó jelentés létrehozása
> * Jelentés közzététele és megosztása

## <a name="prerequisites"></a>Előfeltételek

Az Azure- és Power BI-előfizetések mellett a következőkre lesz szüksége az oktatóanyag elvégzéséhez:

* [Egy teszt fürt és adatbázis](create-cluster-database-portal.md)

* [A StormEvents mintaadatok](ingest-sample-data.md). [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (válasszon **ingyenes letöltés**)

## <a name="render-visuals-in-azure-data-explorer"></a>Vizualizációk renderelése az Azure Data Explorerben

Mielőtt belekezdenénk a Power BI használatába, nézzük meg, hogyan renderelhetők vizualizációk az Azure Data Explorerben. Ez nagyszerű gyors elemzésekhez.

1. Jelentkezzen be itt: [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. A bal oldali panelen válassza ki a StormEvents mintaadatokat tartalmazó tesztadatbázist.

1. Illessze be a jobb oldali ablakba a következő lekérdezést, majd kattintson a **Futtatás** lehetőségre.

    ```Kusto
    StormEvents
    | summarize event_count=count() by State
    | where event_count > 1800
    | project State, event_count
    | sort by event_count
    | render columnchart
    ```

    Ez a lekérdezés az időjárási eseményeket számolja államok szerint. Ezután egy oszlopdiagramot renderel minden olyan államhoz, ahol több mint 1800 időjárási esemény található.

    ![Események oszlopdiagramja](media/visualize-power-bi/events-column-chart.png)

1. Illessze be a jobb oldali ablakba a következő lekérdezést, majd kattintson a **Futtatás** lehetőségre.

    ```Kusto
    StormEvents
    | where State == "WASHINGTON" and StartTime >= datetime(2007-07-01) and StartTime <= datetime(2007-07-31)
    | summarize StormCount = count() by EventType
    | render piechart
    ```

    Ez a lekérdezés típus szerint számolja a júliusi Washington állambeli időjárási eseményeket. Ezután egy kördiagramot renderel, amely az egyes eseménytípusok százalékos arányait jeleníti meg.

    ![Események kördiagramja](media/visualize-power-bi/events-pie-chart.png)

Ideje a Power BI-ra váltanunk, azonban érdemes észben tartania, hogy számos más műveletet is elvégezhet az Azure Data Explorer vizualizációival.

## <a name="connect-to-azure-data-explorer"></a>Csatlakozás az Azure Data Explorerhez

Most már csatlakozhat az Azure Data Explorerhez a Power BI Desktopból.

1. A Power BI desktopban a **kezdőlap** lapon jelölje be **adatok lekérése** majd **további**.

    ![Adatok lekérése](media/visualize-power-bi/get-data-more.png)

1. Keresse meg *Azure adatkezelő*válassza **Azure Data Explorer (bétaverzió)**, majd **Connect**.

    ![Adatok keresése és lekérése](media/visualize-power-bi/search-get-data.png)

1. Az **Összekötő előnézete** képernyőn válassza a **Folytatás** lehetőséget.

1. A következő képernyőn adja meg a teszt-fürt és az adatbázis nevét. A fürtnek a következő formátumban kell lennie: `https://<ClusterName>.<Region>.kusto.windows.net`. A tábla nevének írja be a *StormEvents* kifejezést. A többi beállítást hagyja az alapértelmezett értékükön, majd válassza az **OK** lehetőséget.

    ![Fürt-, adatbázis- és táblázatbeállítások](media/visualize-power-bi/cluster-database-table.png)

1. Az adatok előnézeti képernyőjén válassza a **Szerkesztés** elemet.

    A tábla megnyílik a Power Query-szerkesztőben, ahol az adatok importálása előtt Ön szerkesztheti a sorokat és oszlopokat.

## <a name="work-with-data-in-power-bi-desktop"></a>Adatokkal való munka a Power BI Desktopban

Most, hogy csatlakozott az Azure Data Explorerhez, szerkesztheti az adatokat a Power Query-szerkesztőben. A null értékű sorokat elveti a **BeginLat** oszlop, a **StormSummary** JSON-oszlopot pedig teljes egészében elveti. Ezek egyszerű műveletek, az adatimportáláskor azonban összetett átalakításokat is végezhet.

1. Kattintson a **BeginLat** oszlop melletti nyílra, törölje a **null** jelölőnégyzetet, majd válassza az **OK** lehetőséget.

    ![Oszlop szűrése](media/visualize-power-bi/filter-column.png)

1. Kattintson a jobb gombbal a **StormSummary** oszlop fejlécére, majd válassza az **Eltávolítás** lehetőséget.

    ![Oszlop eltávolítása](media/visualize-power-bi/remove-column.png)

1. A **LEKÉRDEZÉS BEÁLLÍTSAI** panelen módosítsa a nevet *Query1-ről* *StormEventsre*.

    ![Lekérdezésnév módosítása](media/visualize-power-bi/query-name.png)

1. A menüszalag **kezdőlapján** szalagfül válassza a **Bezárás és alkalmazás** lehetőséget.

    ![Bezárás és alkalmazás](media/visualize-power-bi/close-apply.png)

    A Power Query alkalmazza a módosításokat, majd importálja a mintaadatokat egy *adatmodellbe*. A következő lépésekből megtudhatja, hogyan teheti részletgazdagabbá a modellt. Ez egy egyszerű példa, amely alapján láthatja, hogy milyen típusú műveleteket hajthat végre.

1. A fő ablak bal oldalán válassza ki az adatnézetet.

    ![Adatnézet](media/visualize-power-bi/data-view.png)

1. A menüszalag **Modellezés** lapján válassza az **Új oszlop** lehetőséget.

    ![Új oszlop](media/visualize-power-bi/new-column.png)

1. Adja meg a következő Data Analysis Expressions- (DAX-) képletet a képletsávban, majd nyomja le az Enter billentyűt.

    ```DAX
    DurationHours = DATEDIFF(StormEvents[StartTime], StormEvents[EndTime], hour)
    ```

    ![Képletsáv](media/visualize-power-bi/formula-bar.png)

    Ez a képlet létrehozza a *DurationHours* oszlopot, amely kiszámítja, hogy hány órán át tartottak az egyes események. Ezt az oszlopot a következő szakaszban, egy vizualizációban használja majd.

1. Az oszlop megtekintéséhez görgessen a tábla jobb oldalára.

## <a name="create-a-report-with-visuals"></a>Vizualizációkat tartalmazó jelentés létrehozása

Most, hogy importálta az adatokat és továbbfejlesztette az adatmodellt, ideje elkészíteni egy vizualizációkat tartalmazó jelentést. Egy esemény-időtartamon alapuló oszlopdiagramot és egy terményekben esett kárt megjelenítő térképet fog hozzáadni.

1. Az ablak bal oldalán válassza ki a jelentésnézetet.

    ![Jelentésnézet](media/visualize-power-bi/report-view.png)

1. A **VIZUALIZÁCIÓK** panelen válassza a fürtözött oszlopdiagramot.

    ![Oszlopdiagram hozzáadása](media/visualize-power-bi/add-column-chart.png)

    A vásznon megjelenik egy üres diagram.

    ![Üres diagram](media/visualize-power-bi/blank-chart.png)

1. A **MEZŐK** listában válassza a **DurationHours** és az **Állam** lehetőséget.

    ![Mezők kiválasztása](media/visualize-power-bi/select-fields.png)

    Most már van egy diagramja, amely az időjárási események teljes óraszámát jeleníti meg egy év alatt, államok szerint rendezve.

    ![Időtartamot megjelenítő oszlopdiagram](media/visualize-power-bi/duration-column-chart.png)

1. Kattintson a vászon egy, az oszlopdiagramon kívül eső részére.

1. A **VIZUALIZÁCIÓK** panelen válassza ki a térképet.

    ![Térkép hozzáadása](media/visualize-power-bi/add-map.png)

1. A **MEZŐK** listában válassza a **CropDamage** és az **Állam** lehetőséget. Méretezze át úgy a térképet, hogy jól kivehetően megjelenjen az USA összes állama.

    ![Terményekben esett kár – térkép](media/visualize-power-bi/crop-damage-map.png)

    A buborékok mérete a terményekben esett kár dollárban számított értékének felel meg. A részletek megtekintéséhez vigye az egérmutatót a buborékok fölé.

1. Helyezze át és méretezze úgy a vizualizációkat, hogy a jelentés következő képre hasonlítson.

    ![Kész jelentés](media/visualize-power-bi/finished-report.png)

1. Mentse a jelentést *storm-events.pbix* néven.

## <a name="publish-and-share-the-report"></a>Jelentés közzététele és megosztása

Eddig a pontig csak helyileg, a Power BI Desktopban dolgozott. Most közzéteheti a jelentést a Power BI szolgáltatásban, ahol másokkal is megoszthatja.

1. A Power BI Desktop menüszalagjának **kezdőlapján** válassza a **Közzététel** lehetőséget.

    ![Közzététel gomb](media/visualize-power-bi/publish-button.png)

1. Ha még nem jelentkezett be a Power BI-ba, végezze el a szükséges bejelentkezési lépéseket.

1. Válassza a **Saját munkaterület**, majd a **Kiválasztás** lehetőséget.

    ![Munkaterület kiválasztása](media/visualize-power-bi/select-workspace.png)

1. Ha elkészült a közzététellel, válassza **A storm-events.pbix megnyitása a Power BI-ban** lehetőséget.

    ![A közzététel sikerült](media/visualize-power-bi/publishing-succeeded.png)

    A szolgáltatás megnyitja a jelentést a Power BI Desktopban definiált vizualizációkkal és elrendezéssel.

1. A jelentés jobb felső sarkában válassza a **Megosztás** lehetőséget.

    ![Megosztás gomb](media/visualize-power-bi/share-button.png)

1. A **Jelentés megosztása** képernyőn adjon hozzá egy munkatársat és egy megjegyzést, majd válassza a **Megosztás** lehetőséget.

    ![Jelentés megosztása](media/visualize-power-bi/share-report.png)

    Ha a munkatársa rendelkezik a megfelelő engedélyekkel, hozzáférhet a megosztott jelentéshez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem szeretné megőrizni a létrehozott jelentést, egyszerűen törölje a *storm-events.pbix* fájlt. Ha el kívánja távolítani a közzétett jelentést, kövesse az alábbi lépéseket.

1. A **Saját munkaterületen** görgessen a **JELENTÉSEK** szakaszig, és keresse meg **storm-events** fájlt.

1. Kattintson a **storm-events** melletti három pontra (**. . .**), majd válassza az **ELTÁVOLÍTÁS** lehetőséget.

    ![Jelentés eltávolítása](media/visualize-power-bi/remove-report.png)

1. Erősítse meg az eltávolítást.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Lekérdezések írása](write-queries.md)
