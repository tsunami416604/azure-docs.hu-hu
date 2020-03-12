---
title: Útmutató a prediktív karbantartás a légi közlekedésben – a csoportos adatelemzési folyamat
description: Technikai útmutató a megoldási sablonhoz a repülési, segédprogramok és szállítás prediktív karbantartásához.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 3b3a0b00ee6e1e170023584c2e643a5802166428
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087594"
---
# <a name="technical-guide-to-the-solution-template-for-predictive-maintenance-in-aerospace"></a>Technikai útmutató a repülőgépipari prediktív karbantartás megoldási sablonja számára

> [!Important]
> Ez a cikk elavult. A repülőgépiparban a prediktív karbantartásról szóló vita továbbra is releváns, de a jelenlegi információk esetében az [üzleti közönség megoldásának áttekintése](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace)című témakörben talál további információt.


A megoldási sablonok célja, hogy felgyorsítsa a E2E-bemutató létrehozásának folyamatát. Az üzembe helyezett sablon az előfizetését a szükséges összetevőkkel hozza létre, majd létrehozza a közöttük lévő kapcsolatokat. Azt is feltölti a adatfolyamat adatok-készítő alkalmazás, amely letölti és telepíti a helyi gépen a megoldássablon telepítése után a mintaadatokkal. A generátor származó adatok hydrates a adatfolyamat és kezdő létrehozása a machine learning előrejelzéseket, amelyek megjeleníthetők a Power BI-irányítópulton.

Az üzembe helyezési folyamat végigvezeti a megoldás hitelesítő adatok beállításához számos lépést. Győződjön meg arról, jegyezze fel a hitelesítő adatokat, például a megoldás nevét, a felhasználónevet és jelszót, amely az üzembe helyezés során. 


Ez a cikk célja a következők:
- Írja le a referencia-architektúra és összetevők az előfizetésében.
- Cserélje le a mintaadatokat a saját adatok bemutatásához. 
- A megoldássablon módosítása megjelenítése.  

## <a name="overview"></a>Áttekintés
![Prediktív karbantartás architektúra](./media/predictive-maintenance-technical-guide/predictive-maintenance-architecture.png)

A megoldás telepítésekor aktiválja az Azure-szolgáltatásokat, például az Event hub, a Stream Analytics, a HDInsight, a Data Factory és a Machine Learning. Architektúradiagram bemutatja, hogyan a karbantartás a légi és Űrközlekedés Megoldássablon jön létre. Ezek a szolgáltatások az Azure Portalon létrehozott (kivéve a HDInsight, amely igény szerint van kiépítve, ha a kapcsolódó folyamat tevékenységek futtatásához szükséges, és a megoldás üzembe helyezése sablon megoldásdiagramon kattintva megvizsgálhatja törli ezt követően).
Töltse le [a diagram teljes méretű verzióját](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

A következő szakaszok ismertetik a megoldás részei.

## <a name="data-source-and-ingestion"></a>Adatforrás és feldolgozó
### <a name="synthetic-data-source"></a>Szintetikus adatforrás
Ehhez a sablonhoz a rendszer a használt adatforrást egy letöltött asztali alkalmazásból hozza létre, amelyet a sikeres telepítés után helyileg futtat.

Töltse le és telepítse az alkalmazás vonatkozó utasításokat talál, jelölje be az első fürtcsomópont, prediktív karbantartás Adatgenerátor, a megoldás sablon diagramon. A Tulajdonságok sávon található utasításokat. Ez az alkalmazás az [Azure Event hub](#azure-event-hub) szolgáltatást a megoldás többi folyamatában használt adatpontokkal vagy eseményekkel együtt táplálja. Ez az adatforrás a [NASA-adattárházból](https://c3.nasa.gov/dashlink/resources/139/) származó nyilvánosan elérhető adatokból származik, a [Turbofan motor degradációs szimulációs adatkészletének](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan)használatával.

Az esemény generálása alkalmazás feltöltése az Azure Event Hubs, csak azt a számítógépen végrehajtása közben.  

### <a name="azure-event-hub"></a>Azure Event Hub  
Az [Azure Event hub](https://azure.microsoft.com/services/event-hubs/) szolgáltatás a szintetikus adatforrás által megadott bemenet címzettje.

## <a name="data-preparation-and-analysis"></a>Adat-előkészítési és -elemzés  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
A [Azure stream Analytics](https://azure.microsoft.com/services/stream-analytics/) segítségével közel valós idejű elemzéseket biztosíthat az [Azure Event hub](#azure-event-hub) szolgáltatásból származó bemeneti streamhez. Ezután közzéteheti az eredményeket egy [Power bi](https://powerbi.microsoft.com) irányítópulton, és archiválja az összes nyers bejövő eseményt az [Azure Storage](https://azure.microsoft.com/services/storage/) szolgáltatásba a [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) szolgáltatás általi későbbi feldolgozás céljából.

### <a name="hdinsight-custom-aggregation"></a>HDInsight egyéni összesítés
A HDInsight használatával futtassa a [kaptár](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) -parancsfájlokat (Azure Data Factory) a Azure stream Analytics erőforrás használatával archivált nyers események összesítésének biztosításához.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Készítse elő az előrejelzéseket egy adott repülőgép-motor hátralévő hasznos élettartamára (RUL) a [Azure Machine learning szolgáltatással](https://azure.microsoft.com/services/machine-learning/) kapott bemeneti adatok használatával (Azure Data Factory). 

## <a name="data-publishing"></a>Adatok közzététele
### <a name="azure-sql-database"></a>Azure SQL Database
Használja a [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) a Azure Machine learning által fogadott előrejelzések tárolására, amelyeket a rendszer a [Power bi](https://powerbi.microsoft.com) irányítópulton használ fel.

## <a name="data-consumption"></a>Adathasználat
### <a name="power-bi"></a>Power BI
A [Power bi](https://powerbi.microsoft.com) segítségével megjelenítheti a [Azure stream Analytics](https://azure.microsoft.com/services/stream-analytics/)által biztosított összesítéseket és riasztásokat tartalmazó irányítópultot, valamint a [Azure Machine learning](https://azure.microsoft.com/services/machine-learning/)használatával előállított [Azure SQL Databaseban](https://azure.microsoft.com/services/sql-database/) tárolt RUL-előrejelzéseket.

## <a name="how-to-bring-in-your-own-data"></a>Hogyan lehet a saját adatok beolvasása
Ez a szakasz ismerteti, hogyan lehet a saját adatok importálása az Azure-ba, és milyen területeken módosítások szükségesek az adatok, ez az architektúra állapotba.

Nem valószínű, hogy az adatkészlet megegyezik az ehhez a megoldás-sablonhoz használt [Turbofan-motor-romlási szimulációs](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) adatkészlettel használt adatkészlettel. Az adatok és a követelmények ismertetése alapvető fontosságúak a saját adatok sablon módosítása. 

A következő részekben bemutatjuk a sablon a részeit, amikor egy új adatkészlet bevezetett módosításokat igénylő.

### <a name="azure-event-hub"></a>Azure Event Hub
Az Azure Event Hubs szolgáltatás általános; adatok feladható a hub CSV vagy JSON formátumban. Speciális feldolgozás nem következik be az Azure Event Hubs, de fontos, hogy ismeri az adatkéréseket bele adatokat.

Ez a dokumentum nem ismerteti, hogy az adatokat, de egyszerűen küldhet eseményeket vagy adatokat egy Azure Event Hub, Event Hub API-val.

### <a name="azure-stream-analytics-1"></a>Azure Stream Analytics
A Azure Stream Analytics erőforrás segítségével közel valós idejű elemzéseket biztosíthat az adatfolyamok beolvasásával és az adatok tetszőleges számú forrásra való kihelyezésével.

A karbantartás a légi és Űrközlekedés Megoldássablon, az Azure Stream Analytics-lekérdezés áll négy sub-lekérdezések minden lekérdezés felhasználni az eseményeket az Azure Event Hubs szolgáltatásból a kimenetek négy különböző helyekre. Ezeket a kimeneteket három Power BI-adatkészletek és a egy Azure Storage-helyen állnak.

Az Azure Stream Analytics-lekérdezés találhatók:

* Csatlakozás az Azure Portalon
* A Stream Analytics feladatok megkeresése ![Stream Analytics ikon](./media/predictive-maintenance-technical-guide/icon-stream-analytics.png), amelyek a megoldás telepítésekor lettek létrehozva (*például* **maintenancesa02asapbi** és **maintenancesa02asablob** a prediktív karbantartási megoldáshoz)
* Kiválasztása
  
  * ***Bemenetek*** a lekérdezés bemenetének megtekintéséhez
  * ***Lekérdezés*** a lekérdezés megtekintéséhez
  * ***Kimenetek*** a különböző kimenetek megtekintéséhez

Azure Stream Analytics lekérdezési felépítéssel kapcsolatos információkért tekintse meg az MSDN [stream Analytics lekérdezési útmutatójában](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) .

Ebben a megoldásban a lekérdezések kimeneti három adatkészletek a közel valós idejű elemzési információkat a bejövő adatfolyam, ez a megoldássablon részeként a Power BI-irányítópultra. Mivel a bejövő adatok formátumának beállításszolgáltató jellegéből fakadóan ismeri, ezeket a lekérdezéseket módosítani kell az adatok formátum alapján.

A második Stream Analytics **maintenancesa02asablob** található lekérdezés egyszerűen kiadja az [Event hub](https://azure.microsoft.com/services/event-hubs/) összes eseményét az [Azure Storage](https://azure.microsoft.com/services/storage/) -nak, ezért az adatformátumtól függetlenül semmilyen módosítást nem igényel, mivel a teljes eseményre vonatkozó információt a rendszer a tárterületre továbbítja.

### <a name="azure-data-factory"></a>Azure Data Factory
A [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) szolgáltatás összehangolja az adatátvitelt és-feldolgozást. A repülőgépipari megoldás sablonjának prediktív karbantartása során az adatelőállító három olyan [folyamatból](../../data-factory/concepts-pipelines-activities.md) áll, amelyek különböző technológiák használatával helyezik át és dolgozzák fel az adatfeldolgozást.  Az adat-előállító eléréséhez nyissa meg a Data Factory csomópontra való a megoldás üzembe helyezéséhez létrehozott sablon megoldásdiagramon alján. Az adatkészletek alatt hibák miatt a data factory folyamatban már telepítve vannak az adatgenerálást lett elindítva. Ezeket a hibákat figyelmen kívül lesz hagyva, és nem akadályozzák az adat-előállító működése

![Data Factory adatkészlet hibák](./media/predictive-maintenance-technical-guide/data-factory-dataset-error.png)

Ez a szakasz a [Azure Data Factoryban](https://azure.microsoft.com/documentation/services/data-factory/)található szükséges [folyamatokat és tevékenységeket](../../data-factory/concepts-pipelines-activities.md) ismerteti. Ez a megoldás diagram nézetet.

![Azure Data Factory](./media/predictive-maintenance-technical-guide/azure-data-factory.png)

A gyár két folyamata tartalmazza az adatparticionálásra és-összesítésre használt [kaptár](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) -parancsfájlokat. Ha ezt megjegyezte, a parancsfájlok a telepítés során létrehozott [Azure Storage](https://azure.microsoft.com/services/storage/) -fiókban találhatók. A helyük a következő: maintenancesascript\\\\script\\\\kaptár\\\\ (vagy https://[a megoldás neve]. blob. Core. Windows. net/maintenancesascript).

A [Azure stream Analytics](#azure-stream-analytics-1) lekérdezésekhez hasonlóan a [struktúra](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) parancsfájljai is implicit ismerettel rendelkeznek a bejövő adatformátummal kapcsolatban, és az adatformátuma alapján kell módosítani.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Ez [a folyamat](../../data-factory/concepts-pipelines-activities.md) egyetlen tevékenységet tartalmaz – egy [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) tevékenységet, amely egy [struktúra](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) parancsfájlt futtató [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) használ az [Azure Storage](https://azure.microsoft.com/services/storage/) -ban az [Azure stream Analytics](https://azure.microsoft.com/services/stream-analytics/) -feladatokban elhelyezett adatok particionálásához.

A [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) particionálási feladat ***AggregateFlightInfo. HQL***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Ez a [folyamat](../../data-factory/concepts-pipelines-activities.md) több olyan tevékenységet tartalmaz, amelyek végeredménye az ehhez a megoldási sablonhoz társított [Azure Machine learning](https://azure.microsoft.com/services/machine-learning/) -kísérlet pontozásos előrejelzése.

Foglalt tevékenységek a következők:

* [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) -tevékenység olyan [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) használatával, amely egy [struktúra](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) -parancsfájlt futtat a [Azure Machine learning](https://azure.microsoft.com/services/machine-learning/) kísérlethez szükséges összesítések és funkciók tervezéséhez.
  A [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) particionálási feladat ***PrepareMLInput. HQL***.
* [Másolási](https://msdn.microsoft.com/library/azure/dn835035.aspx) tevékenység, amely áthelyezi az eredményeket a [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) tevékenységből egyetlen, az [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) tevékenység által elérhető [Azure Storage](https://azure.microsoft.com/services/storage/) -blobba.
* A [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) tevékenység meghívja a [Azure Machine learning](https://azure.microsoft.com/services/machine-learning/) kísérletet, és egyetlen [Azure Storage](https://azure.microsoft.com/services/storage/) -blobba helyezi az eredményeket.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Ez a [folyamat](../../data-factory/concepts-pipelines-activities.md) egyetlen tevékenységet tartalmaz: egy [másolási](https://msdn.microsoft.com/library/azure/dn835035.aspx) tevékenységet, amely áthelyezi a [Azure Machine learning](#azure-machine-learning) kísérlet eredményeit a ***MLScoringPipeline*** a megoldás sablonjának telepítése során kiépített [Azure SQL Databasera](https://azure.microsoft.com/services/sql-database/) .

### <a name="azure-machine-learning"></a>Azure Machine Learning
Az ehhez a megoldási sablonhoz használt [Azure Machine learning](https://azure.microsoft.com/services/machine-learning/) kísérlet a repülőgép-hajtóművek hátralévő hasznos élettartamát (RUL) biztosítja. A kísérlet az adatkészletben használt, és módosítást igényel, vagy az adatok adott helyettesítő állapotba hozni.

## <a name="monitor-progress"></a>A figyelő folyamatban
Az Adatgenerátor indul el, miután dehidratált megkezdi a folyamat, és a megoldás összetevői, indítsa el a data factory által kiadott parancsok művelet következő be megkezdése. Kétféleképpen figyelheti a folyamatot.

* A blob storage a Stream Analytics-feladatok egyik ír a nyers bejövő adatok. Ha a megoldás Blob Storage elemére kattint a képernyőn, akkor sikeresen üzembe helyezte a megoldást, majd a jobb oldali panelen kattintson a Megnyitás gombra, a rendszer elvégzi a [Azure Portal](https://portal.azure.com/). Ezután kattintson a Blobok. A következő panelen láthatja a tárolók listáját. Kattintson a **maintenancesadata**elemre. A következő panelen a **rawdata** mappa található. A rawdata mappában vannak a mappák nevekkel például óra = és 17 óra = 18. Ezek a mappák jelenlétét jelzi, hogy folyamatban van a nyers adatokat, a számítógép generált, és a blob storage-ban tárolt. Csv-fájlok (MB), a mappákat véges méretű kell megjelennie.
* A folyamat utolsó lépéseként írja az adatokat (például előrejelzéseket a gépi tanulás), SQL Database. Előfordulhat, hogy várjon legfeljebb három óra, az SQL Database-ben jelennek meg adatok. A SQL Databaseban rendelkezésre álló adatmennyiség figyelésének egyik módja a [Azure Portal](https://portal.azure.com/). A bal oldali panelen keresse meg az SQL-adatbázisok ![SQL Icon](./media/predictive-maintenance-technical-guide/icon-SQL-databases.png) és kattintson rá. Ezután keresse meg az adatbázis **pmaintenancedb** , és kattintson rá. A következő oldalon a lap alján kattintson a kezelés
   
    ![Ikon kezelése](./media/predictive-maintenance-technical-guide/icon-manage.png)
   
    Itt kattintson a új lekérdezés és a lekérdezés (például a PMResult válassza count(*)) sorok számát. Az adatbázis növekedésével növelje a tábla sorainak számát.

## <a name="power-bi-dashboard"></a>Power BI-irányítópult

Állítsa be a Power BI-irányítópulton jelenítheti meg az Azure Stream Analytics-adatok (gyakori elérésű útvonal) és az Azure machine learning (ritka elérésű útvonal) a batch-előrejelzési eredményeket.

### <a name="set-up-the-cold-path-dashboard"></a>A ritka elérésű útvonal irányítópult beállítása
A ritka elérésű útvonal adatok folyamat célja (ciklus) repülőjegyet befejeződésekor egyes repülőmotor prediktív RUL (fennmaradó hasznos élettartama) beolvasásához. Az előrejelzési eredmények, amelyek az elmúlt 3 órában repülőjegyet befejezte a repülőgép-hajtóművek előrejelzésére szolgáló 3 óránként frissül.

Power BI egy Azure SQL Databasehoz csatlakozik az adatforrásként, ahol a rendszer az előrejelzési eredményeket tárolja. 

Megjegyzés: 
1.    A megoldás üzembe helyezése után az előrejelzés az adatbázisban 3 órán belül megjelenik. A pbix-fájl, melyet a generátor letöltési néhány kezdőérték adatokat tartalmaz, így azonnal is létrehozhat a Power BI-irányítópulton. 
2.    Ebben a lépésben az ingyenes szoftver [Power bi asztal](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)letöltésére és telepítésére vonatkozó előfeltétel.

Az alábbi lépéseket leírja, hogyan csatlakozhat az SQL-adatbázis, amely a megoldás üzembe helyezése tartalmazó adatok (például előrejelzési eredményeket) megjelenítési időpontjában hoz létre a pbix-fájlt.

1. Az adatbázis hitelesítő adatainak lekérése.
   
   Mielőtt továbblép a következő lépésekre, szüksége lesz az **adatbázis-kiszolgáló nevére, az adatbázis nevére, a felhasználónévre és a jelszóra** . Az alábbiakban útmutató, hogyan találhatja meg azokat a lépéseket.
   
   * A megoldási sablon diagramjának **"Azure SQL Database"** után a zöldre vált, kattintson rá, majd kattintson a **Megnyitás**gombra.
   * Ekkor megjelenik egy új böngésző lap/ablak, amely megjeleníti a Azure Portal lapot. Kattintson a bal oldali panelen található **erőforráscsoportok** elemre.
   * Válassza ki a megoldás üzembe helyezéséhez használt előfizetést, majd válassza a **"YourSolutionName\_ResourceGroup"** lehetőséget.
   * Az új kiugró panelen kattintson a ![SQL Icon](./media/predictive-maintenance-technical-guide/icon-sql.png) ikonra az adatbázis eléréséhez. Az adatbázis neve az ikon mellett található (például **"pmaintenancedb"** ), az **adatbázis-kiszolgáló neve** pedig a kiszolgálónév tulajdonság alatt jelenik meg, és a **YourSolutionName.database.Windows.net**hasonlónak kell lennie.
   * Az adatbázis **felhasználóneve** és **jelszava** megegyezik a megoldás üzembe helyezése során korábban rögzített felhasználónévvel és jelszóval.
2. Frissítse az adatforrást a ritka elérésű útvonal fájlját a Power BI Desktopban.
   
   * Az a mappában, ahová letöltötte és kicsomagolta a létrehozó fájlt, kattintson duplán a **PowerBI\\PredictiveMaintenanceAerospace. pbix** fájlra. Ha a fájl megnyitásakor a figyelmeztető üzeneteket látja, figyelmen kívül hagyja őket. A fájl tetején kattintson a **"lekérdezések szerkesztése"** elemre.
     
     ![Lekérdezések szerkesztése](./media/predictive-maintenance-technical-guide/edit-queries.png)
   * Két tábla, **RemainingUsefulLife** és **PMResult**jelenik meg. Válassza ki az első táblázatot, és kattintson ![lekérdezési beállítások](./media/predictive-maintenance-technical-guide/icon-query-settings.png) ikonra a **"forrás"** elem **mellett, a** jobb oldali **"lekérdezési beállítások** " panelen. Hagyja figyelmen kívül a megjelenő figyelmeztető üzeneteket.
   * A kiugró ablakban cserélje le a **"kiszolgáló"** és az **"adatbázis"** elemet a saját kiszolgáló-és adatbázis-neveire, majd kattintson **az OK**gombra. A kiszolgáló neve mezőben adja meg a 1433-as portot (**YourSolutionName.database.Windows.net, 1433**). Hagyja meg az adatbázis mezőt **pmaintenancedb**. Figyelmen kívül hagyhatja a figyelmeztetéseket, a képernyőn megjelenő.
   * A következő kiugró ablakban két lehetőség jelenik meg a bal oldali ablaktáblán (**Windows** és **adatbázis**). Kattintson az **adatbázis**elemre, töltse ki a **"username"** és a **"password"** (a megoldás első telepítésekor megadott felhasználónevet és jelszót, és hozzon létre egy Azure SQL Database). A ***válassza ki, hogy melyik szinten szeretné alkalmazni ezeket a beállításokat***, és jelölje be az adatbázis-szint beállítást. Ezután kattintson a **"kapcsolat"** gombra.
   * Kattintson a második táblázat **PMResult** ,](./media/predictive-maintenance-technical-guide/icon-navigation.png) ![majd a " **forrás"** **elemre** a jobb oldali **"lekérdezési beállítások** " panelen, majd a kiszolgáló-és adatbázis-nevek frissítése a fenti lépésekkel, és kattintson az OK gombra.
   * Miután, interaktív van az előző oldalra, zárja be az ablakot. Üzenet jelenik meg – kattintson az **alkalmaz**gombra. Végül kattintson a Save ( **Mentés** ) gombra a módosítások mentéséhez. A Power BI-fájl most létesített kapcsolat a kiszolgálóval. Ha a Vizualizációk üres, győződjön meg arról, törölje a jelet a választott beállításokat, a Vizualizációk adatok megjelenítése a jelmagyarázat jobb felső sarkában a radír ikonra kattintva. A frissítés gomb használatával új adatokat tükrözik a vizualizációt. Kezdetben csak látható a kezdőérték-adatok a Vizualizációk az adat-előállító 3 óránként ütemezett módon. 3 óra után látni fogja a Vizualizációk megjelennek, amikor az adatok frissítése új előrejelzéseket.
3. Választható Tegye közzé a hűtőházi útvonal irányítópultját, hogy [Power bi online állapotba](https://www.powerbi.com/). Ehhez a lépéshez Power BI fiókra (vagy Office 365-fiókra) van szükség.
   
   * Kattintson a **Közzététel** gombra, és néhány másodperccel később megjelenik egy ablak, amely a "közzététel a Power bi sikerhez!" lehetőséget jeleníti meg. egy zöld pipának. Kattintson az alábbi "Nyílt PredictiveMaintenanceAerospace.pbix a Power BI" hivatkozásra. Részletes utasításokért tekintse meg [a Power bi Desktop közzétételét](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop)ismertető témakört.
   * Új irányítópult létrehozásához kattintson a bal oldali ablaktábla **irányítópultok** szakasza melletti **+** jelre. Adja meg az új irányítópult nevét "Prediktív karbantartási bemutató".
   * Miután megnyitotta a jelentést, kattintson ![rögzítés ikon](./media/predictive-maintenance-technical-guide/icon-pin.png) az összes vizualizáció rögzítéséhez az irányítópulton. Részletes utasításokért lásd: [csempe rögzítése Power bi irányítópulton egy jelentésből](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Nyissa meg az irányítópult-oldalon és a méret- és a Vizualizációk és szerkessze a címben. A csempék szerkesztésével kapcsolatos részletes utasításokért lásd: [csempe szerkesztése – átméretezés, áthelyezés, átnevezés, rögzítés, törlés, hivatkozás hozzáadása](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Íme egy példa irányítópult egyes ritka elérésű útvonal vizualizációkkal rögzítve rajta.  Attól függően, hogy mennyi ideig futtatja az adatgenerátor a Vizualizációk a számok eltérő lehet.
     <br/>
     ![végső nézet](./media/predictive-maintenance-technical-guide/final-view.png)
     <br/>
   * Az adat frissítésének megadásához vigye az egérmutatót a **PredictiveMaintenanceAerospace** adatkészletre, kattintson ![három pont ikont](./media/predictive-maintenance-technical-guide/icon-elipsis.png), majd válassza az **ütemezett frissítés**lehetőséget.
     <br/>
     **Megjegyzés:** Ha megjelenik egy figyelmeztetési masszázs, kattintson a **hitelesítő adatok szerkesztése** lehetőségre, és győződjön meg arról, hogy az adatbázis hitelesítő adatai ugyanazok, mint az 1. lépésben leírt módon.
     <br/>
     ![ütemezett frissítés](./media/predictive-maintenance-technical-guide/schedule-refresh.png)
     <br/>
   * Bontsa ki az **ütemterv frissítése** szakaszt. Kapcsolja be "tartsa adatait naprakészen".
     <br/>
   * A frissítés ütemezése az igényei szerint. További információk: [Adatfrissítés Power BIban](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Gyakori elérésű útvonal irányítópult beállítása
A következő lépések végigvezetik hogyan jelenítheti meg a Stream Analytics-feladatok a megoldás üzembe helyezése során előállított kimeneti adatokat. A következő lépések végrehajtásához egy [Power bi online](https://www.powerbi.com/) fiókra van szükség. Ha nem rendelkezik fiókkal, [létrehozhat egyet](https://powerbi.microsoft.com/pricing).

1. Adja hozzá a Power BI-kimenet az Azure Stream Analytics (ASA).
   
   * Követnie kell a [Azure Stream Analytics & Power bi: egy elemzési irányítópultot, amely valós idejű láthatóságot biztosít a folyamatos átviteli adatok](../../stream-analytics/stream-analytics-power-bi-dashboard.md) számára, hogy a Azure stream Analytics feladat kimenetét Power bi irányítópultként állítsa be.
   * Az ASA-lekérdezés három kimenettel rendelkezik, amelyek a következők: **aircraftmonitor**, **aircraftalert**és **flightsbyhour**. A lekérdezést a Query (lekérdezés) lapra kattintva tekintheti meg. ezeknek a tábláknak a megfelelő kimenetet kell hozzáadnia az ASA-hoz. Az első kimenet (**aircraftmonitor**) hozzáadásakor győződjön meg arról, hogy a **kimeneti alias**, az **adatkészlet neve** és a **tábla neve** azonos (**aircraftmonitor**). A **aircraftalert**és a **flightsbyhour**kimenetének hozzáadásához ismételje meg a lépéseket. Miután hozzáadta a három kimeneti táblák és az ASA-feladat elindult, egy megerősítő üzenetet ("Stream Analytics indítása feladat maintenancesa02asapbi sikeres") szerezheti be.
2. Jelentkezzen be [Power bi online](https://www.powerbi.com) -ba
   
   * A saját munkaterület bal oldali panel adatkészletek szakaszában a **aircraftmonitor**, a **aircraftalert**és a **flightsbyhour** ***adatkészlet*** nevenek kell megjelennie. Ez az az előző lépésben az Azure Stream Analytics leküldve a streamelt adatokat. Előfordulhat, hogy az adatkészlet **flightsbyhour** nem jelenik meg egyszerre a másik két adatkészletből, mert az SQL-lekérdezés jellegéből ered. Azonban ennek meg kell jelennie egy óra elteltével.
   * Győződjön meg arról, hogy a ***vizualizációk*** ablaktábla meg van nyitva, és a képernyő jobb oldalán jelenik meg.
3. Miután a Power BI szolgáltatásba áramló adatokat, elkezdheti a streamelési adatok megjelenítése. Alább néhány gyakori elérésű útvonal Vizualizációk egy példa az irányítópulton rögzítve van rá. Más irányítópult-csempék megfelelő adathalmazok alapján létrehozhat. Attól függően, hogy mennyi ideig futtatja az adatgenerátor a Vizualizációk a számok eltérő lehet.

    ![Irányítópult nézet](media/predictive-maintenance-technical-guide/dashboard-view.png)

1. Íme néhány lépés, amely a fenti csempék egyikét hozza létre: a "az érzékelő 11. küszöbértéke és az 48,26-es küszöb" csempe:
   
   * Kattintson a bal oldali panel adatkészletek szakaszának adatkészlet **aircraftmonitor** elemére.
   * Kattintson a **vonal diagram** ikonjára.
   * Kattintson a **feldolgozott** gombra a **mezők** ablaktáblán, hogy az a "tengely" alatt látható legyen a **vizualizációk** ablaktáblán.
   * Kattintson a "S11" és a "S11\_riasztás" elemre, hogy mindkettő megjelenjen az "értékek" alatt. Kattintson a **S11** és **S11\_riasztás**melletti kis nyílra, és módosítsa az "összeg" kifejezést az "átlag" értékre.
   * Kattintson a **Save (Mentés** ) gombra a lap tetején, és nevezze el a "aircraftmonitor" jelentést. A "aircraftmonitor" nevű jelentés a bal oldali **navigátor** ablaktáblájának **jelentések** szakaszában jelenik meg.
   * Kattintson a **rögzítési vizualizáció** ikonra a diagram jobb felső sarkában. A "Rögzítés az irányítópulton" ablak előfordulhat, hogy jelennek meg, hogy válasszon ki egy irányítópultot. Válassza ki a "Prediktív karbantartási bemutató", majd kattintson a "Rögzítés."
   * Vigye az egérmutatót erre a csempére az irányítópulton, kattintson a jobb felső sarokban található "szerkesztés" ikonra, hogy megváltoztassa a címét: "az érzékelő 11. küszöbértéke és a 48,26-es küszöb", az alcím pedig "a flotta átlaga az idő múlásával".

## <a name="delete-your-solution"></a>A megoldás törlése
Győződjön meg arról, hogy leállítsa az adatgenerálást amikor nem használja aktívan a megoldást, az adatgeneráló magasabb költségekkel jár. Törölje a megoldást, ha nem használ. A megoldás törlésekor a megoldás üzembe helyezésekor az előfizetésben kiépített összes összetevőt. A megoldás törléséhez kattintson a megoldás nevére a megoldás sablonjának bal oldali paneljén, majd kattintson a **Törlés**gombra.

## <a name="cost-estimation-tools"></a>Költségbecslés eszközök
A következő két eszközök segítségével jobban megismerheti az a teljes költség a prediktív karbantartás a légi és Űrközlekedés Megoldássablon előfizetésében futó részt érhetők el:

* [Microsoft Azure Cost kalkulátor eszköz (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure Cost kalkulátor eszköz (asztali)](https://www.microsoft.com/download/details.aspx?id=43376)

