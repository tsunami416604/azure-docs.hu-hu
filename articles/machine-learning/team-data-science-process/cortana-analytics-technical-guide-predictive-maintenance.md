---
title: "Az Azure - Cortana Intelligence megoldás műszaki útmutató űrtechnikai a prediktív karbantartási |} Microsoft Docs"
description: "Egy technikai útmutató a Microsoft Cortana Intelligence a megoldás sablon űrtechnikai, segédprogramok vagy szállítására prediktív karbantartás."
services: cortana-analytics
documentationcenter: 
author: fboylu
manager: jhubbard
editor: cgronlun
ms.assetid: 2c4d2147-0f05-4705-8748-9527c2c1f033
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: fboylu
ms.openlocfilehash: a6d9cd05eb370399fc95cc64bae892e8b5a73fe2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace-and-other-businesses"></a>A Cortana Intelligence megoldás sablont űrtechnikai és más vállalatok számára a prediktív karbantartási műszaki útmutatója

## <a name="important"></a>**Fontos**
Ez a cikk elavult. Az adatokat a probléma megírja, azaz prediktív karbantartás miatt űrtechnikai, továbbra is kapcsolódik, de a legfrissebb információkat a legújabb cikkben található [Itt](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace). 

## <a name="acknowledgements"></a>**A nyugtázás**
Ez a cikk a felhasználók a adatszakértőkön Pozsony Zhang Gauher Shaheen, Fidan Boylu Uz és szoftver visszafejtés Dan Grecoe a Microsoft által készített.

## <a name="overview"></a>**Áttekintés**
Megoldás sablonok készültek, annak érdekében, a folyamatot nevezzük, egy E2E bemutató Cortana Intelligence Suite felett. Egy telepített sablon kiépíteni az előfizetés szükséges a Cortana Intelligence összetevőkkel, és a build a köztük lévő viszonyt is. Egy adatok készítő alkalmazást, amely akkor letölti és telepíti a helyi számítógépen, a megoldás sablon üzembe helyezése után előállított mintaadatokkal az adatok folyamat is magok. A készítő által létrehozott adatokat fog hydrate, az adatok feldolgozási sorban lévő és a machine learning előrejelzéseket, amelyek megjeleníthetők a Power BI-irányítópult létrehozása start. A telepítési folyamat végigvezeti a megoldás hitelesítő adatok beállításához számos lépést. Ellenőrizze, hogy a hitelesítő adatokat, például a megoldás neve, a felhasználónevet és jelszót a telepítés során megadott rögzítése.  

A jelen dokumentum célja a referencia-architektúrában és a különböző összetevőket az előfizetésében megoldás sablon részeként. A dokumentum cseréje a mintaadatok az elemzések és a saját adatok által megtekintheti saját valós adatokkal is bemutat. Emellett a dokumentum ismerteti a kijelzők lehet módosítani, ha a saját adataival a megoldásban testre kell megoldás-sablon. A Power BI-irányítópultot megoldás sablon létrehozásával kapcsolatos útmutatást végén.

> [!TIP]
> Töltse le, és nyomtassa ki a [PDF-verziót a dokumentum](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).
> 
> 

## <a name="big-picture"></a>**Nagyméretű kép**
![A prediktív karbantartási architektúrája](./media/cortana-analytics-technical-guide-predictive-maintenance/predictive-maintenance-architecture.png)

Amikor a megoldást már telepítették, Cortana Analytics Suite belül különböző Azure-szolgáltatások rendszer aktiválja azokat (*azaz* Az Event Hubs, a Stream Analytics, HDInsight, adat-előállító gépi tanulás, *stb*). A fenti architektúrája látható, magas szinten, hogyan a prediktív karbantartási megoldás repüléstechnikai sablon végpont értékekből összeállított. Vizsgálja meg ezeket a szolgáltatásokat az azure-portálon létrehozott a megoldás üzembe helyezése a HDInsight kivételével, ez a szolgáltatás ki van építve megoldás sablon diagram rájuk kattintva lesz az igénylés, ha a kapcsolódó folyamat tevékenységek szükségesek a lefutott, és törölte ezt követően.
Letöltheti a [a diagram teljes méretű változatát](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

A következő szakaszok ismertetik a minden egyes adatra.

## <a name="data-source-and-ingestion"></a>**Az adatforrás és feldolgozási**
### <a name="synthetic-data-source"></a>Szintetikus adatforrás
Ehhez a sablonhoz használt adatforrás egy asztali alkalmazás, amely akkor letölti és helyileg futtassa a sikeres telepítést jönnek létre. Töltse le és telepítse az alkalmazást a Tulajdonságok sávon prediktív karbantartási Adatgenerátor nevű megoldás sablon diagram első csomópontjának kijelölésekor vonatkozó utasításokat talál. Ez az alkalmazás-hírcsatornák a [Azure Event Hubs](#azure-event-hub) szolgáltatás, vagy ugyanazt az eseményeket, a megoldás folyamatot a többi használandó. Ez az adatforrás áll, vagy származó nyilvánosan elérhető adatait a [NASA adattárház](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/) használatával a [turbóventillátoros motor teljesítménycsökkenést szimuláció adatkészlet](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan).

Az esemény generációs alkalmazást feltölti az Azure Event Hubs csak, amíg a számítógép végrehajtása történik.

### <a name="azure-event-hub"></a>Az Azure event hubs
A [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) szolgáltatása címzettje a fent leírt szintetikus adatforrás által megadott bemenetet.

## <a name="data-preparation-and-analysis"></a>**Adatok előkészítése és elemzése**
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
A [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) szolgáltatásával közel valós idejű elemzés a bemeneti adatfolyam nyújtanak a [Azure Event Hubs](#azure-event-hub) szolgáltatás, és tegye közzé eredményei közül a [Power BI](https://powerbi.microsoft.com)irányítópult, valamint az összes nyers bejövő események archiválása a [Azure Storage](https://azure.microsoft.com/services/storage/) szolgáltatás később feldolgozásra, amelyet a [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) szolgáltatás.

### <a name="hd-insights-custom-aggregation"></a>Egyéni összesítési HD Insights
Az Azure HD Insight-szolgáltatás futtatásához használt [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (Azure Data Factory által összehangolva) parancsfájlok összesítések ad az Azure Stream Analytics szolgáltatással lettek archiválva nyers eseményeket.

### <a name="azure-machine-learning"></a>Azure Machine Learning
A [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) szolgáltatás használható-e (Azure Data Factory által összehangolva.) a előrejelzéseket készítsen a fennmaradó élettartama (Szabályainak) egy adott repülőgép motor a kapott bemeneti adatokat adott meg.

## <a name="data-publishing"></a>**Adatok közzététele**
### <a name="azure-sql-database-service"></a>Az Azure SQL adatbázis-szolgáltatás
A [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) szolgáltatás fogja tárolni (kezeli az Azure Data Factory) az Azure Machine Learning szolgáltatás, amely a fognak használni az előrejelzés a [Power BI](https://powerbi.microsoft.com) irányítópult.

## <a name="data-consumption"></a>**Adatok felhasználásához**
### <a name="power-bi"></a>Power BI
A [Power BI](https://powerbi.microsoft.com) szolgáltatás használatával egy irányítópultot, amely tartalmazza az összesítések és által biztosított értesítések megjelenítése az [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) szolgáltatás, valamint tárolt Szabályainak előrejelzéseket [Azure SQL Adatbázis](https://azure.microsoft.com/services/sql-database/) használatával keletkezett a [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) szolgáltatás. A Power BI-irányítópultot megoldás sablon létrehozásával kapcsolatos útmutatásért tekintse meg a következő szakaszban.

## <a name="how-to-bring-in-your-own-data"></a>**Útmutató saját adatok**
Ez a szakasz ismerteti, hogyan kell a saját adatait az Azure-ba, és milyen területeken igényel módosításokat az adatok akkor vonja ebbe az architektúrába.

Nem valószínű, hogy bármely kapcsolása dataset adatkészlet által használt megfelelő a [turbóventillátoros motor teljesítménycsökkenést szimuláció adatkészlet](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan) megoldás sablon használatos. Az adatok és a követelmények ismertetése a sablon a saját adatok módosítása a kulcsfontosságú lesz. Ha ez az első kapta az Azure Machine Learning szolgáltatáshoz, egy bemutatása példa használatával kaphat [az első kísérlet létrehozása](../studio/create-experiment.md).

Az alábbi szakaszok ismertetik a sablonból, amely a módosítások van szükség, amikor új adatkészlet megjelent szakaszok.

### <a name="azure-event-hub"></a>Az Azure Event Hubs
Az Azure Event Hubs szolgáltatás nem nagyon általános, úgy, hogy az adatok is közzé lehet tenni a központi vagy a fürt megosztott kötetei szolgáltatás, vagy a JSON formátumban. Az Azure Event Hubs különleges feldolgozás nem történik, de fontos, hogy tudomásul veszi be táplált adatokat.

Ez a dokumentum nem ismerteti az adatok, de használatával egyszerűen küldhet eseményeket vagy adatokat az Azure-Eseményközpontok felé az Event Hub API használatával.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Az Azure Stream Analytics szolgáltatás segítségével adatfolyamok olvasása és írása források tetszőleges számú adatokat közel valós idejű elemzési nyújtanak.

A prediktív karbantartási megoldás repüléstechnikai sablon az Azure Stream Analytics lekérdezési négy segédlekérdezéseket, minden felhasználó események kimenetek kellene négy különböző helyek, és az Azure Event Hubs szolgáltatás áll. A kimenetek három Power BI-adatkészletek és egy Azure tárolási helye állnak.

Az Azure Stream Analytics lekérdezési találhatók:

* Jelentkezzen be az Azure-portálon
* A Stream Analytics-feladatok keresése ![Stream Analytics ikon](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-stream-analytics.png) , hogy a megoldás telepítésekor jött létre (*p.l.*, **maintenancesa02asapbi** és **maintenancesa02asablob** a prediktív karbantartási megoldás)
* Kiválasztása
  
  * ***BEMENETEK*** adjon meg a lekérdezés megtekintése
  * ***LEKÉRDEZÉS*** magát a lekérdezést megtekintése
  * ***KIMENETI*** a különböző kimenetének megtekintése

Azure Stream Analytics lekérdezési konstrukció kapcsolatos információk találhatók a [Stream Analytics lekérdezési hivatkozás](https://msdn.microsoft.com/library/azure/dn834998.aspx) az MSDN Webhelyén.

Ebben a megoldásban a lekérdezések kimeneti három adatkészletek a közel valós idejű elemzési információt a bejövő adatfolyam megoldás sablon részeként biztosított Power BI-irányítópulthoz. Mivel a bejövő adatformátum implicit ismerete, ezeket a lekérdezéseket kell módosítani a adatformátum alapján.

A lekérdezés második Stream Analytics-feladat **maintenancesa02asablob** egyszerűen kiírja az összes [Eseményközpont](https://azure.microsoft.com/services/event-hubs/) események [Azure Storage](https://azure.microsoft.com/services/storage/) és ezért nem módosítása a teljes esemény adatformátum függetlenül információk Storage továbbítja adatfolyamként.

### <a name="azure-data-factory"></a>Azure Data Factory
A [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) szolgáltatás koordinálja a mozgás és az adatok feldolgozása. A prediktív karbantartási megoldás repüléstechnikai sablon az adat-előállítóban áll három [folyamatok](../../data-factory/v1/data-factory-create-pipelines.md) , helyezze át, és feldolgozza a különféle technológiái.  A data factory férhetnek megnyitása a a Data Factory csomópont a megoldás sablon diagram alján létre az üzemelő példányhoz, a megoldás. Ekkor megjelenik az Azure-portál data factoryval való. Ha hibaüzenet jelenik meg a adatkészletek alatt, figyelmen kívül hagyhatja azokat, mivel ezek a adatgenerátor indítása előtt telepített adat-előállító miatt. Ezek a hibák nem akadályozzák a data factory működését.

![Data Factory dataset hibák](./media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Ez a szakasz ismerteti a szükséges [folyamatok](../../data-factory/v1/data-factory-create-pipelines.md) és [tevékenységek](../../data-factory/v1/data-factory-create-pipelines.md) szerepel a [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Az alábbiakban van a megoldás a diagram nézetben.

![Azure Data Factory](./media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Két, az adat-előállító adatcsatornák tartalmazhat [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) parancsfájlok használt particionálása és összesíti az adatokat. Az áttelepítés előtt feljegyzett, ha a parancsfájlok találhatók a [Azure Storage](https://azure.microsoft.com/services/storage/) a telepítés során létrehozott fiók. A helyen lesz: maintenancesascript\\\\parancsfájl\\\\hive\\ \\ (vagy https://[Your megoldás name].blob.core.windows.net/maintenancesascript).

Hasonló a [Azure Stream Analytics](#azure-stream-analytics-1) lekérdezéseket, a [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) parancsfájlok bejövő adatformátum implicit ismerete, ezeket a lekérdezéseket volna kell módosítani a adatformátum alapján.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Ez [csővezeték](../../data-factory/v1/data-factory-create-pipelines.md) tartalmazza egy adott tevékenység - egy [HDInsightHive](../../data-factory/v1/data-factory-hive-activity.md) tevékenység segítségével egy [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , amelyen fut a [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) az adatok particionálása-parancsprogramot be [Azure Storage](https://azure.microsoft.com/services/storage/) során a [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) feladat.

A [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) particionálási feladat érték parancsfájl ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Ez [csővezeték](../../data-factory/v1/data-factory-create-pipelines.md) több tevékenységet tartalmaz, és amelynek végeredménynek a pontozott előrejelzéseket a [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) megoldás sablonhoz társított kísérlet.

Ez szereplő tevékenységeket a következők:

* [HDInsightHive](../../data-factory/v1/data-factory-hive-activity.md) tevékenység segítségével egy [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , amelyen fut a [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) összesítések elvégzéséhez és a szükséges jellemzőkiemelés parancsfájl a [Azure Gépi tanulás](https://azure.microsoft.com/services/machine-learning/) kipróbálásához.
  A [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) particionálási feladat érték parancsfájl ***PrepareMLInput.hql***.
* [Másolás](https://msdn.microsoft.com/library/azure/dn835035.aspx) helyezi át az eredményeket a tevékenység a [HDInsightHive](../../data-factory/v1/data-factory-hive-activity.md) egyetlen tevékenységet [Azure Storage](https://azure.microsoft.com/services/storage/) blob, amely lehet hozzáférését a [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx)tevékenység.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) tevékenység, amely behívja a [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) kísérlet, amely az eredményeket, mivel ez egy put egyetlen eredményez [Azure Storage](https://azure.microsoft.com/services/storage/) blob.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Ez [csővezeték](../../data-factory/v1/data-factory-create-pipelines.md) tartalmazza egy adott tevékenység - egy [másolási](https://msdn.microsoft.com/library/azure/dn835035.aspx) tevékenység, amely helyezi át az eredményeket a [Azure Machine Learning](#azure-machine-learning) a kísérletezhet a  ***MLScoringPipeline*** számára a [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) , hogy a megoldás sablon telepítésének részeként lett kiépítve.

### <a name="azure-machine-learning"></a>Azure Machine Learning
A [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) használt kísérletezhet, ez a megoldás sablon biztosít a fennmaradó hasznos élettartama (Szabályainak) repülőgép motor számára. A kísérlet felhasznált adathalmaz jellemző, és ezért módosítása vagy cseréje állapotba kerül, az adatok adott szükséges.

További információ az Azure Machine Learning kísérlet létrehozásának módja: [prediktív karbantartási: 1. lépés 3-as adatok előkészítése, a szolgáltatás fejlesztés](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>**A figyelő folyamatban**
 A Adatgenerátor eszköztárat, a feldolgozási folyamat veszi át a hidratált beolvasása és a megoldás a különböző összetevők indítsa el a következő a parancsok a Data Factory által kibocsátott kicking. Két módon figyelheti a folyamatot.

1. A blob storage a nyers bejövő adatokat ír egy Stream Analytics-feladat. Ha rákattint az Blob Storage-összetevő a megoldás a képernyőről, sikeresen telepített a megoldás és a jobb oldali panelen kattintson a Megnyitás, azt választja, az a [kezelési portál](https://portal.azure.com/). Ezután kattintson a Blobok. A következő panelen látni fogja a tárolók listája. Kattintson a **maintenancesadata**. A következő panelen megjelenik a **rawdata** mappa. A rawdata mappában található látni fogja a mappák, valamint a neveket például órát = 17 óra = 18 stb. Ha ezeket a mappákat, azt jelzi, hogy a nyers adatok sikeresen jön létre a számítógépen és a blob storage-ban tárolt. Meg kell jelennie a csv-fájlok, amelyben véges mérete (MB) az érintett mappákat.
2. A folyamat utolsó lépését az adatokat (p.l. a gépi tanulás által előrejelzéseket) az SQL-adatbázisba írni. Lehetséges, hogy várnia legfeljebb három órával az adatok jelennek meg az SQL-adatbázis. Figyelje az elérhető az SQL-adatbázis mennyi adatot egyike a keresztül [azure-portálon](https://manage.windowsazure.com/). A bal oldali panelen keresse meg az SQL-ADATBÁZISOK ![SQL ikon](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-SQL-databases.png) , és kattintson rá. Keresse meg az adatbázis **pmaintenancedb** , és kattintson rá. A következő oldalon a lap alján kattintson a kezelés
   
    ![Ikon kezelése](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-manage.png).
   
    Itt kattintson az új lekérdezés és a lekérdezés megadása a sorok (pl. a PMResult válassza count(*)) száma. Az adatbázis növekedésének megfelelően, növelje a tábla sorainak száma.

## <a name="power-bi-dashboard"></a>**A Power BI-irányítópulton**
### <a name="overview"></a>Áttekintés
Ez a szakasz ismerteti, hogyan Azure Stream Analytics (gyakran használt adatok elérési útja), a valós idejű adatok megjelenítése Power BI-irányítópultot beállítása valamint kötegelt előrejelzés eredményeinek képzési (cold) az Azure machine.

### <a name="setup-cold-path-dashboard"></a>A telepítő cold elérési irányítópult
A cold elérési adatok feldolgozási alapvető célja az egyes repülőgép motor (hátralévő élettartama) prediktív Szabályainak megszerezni a felhőszolgáltató közötti átviteléhez (ciklus) művelet befejeződése után. Az előrejelzés eredmény repülőgép motor, amely az elmúlt 3 órában a felhőszolgáltató közötti átviteléhez végzett előrejelzésére 3 óránként frissül.

A Power BI csatlakozik Azure SQL-adatbázis adatforrásként, az előrejelzési eredmények tárolásához. Megjegyzés: 1), a megoldás telepítéséhez, egy valódi előrejelzés jelennek meg az adatbázisban 3 órán belül.
A pbix-fájl, melyet a kódgenerátor letöltési tartalmaz kezdőérték adatokat, hogy a Power BI-irányítópult azonnal létrehozhat. 2.) Ez a lépés az előfeltétel, hogy töltse le és telepítse az ingyenes szoftvert [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Az alábbi lépéseket ismerteti a pbix-fájl csatlakoztatása az SQL-adatbázis, amely a megoldás központi telepítési adatokat tartalmazó időpontjában hoz létre a (*p.l.*. Előrejelzés eredmények) a képi megjelenítéshez tartozó.

1. Az adatbázis hitelesítő adatainak lekéréséhez.
   
   Szüksége lesz **adatbázis-kiszolgáló nevét, az adatbázisnév, felhasználónevet és jelszót** előtt áthelyezése a következő lépéseket. Az alábbiakban a lépéseit ismerteti azok megkereséséről.
   
   * Egyszer **"Azure SQL Database"** a megoldás sablonban diagram zöldre, kattintson rá, és kattintson a **"Megnyitás"**.
   * Láthatja, hogy egy új lap/böngészőablakot, amely az Azure portál oldalát jeleníti meg. Kattintson a **"Erőforráscsoportok"** a bal oldali panelen.
   * Válassza ki az előfizetést, a megoldás telepítéséhez használ, és válassza **"YourSolutionName\_ResourceGroup"**.
   * Az új pop kimenő panelen, kattintson a ![SQL ikon](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-sql.png) ikon az adatbázis eléréséhez. Az adatbázis nevének megadása mellett az alábbi ikon (*pl.*, **"pmaintenancedb"**), és a **adatbázis-kiszolgáló neve** megtalálható-e a Server name tulajdonsággal, és ehhez hasonlóan kell kinéznie a **YourSoutionName.database.windows.net**.
   * Az adatbázis **felhasználónév** és **jelszó** ugyanaz, mint a felhasználónév és jelszó korábban tárolja, a megoldás üzembe helyezése során.
2. Az adatforrás a cold elérési jelentés fájl frissítése. a Power BI Desktop.
   
   * A számítógépen, amelyen le, és a kódgenerátor fájl unzipped mappában kattintson duplán a **Power bi\\PredictiveMaintenanceAerospace.pbix** fájlt. Ha a fájl megnyitásakor minden mellőzött figyelmeztető üzenet jelenik meg, figyelmen kívül hagyja azokat. A fájl felső részén található kattintson **szerkesztése lekérdezések**.
     
     ![Lekérdezések szerkesztése](./media/cortana-analytics-technical-guide-predictive-maintenance/edit-queries.png)
   * Látni fogja, két tábla **RemainingUsefulLife** és **PMResult**. Válassza ki az első tábla, és kattintson a ![lekérdezés beállítások ikonra](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-query-settings.png) melletti **"Forrás"** alatt **alkalmazott LÉPÉSEKET** a jobb oldali **"Lekérdezés beállításai"** panel. Hagyja figyelmen kívül minden mellőzött figyelmeztető üzenet jelenik meg.
   * Cserélje le a pop ki ablakot, **"Server"** és **"Database"** a saját kiszolgáló és az adatbázis nevének, és kattintson a **"OK"**. A kiszolgálónév, ellenőrizze, hogy az 1433-as port megadott (**YourSoutionName.database.windows.net, 1433**). Hagyja meg az adatbázis mező **pmaintenancedb**. Figyelmen kívül hagyja a képernyőn megjelenő figyelmeztető üzeneteket.
   * A következő pop ki ablakot, két lehetőség közül választhat a bal oldali ablaktáblán látható (**Windows** és **adatbázis**). Kattintson a **"Database"**, töltse ki a **"Username adat"** és **'Password'** (azt a felhasználónevet és jelszót, amikor először a megoldás telepített és létrehozott egy Azure SQL-adatbázis). A ***válassza ki, melyik szintre legyenek érvényesek a beállítások***, ellenőrizze az adatbázis-szintű beállítás. Kattintson a **"Csatlakozás"**.
   * Kattintson a második tábla **PMResult** kattintson ![navigációs ikon](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-navigation.png) melletti **"Forrás"** alatt **alkalmazott LÉPÉSEKET** a jobb oldali **"Lekérdezés beállításai"** panelen, és módosíthatja az a kiszolgáló és az adatbázis nevének, ahogy a fenti lépéseket, és kattintson az OK gombra.
   * Amennyiben most interaktív, az előző oldalra, az ablak bezárásához. Egy üzenet jelenik out - kattintson **alkalmaz**. Végül kattintson a **mentése** gombra a módosítások mentéséhez. A Power BI-fájl most hozott létre kapcsolatot a kiszolgálóval. Üres a képi megjelenítés esetén győződjön meg arról, hogy törli a kijelölt elemek összes adatok megjelenítéséhez a jelmagyarázatokból jobb felső sarkában a radír ikonjára kattint a képi megjelenítések. A frissítés gombra segítségével új adatok vizuális tükrözi. Kezdetben csak akkor jelenik meg a adatait a képi megjelenítések a, az adat-előállítóban ütemezett frissítése 3 óránként. 3 óra elteltével jelenik meg új előrejelzéseket megjelennek a képi megjelenítések, amikor az adatok frissítése.
3. (Választható) A cold elérési irányítópultot közzététele [Power BI online](http://www.powerbi.com/). Vegye figyelembe, hogy ez a lépés a Power BI fiókot (vagy Office 365-fiókkal) kell-e.
   
   * Kattintson a **"Publish"** és néhány másodperc múlva megjelenik egy ablak megjelenítése a "Power BI sikeres közzététel!" a zöld pipa jelzi. Kattintson az alábbi "Megnyitás PredictiveMaintenanceAerospace.pbix a Power BI" hivatkozásra. Részletes utasításokat talál [a Power BI Desktop közzététele](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Új irányítópult létrehozása: kattintson a  **+**  jelentkezzen mellett a **irányítópultok** szakaszt, a bal oldali ablaktáblán. Adja meg az új irányítópult "Prediktív karbantartási bemutató" nevét.
   * Ha a jelentés megnyitásához kattintson ![RAJZSZÖG ikonra](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-pin.png) rögzítése az irányítópulton való rögzítéséhez képi megjelenítések. Részletes utasításokat talál [egy csempe rögzítése egy Power BI-irányítópult a jelentés](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Ugrás az irányítópult-oldalon, és méretének és a képi megjelenítések helyét és szerkessze a címben. Részletes utasításokat talál szerkesztése a csempéket a [Szerkesztés mozaik – átméretezési, move, nevezze át, PIN-kód, törlése, hivatkozás hozzáadása a](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Íme egy példa irányítópult néhány cold elérési megjelenítésekkel azt van rögzítve.  Attól függően, hogy mennyi ideig a adatgenerátor futtatja a vizuális a számok lehetnek.
     <br/>
     ![A végső nézete](./media/cortana-analytics-technical-guide-predictive-maintenance/final-view.png)
     <br/>
   * Az adatok frissítés ütemezéséhez az egérmutatóval rámutat a **PredictiveMaintenanceAerospace** dataset, kattintson ![Elipsis ikon](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-elipsis.png) majd **ütemezés frissítése**.
     <br/>
     **Megjegyzés:** Ha megjelenik egy figyelmeztetés masszírozó, kattintson a **hitelesítő adatok szerkesztése** , és győződjön meg arról, hogy az adatbázis hitelesítő adatai megegyeznek-e az 1. lépésben leírt.
     <br/>
     ![A frissítésütemezés](./media/cortana-analytics-technical-guide-predictive-maintenance/schedule-refresh.png)
     <br/>
   * Bontsa ki a **ütemezés frissítése** szakasz. Kapcsolja be "az adatok naprakészen tarthatók".
     <br/>
   * A frissítés ütemezése a igények alapján. További információkért lásd: [adatfrissítési a Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>A telepítő gyakran használt adatok elérési útja irányítópult
Az alábbi lépéseket ismerteti hogyan jelenítheti meg a megoldás üzembe helyezése során létrehozott Stream Analytics-feladatok kimenete valós idejű adatokat. A [Power BI online](http://www.powerbi.com/) fiók szükséges a következő lépésekkel. Ha nincs fiókja, akkor [hozzon létre egyet](https://powerbi.microsoft.com/pricing).

1. Adja hozzá a Power BI-kimenet Azure Stream Analytics (ASA).
   
   * Kövesse az utasításokat kell [Azure Stream Analytics & Power BI: valós idejű látható-e a streamelési adatok a valós idejű elemzési irányítópult](../../stream-analytics/stream-analytics-power-bi-dashboard.md) , a Power BI az Azure Stream Analytics-feladat eredményének beállítása Irányítópult.
   * Az ASA lekérdezés rendelkezik három kimenetek, amelyek **aircraftmonitor**, **aircraftalert**, és **flightsbyhour**. A lekérdezés fülre kattintva megtekintheti a lekérdezést. Megfelelő ezek a táblázatok, szüksége lesz egy kimenet hozzáadása ASA. Az első kimeneti hozzáadásakor (*pl.* **aircraftmonitor**) Győződjön meg arról, hogy a **kimeneti Alias**, **Adatkészletnevet** és **tábla Név** megegyezik (**aircraftmonitor**). Ismételje meg a kimeneti hozzáadandó **aircraftalert**, és **flightsbyhour**. Miután hozzáadta a három kimeneti táblák, és a ASA feladat elindítása, egy megerősítő üzenetet kapja meg (*pl.*, "Indítása Stream Analytics-feladat sikeres maintenancesa02asapbi").
2. Jelentkezzen be [online Power bi-ban](http://www.powerbi.com)
   
   * A bal oldali panelen adatkészletek szakasz a saját munkaterületen a ***DATASET*** nevek **aircraftmonitor**, **aircraftalert**, és **flightsbyhour** meg kell jelennie. Ez az az előző lépésben az Azure Stream Analytics leküldött streamadatok. A dataset **flightsbyhour** előfordulhat, hogy nem jelenik meg a másik két adatkészletet az SQL-lekérdezést mögötte jellemzői miatt egyszerre. Azonban azt kell jelenik meg egy óra múlva.
   * Győződjön meg arról, hogy a ***képi megjelenítések*** ablak meg nyitva, és a képernyő jobb oldalán látható.
3. Miután a Power BI-bA áramló adatokat, megkezdheti a streamelési adatok megjelenítése. Alább néhány gyakran használt adatok elérési útja megjelenítésekkel egy példa irányítópult rögzítve azt. Más irányítópult-csempéihez megfelelő adatkészletek alapján hozhat létre. Attól függően, hogy mennyi ideig a adatgenerátor futtatja a vizuális a számok lehetnek.

    ![Irányítópult-nézet](media\cortana-analytics-technical-guide-predictive-maintenance\dashboard-view.png)

1. Az alábbiakban néhány lépést hozzon létre egyet a fenti – csempék "érzékelő 11 vs flotta nézetét. Küszöbérték 48.26" csempe:
   
   * Kattintson a dataset **aircraftmonitor** adatkészletek szakasz bal oldali panelen.
   * Kattintson a **vonaldiagram** ikonra.
   * Kattintson a **feldolgozott** a a **mezők** ablaktáblán, a "Tengely" mutatja, hogy a **képi megjelenítések** ablaktáblán.
   * Kattintson a "s11" és "s11\_riasztás", mindkettő "Értékek" alatt jelenik meg. A kis melletti nyílra **s11** és **s11\_riasztás**, "Sum" módosítása "Átlag" értékre.
   * Kattintson a **mentése** felül és a jelentés "aircraftmonitor" nevét. A jelentés "aircraftmonitor" nevű jelenik meg a **jelentések** szakasz a **Navigator** a bal oldali ablaktáblán.
   * Kattintson a **PIN-kód Visual** ikonra a vonaldiagram jobb felső sarkában. "PIN-kód irányítópult" ablak lehet, hogy jelenik meg, hogy válasszon egy irányítópultot. "A prediktív karbantartási bemutató", majd kattintson a "PIN-kód".
   * Az egérrel a csempe az irányítópulton keresztül, kattintson a jobb felső sarokban található módosítása "érzékelő 11 vs flotta nézetét a cím az"edit"ikonra. Küszöbérték 48.26" és a felirat"Között járműflotta időbeli átlag"értékre.

## <a name="how-to-delete-your-solution"></a>**A megoldás törlése**
Győződjön meg arról, hogy le a adatgenerátor használatakor nem aktív a megoldás, a adatgenerátor futtató tájékozódnia magasabb költségek. Ha nem használnak, törölje a megoldás. A megoldás törlése eltávolítja a megoldás üzembe helyezésekor az előfizetésben kiépített összetevőit. Törölje a megoldás kattintson a bal oldali panelen, a megoldás sablont, majd kattintson a Törlés a megoldás neve.

## <a name="cost-estimation-tools"></a>**Költség becslése eszközök**
A következő két eszközök segítségével jobb megértése érdekében a prediktív karbantartási futtató repüléstechnikai Megoldássablon az előfizetésében szereplő részt a teljes költségek érhetők el:

* [A Microsoft Azure költség négyzetgyökének eszköz (online)](https://azure.microsoft.com/pricing/calculator/)
* [A Microsoft Azure költség négyzetgyökének eszköz (asztali verzió)](http://www.microsoft.com/download/details.aspx?id=43376)

