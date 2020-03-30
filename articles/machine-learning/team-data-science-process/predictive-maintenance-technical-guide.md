---
title: Útmutató a repülőgépipar prediktív karbantartásához - Team Data Science Process
description: Technikai útmutató a megoldássablonhoz a repülőgépipar, a közművek és a szállítás prediktív karbantartásához.
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
ms.openlocfilehash: 0542106f70e96b6c2f63e8ca03d2532de191d365
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477170"
---
# <a name="technical-guide-to-the-solution-template-for-predictive-maintenance-in-aerospace"></a>Műszaki útmutató a repülőgépipari prediktív karbantartásmegoldás-sablonhoz

> [!Important]
> Ez a cikk elavult. A prediktív karbantartásról szóló vita az Aerospace-ben továbbra is releváns, de az aktuális információkért tekintse meg [a Megoldás áttekintése az üzleti közönségek számára című témakört.](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace)


A megoldássablonok célja, hogy felgyorsítsák az E2E demó létrehozásának folyamatát. Egy üzembe helyezett sablon rendelkezik az előfizetés szükséges összetevőket, majd létrehozza a köztük lévő kapcsolatokat. Azt is magok az adatfolyamat mintaadatokat egy adatgenerátor-alkalmazás, amely a megoldássablon üzembe helyezése után letölti és telepíti a helyi gépen. A generátorból származó adatok hidratálják az adatfolyamatot, és megkezdik a gépi tanulási előrejelzések generálását, amelyek ezután megjelenhetnek a Power BI irányítópultján.

A telepítési folyamat végigvezeti a megoldás hitelesítő adatainak beállításához szükséges lépéseken. Győződjön meg arról, hogy rögzíti a hitelesítő adatokat, például a megoldás nevét, felhasználónevét és jelszavát, amelyet a központi telepítés során ad meg. 


A cikk céljai a következők:
- Írja le az előfizetésben kiépített referenciaarchitektúrát és összetevőket.
- Mutassa be, hogyan cserélheti le a mintaadatokat a saját adataira. 
- A megoldássablon módosításának bemutatása.  

## <a name="overview"></a>Áttekintés
![Prediktív karbantartási architektúra](./media/predictive-maintenance-technical-guide/predictive-maintenance-architecture.png)

A megoldás üzembe helyezésekor aktiválja az Azure-szolgáltatásokat, beleértve az Event Hubot, a Stream Analyticset, a HDInsightot, a Data Factoryt és a Machine Learninget. Az architektúradiagram bemutatja, hogyan épül fel a prediktív karbantartás a repülőgépipari megoldássablonhoz. Ezeket a szolgáltatásokat az Azure Portalon megvizsgálhatja, ha rájuk kattint a megoldás üzembe helyezésével létrehozott megoldássablon-diagramban (kivéve a HDInsight-ot, amely igény szerint van kiépítve, amikor a kapcsolódó folyamattevékenységek futtatásához szükségesek, és ezt követően törölni kell).
Töltse le [a diagram teljes méretű változatát.](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png)

A következő szakaszok az oldat részeket ismertetik.

## <a name="data-source-and-ingestion"></a>Adatforrás és betöltés
### <a name="synthetic-data-source"></a>Szintetikus adatforrás
Ehhez a sablonhoz a használt adatforrás egy letöltött asztali alkalmazásból jön létre, amelyet a sikeres telepítés után helyileg futtat.

Az alkalmazás letöltésére és telepítésére vonatkozó utasítások megkereséséhez válassza ki az első csomópontot, a Prediktív karbantartási adatgenerátort a megoldássablon-diagramon. Az utasítások a Tulajdonságok sávon találhatók. Ez az alkalmazás az [Azure Event Hub](#azure-event-hub) szolgáltatás adatpontokat vagy eseményeket, a megoldás folyamatának többi részében használt. Ez az adatforrás a [NASA adattárából](https://c3.nasa.gov/dashlink/resources/139/) származó, a Turbofan Engine Degradation Simulation Data Set segítségével nyilvánosan elérhető adatokból [származik.](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan)

Az eseménygenerálási alkalmazás csak akkor feltölti az Azure Event Hubot, amikor az a számítógépen hajt végre.  

### <a name="azure-event-hub"></a>Azure Event Hub  
Az [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) szolgáltatás a szintetikus adatforrás által biztosított bemenet címzettje.

## <a name="data-preparation-and-analysis"></a>Adatok előkészítése és elemzése  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Az [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) használatával közel valós idejű elemzéseket biztosítaz Azure Event [Hub](#azure-event-hub) szolgáltatás bemeneti adatfolyamáról. Ezután közzéteheti az eredményeket egy Power BI-irányítópulton, valamint archiválhatja az összes nyers bejövő eseményt az [Azure Storage szolgáltatásba](https://azure.microsoft.com/services/storage/) az [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) szolgáltatás későbbi feldolgozásához. [Power BI](https://powerbi.microsoft.com)

### <a name="hdinsight-custom-aggregation"></a>HDInsight egyéni összesítés
Futtathatja [a Hive-parancsfájlokat](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (az Azure Data Factory által vezénylve) a HDInsight használatával, hogy aggreggregációkat biztosítson az Azure Stream Analytics-erőforrás használatával archivált nyers eseményekről.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Előrejelzéseket készíthet egy adott repülőgép-motor fennmaradó hasznos élettartamáról (RUL) az [Azure Machine Learning Service](https://azure.microsoft.com/services/machine-learning/) (az Azure Data Factory által hangszerelt) bemenetei alapján. 

## <a name="data-publishing"></a>Adatközzététel
### <a name="azure-sql-database"></a>Azure SQL Database
Az [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) használatával tárolhatja az Azure Machine Learning által kapott előrejelzéseket, amelyeket a Power [BI](https://powerbi.microsoft.com) irányítópultján használ fel.

## <a name="data-consumption"></a>Adathasználat
### <a name="power-bi"></a>Power BI
A [Power BI](https://powerbi.microsoft.com) segítségével megjelenítheti az Azure Stream [Analytics](https://azure.microsoft.com/services/stream-analytics/)által biztosított összesítéseket és riasztásokat tartalmazó irányítópultokat, valamint az Azure Machine [Learning](https://azure.microsoft.com/services/machine-learning/)használatával előállított RUL-előrejelzéseket. [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)

## <a name="how-to-bring-in-your-own-data"></a>Hogyan hozhatbe saját adatokat
Ez a szakasz bemutatja, hogyan hozhatja a saját adatait az Azure-ba, és milyen területeken kell módosítani az ebben az architektúrában hozott adatokat.

Nem valószínű, hogy az adatkészlet megegyezik a megoldássablonhoz használt [Turbofan Engine Degradation Simulation Data Set](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) által használt adatkészlet. Az adatok és a követelmények megértése alapvető fontosságú a sablon módosításához, hogy a saját adataival dolgozhasson. 

A következő szakaszok ismertetik a sablon azon részeit, amelyek új adatkészlet bevezetésekor módosításokat igényelnek.

### <a name="azure-event-hub"></a>Azure Event Hub
Az Azure Event Hub általános; adatok csv vagy JSON formátumban is feladhatók a hubra. Az Azure Event Hubon nem történik speciális feldolgozás, de fontos, hogy megértse a betáplált adatokat.

Ez a dokumentum nem ismerteti, hogyan kell bedolgozni az adatokat, de könnyen küldhet eseményeket vagy adatokat egy Azure Event Hub az Event Hub API-k használatával.

### <a name="azure-stream-analytics"></a><a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Az Azure Stream Analytics erőforrás használatával közel valós idejű elemzéseket biztosíthat az adatfolyamokból való olvasással és az adatok tetszőleges számú forrásra történő kiközvetítésével.

A prediktív karbantartás a aerospace solution template, az Azure Stream Analytics-lekérdezés négy allekérdezések, minden lekérdezés takar események az Azure Event Hub szolgáltatás, kimenetek négy különböző helyekre kimenetek. Ezek a kimenetek három Power BI-adatkészletből és egy Azure Storage-helyből állnak.

Az Azure Stream Analytics-lekérdezés a következő kérhető:

* Csatlakozás az Azure Portalhoz
* A streamanalytics-feladatok ![Stream](./media/predictive-maintenance-technical-guide/icon-stream-analytics.png) Analytics ikonjának megkeresése, amely a megoldás telepítésekor jött létre *(például* **maintenancesa02asapbi** és **maintenancesa02asablob** a prediktív karbantartási megoldáshoz)
* Kiválasztása
  
  * ***BEMENETEK*** a lekérdezésbemenet megtekintéséhez
  * ***QUERY*** a lekérdezés megtekintéséhez maga
  * ***KIMENETEK*** a különböző kimenetek megtekintéséhez

Az Azure Stream Analytics-lekérdezés felépítésével kapcsolatos információk az MSDN [Stream Analytics lekérdezési hivatkozásában](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) találhatók.

Ebben a megoldásban a lekérdezések három adatkészletet adnak ki a bejövő adatfolyamról szóló közel valós idejű elemzési információkkal egy Power BI-irányítópultra, amely et a megoldássablon részeként biztosítják. Mivel a bejövő adatformátumot implicit ismeretekkel rendelkezik, ezeket a lekérdezéseket az adatformátum nak megfelelően módosítani kell.

A lekérdezés a második Stream Analytics-feladat **maintenancesa02asablob** egyszerűen az összes [Event Hub-események](https://azure.microsoft.com/services/event-hubs/) az [Azure Storage-ba,](https://azure.microsoft.com/services/storage/) és ezért nem igényel változtatást, függetlenül az adatformátum, mint a teljes esemény információkat a tárolóba továbbítja.

### <a name="azure-data-factory"></a>Azure Data Factory
Az [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) szolgáltatás vezényli az adatok mozgását és feldolgozását. A prediktív karbantartás a aerospace solution sablon, az adatgyár áll három folyamatok, amelyek [mozognak](../../data-factory/concepts-pipelines-activities.md) és feldolgozzák az adatokat különböző technológiák használatával.  Az adat-előállító eléréséhez nyissa meg a Data Factory csomópont alján a megoldás telepítésével létrehozott megoldássablon-diagram. Az adatkészletek alatt az adat-előállító üzembe helyezése az adatgenerátor indítása előtt történt hibák miatt. Ezek a hibák figyelmen kívül hagyhatók, és nem akadályozzák meg az adatgyár működését.

![Adatfeldolgozó adatkészlethibái](./media/predictive-maintenance-technical-guide/data-factory-dataset-error.png)

Ez a szakasz ismerteti a szükséges [folyamatokat és tevékenységeket](../../data-factory/concepts-pipelines-activities.md) az [Azure Data Factory.](https://azure.microsoft.com/documentation/services/data-factory/) Az alábbiakban a megoldás diagramnézetét találja.

![Azure Data Factory](./media/predictive-maintenance-technical-guide/azure-data-factory.png)

A gyár két folyamata [hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) parancsfájlokat tartalmaz az adatok particionálásához és összesítéséhez. Ha megjegyezte, a parancsfájlok találhatók az [Azure Storage-fiók](https://azure.microsoft.com/services/storage/) a telepítés során létrehozott. A\\\\helyük: maintenancesascript\\\\parancsfájl\\ \\ struktúra (vagy https://[A megoldás neve].blob.core.windows.net/maintenancesascript).

Az [Azure Stream](#azure-stream-analytics-1) Analytics-lekérdezésekhez hasonlóan a [Hive-parancsfájlok](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) implicit ismeretekkel rendelkeznek a bejövő adatformátumról, és az adatformátum alapján módosítani kell őket.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Ez [a folyamat](../../data-factory/concepts-pipelines-activities.md) egyetlen tevékenységet tartalmaz – egy [HDInsightHive-tevékenységet](../../data-factory/transform-data-using-hadoop-hive.md) egy [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) használatával, amely [egy Hive-parancsfájlt](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) futtat az [Azure Storage-ban](https://azure.microsoft.com/services/storage/) az Azure Stream [Analytics-feladat](https://azure.microsoft.com/services/stream-analytics/) során felhozott adatok particionálásához.

A particionálási feladat [Hive-parancsfájlja](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) ***az AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoring csővezeték*
Ez [a folyamat](../../data-factory/concepts-pipelines-activities.md) számos olyan tevékenységet tartalmaz, amelyek végeredménye az Azure Machine [Learning-kísérletből](https://azure.microsoft.com/services/machine-learning/) származó, ehhez a megoldássablonhoz társított pontozott előrejelzések.

Az ide tartozó tevékenységek a következők:

* [HDInsightHive-tevékenység](../../data-factory/transform-data-using-hadoop-hive.md) egy [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) használatával, amely egy [Hive-parancsfájlt](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) futtat az [Azure Machine Learning-kísérlethez](https://azure.microsoft.com/services/machine-learning/) szükséges összesítések és szolgáltatástervezés végrehajtásához.
  A particionálási feladat [Hive-parancsfájlja](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) ***prepareMLInput.hql***.
* [Másolja](https://msdn.microsoft.com/library/azure/dn835035.aspx) azokat a tevékenységet, amely a [HDInsightHive-tevékenység](../../data-factory/transform-data-using-hadoop-hive.md) eredményeit egyetlen [Azure Storage-blobba](https://azure.microsoft.com/services/storage/) helyezi át, amelyet az [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) tevékenység elér.
* [Az AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) tevékenység meghívja az [Azure Machine Learning-kísérletet,](https://azure.microsoft.com/services/machine-learning/) és az eredmények egyetlen [Azure Storage-blobban](https://azure.microsoft.com/services/storage/) kerülnek be.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Ez [a folyamat](../../data-factory/concepts-pipelines-activities.md) egyetlen tevékenységet tartalmaz – egy [másolási](https://msdn.microsoft.com/library/azure/dn835035.aspx) tevékenységet, amely az ***MlScoringPipeline-ból az MLScoringPipeline-ból*** az [Azure SQL Database-be](https://azure.microsoft.com/services/sql-database/) helyezi át az [eredményeket](#azure-machine-learning) a megoldássablon telepítésének részeként.

### <a name="azure-machine-learning"></a>Azure Machine Learning
A megoldássablonhoz használt [Azure Machine Learning-kísérlet](https://azure.microsoft.com/services/machine-learning/) biztosítja a repülőgép-hajtómű hátralévő hasznos élettartamát (RUL). A kísérlet a felhasznált adatkészletre jellemző, és a bevitt adatokra jellemző módosítást vagy cserét igényel.

## <a name="monitor-progress"></a>Folyamat figyelése
Az adatgenerátor elindítása után a folyamat elkezd kiszáradni, és a megoldás különböző összetevői működésbe lépnek az adat-előállító által kiadott parancsok at követően. A folyamat kétféleképpen figyelheti a folyamatot.

* A Stream Analytics-feladatok egyike írja a nyers bejövő adatokat a blob storage.One of the Stream Analytics jobs writes the raw incoming data to blob storage. Ha a megoldás Blob Storage összetevőjére kattint a megoldás sikeres üzembe helyezésének képernyőjén, majd a jobb oldali panelen a Megnyitás gombra kattint, akkor az [Azure Portalra](https://portal.azure.com/)kerül. Ha ott van, kattintson a Blobs. A következő panelen a tárolók listája látható. Kattintson a **maintenancesadata lehetőségre.** A következő panelen található a **rawdata** mappa. A rawdata mappán belül olyan mappák találhatók, amelyek neve például hour=17 és hour=18. Ezeknek a mappáknak a jelenléte azt jelzi, hogy nyers adatok jönnek létre a számítógépen, és a blob storage tárolja. Ezekben a mappákban a véges méretű csv-fájloknak mb-ban kell megtekinteniük.
* A folyamat utolsó lépése az adatok írása (például a gépi tanulásból származó előrejelzések) az SQL Database-be. Előfordulhat, hogy legfeljebb három órát kell várnia, hogy az adatok megjelenjenek az SQL Database-ben. Az SQL-adatbázisban rendelkezésre álló adatok figyelésének egyik módja az [Azure Portalon](https://portal.azure.com/)keresztül érhető el. A bal oldali panelen ![keresse](./media/predictive-maintenance-technical-guide/icon-SQL-databases.png) meg az SQL DATABASES SQL ikonját, és kattintson rá. Ezután keresse meg az **adatbázispmaintenancedb** és kattintson rá. A következő oldalon az alján, kattintson kezelése.
   
    ![Kezelés ikon](./media/predictive-maintenance-technical-guide/icon-manage.png)
   
    Itt az Új lekérdezés és a lekérdezés gombra kattinthat a sorok számára (például válassza ki a count(*) elemet a PMResult-ből). Az adatbázis növekedésével a tábla sorainak száma növekedni fog.

## <a name="power-bi-dashboard"></a>Power BI-irányítópult

Állítson be egy Power BI-irányítópultot az Azure Stream Analytics-adatok (elérési út) és az Azure gépi tanulás (hideg út) kötegelt előrejelzési eredményeinek megjelenítéséhez.

### <a name="set-up-the-cold-path-dashboard"></a>A hidegút-irányítópult beállítása
A hideg út adatfolyamatában a cél az, hogy az egyes repülőgép-hajtóművek prediktív RUL-ját (fennmaradó hasznos élettartamát) a repülés (ciklus) befejezése után kapja meg. Az előrejelzési eredmény 3 óránként frissül, hogy megjósolja azokat a repülőgép-hajtóműveket, amelyek az elmúlt 3 órában befejezték a repülést.

A Power BI adatforrásként csatlakozik egy Azure SQL-adatbázishoz, ahol az előrejelzési eredmények tárolódnak. 

Megjegyzés: 
1.    A megoldás üzembe helyezésekor egy előrejelzés jelenik meg az adatbázisban 3 órán belül. A Generator letöltéshez kapó pbix fájl tartalmaz néhány magadatot, így azonnal létrehozhatja a Power BI irányítópultját. 
2.    Ebben a lépésben az előfeltétele az ingyenes szoftver Power BI desktop letöltésének és [telepítésének.](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

A következő lépések bemutatják, hogyan lehet csatlakoztatni a pbix-fájlt az SQL-adatbázishoz, amely a megoldás üzembe helyezésekor adatokat (például előrejelzési eredményeket) tartalmaz a vizualizációhoz.

1. Az adatbázis hitelesítő adatainak beírása.
   
   A következő lépésekre való áttérés előtt szüksége lesz **az adatbázis-kiszolgáló nevére, az adatbázis nevére, felhasználónevére és jelszavára.** Az alábbi lépésekkel elvezetheti Önt a keresésükhöz.
   
   * Miután **az "Azure SQL Database"** a megoldássablon-diagramon zöldre vált, kattintson rá, majd kattintson a **"Megnyitás"** gombra.
   * Megjelenik egy új böngészőlap/ablak, amely az Azure Portal lapot jeleníti meg. Kattintson az **"Erőforráscsoportok"** gombra a bal oldali panelen.
   * Válassza ki a megoldás üzembe helyezéséhez használt előfizetést, majd válassza a **"YourSolutionName\_ResourceGroup"** lehetőséget.
   * Az új előugró panelen ![kattintson az SQL ikonra](./media/predictive-maintenance-technical-guide/icon-sql.png) az adatbázis eléréséhez. Az adatbázis neve ezen ikon mellett található (például **"pmaintenancedb"),** és az **adatbázis-kiszolgáló neve** a Kiszolgáló név tulajdonság alatt található, és a **YourSolutionName.database.windows.net**hasonlóan kell kinéznie.
   * Az adatbázis **felhasználóneve** és **jelszava** megegyezik a megoldás telepítése során korábban rögzített felhasználónévvel és jelszóval.
2. Frissítse a hidegelérési út jelentésfájljának adatforrását a Power BI Desktop segítségével.
   
   * Abban a mappában, ahol letöltötte és kicsomagolta a Generator fájlt, kattintson duplán a **\\PowerBI PredictiveMaintenanceAerospace.pbix** fájlra. Ha a fájl megnyitásakor figyelmeztető üzenetek jelennek meg, hagyja figyelmen kívül azokat. A fájl tetején kattintson a **"Lekérdezések szerkesztése" gombra.**
     
     ![Lekérdezések szerkesztése](./media/predictive-maintenance-technical-guide/edit-queries.png)
   * Két tábla jelenik meg: **RemainingUsefulLife** és **PMResult**. Jelölje ki az ![első táblát, és kattintson](./media/predictive-maintenance-technical-guide/icon-query-settings.png) a **"Forrás"** melletti Lekérdezésbeállítások ikonra a jobb oldali **"Lekérdezési beállítások"** panel **"Alkalmazott lépések"** elemében. Hagyja figyelmen kívül a megjelenő figyelmeztető üzeneteket.
   * Az előugró ablakban cserélje le a **"Kiszolgáló"** és az **"Adatbázis"** lehetőséget a saját kiszolgáló- és adatbázisnevére, majd kattintson az **"OK"** gombra. A kiszolgálónévhez adja meg az 1433 -os portot (**YourSolutionName.database.windows.net, 1433**). Hagyja az Adatbázis mezőt **pmaintenancedb**néven. Hagyja figyelmen kívül a képernyőn megjelenő figyelmeztető üzeneteket.
   * A következő kiugró ablakban két lehetőség látható a bal oldali ablaktáblán (**Windows** és **Adatbázis**). Kattintson az **"Adatbázis"** elemre, töltse ki a **"Felhasználónév"** és a **"Jelszó"** (a felhasználónevet és a jelszót, amelyet a megoldás első üzembe helyezésekor adott meg, és létrehozott egy Azure SQL Database-t). A ***Válassza ki, hogy melyik szintre alkalmazza ezeket a beállításokat,*** ellenőrizze az adatbázis szint beállítását. Ezután kattintson **a "Csatlakozás" gombra.**
   * Kattintson a második táblázat **PMResult** majd ![kattintson a Navigációs ikon](./media/predictive-maintenance-technical-guide/icon-navigation.png) mellett **"Forrás"** a **"Alkalmazott lépések"** a jobb oldali **"Lekérdezési beállítások"** panelen, és frissítse a szerver és az adatbázis nevét, mint a fenti lépéseket, és kattintson az OK gombra.
   * Ha visszavezeted az előző oldalra, zárja be az ablakot. Egy üzenet jelenik meg - kattintson **az Alkalmaz gombra.** Végül kattintson a **Mentés** gombra a módosítások mentéséhez. A Power BI-fájl most már kapcsolatot létesített a kiszolgálóval. Ha a vizualizációk üresek, törölje a vizualizációkon lévő kijelöléseket az összes adat megjelenítéséhez a jelmagyarázatok jobb felső sarkában lévő radír ikonra kattintva. A frissítés gombbal a vizualizációk új adatait jelenítheti meg. Kezdetben csak a vizualizációk on-in jelennek meg a magadatokat, mivel az adatgyár 3 óránként frissül. 3 óra elteltével az adatok frissítésekor a vizualizációkban megjelennek az új előrejelzések.
3. (Nem kötelező) A hidegút irányítópultjának közzététele a [Power BI online szolgáltatásában.](https://www.powerbi.com/) Ehhez a lépéshez Power BI-fiókra (vagy Office 365-fiókra) van szükség.
   
   * Kattintson **a "Közzététel"** gombra, majd néhány másodperccel később megjelenik egy ablak, amelyen a "Közzététel a Power BI-ban sikeres!" felirat látható. zöld pipával. Kattintson az alábbi hivatkozásra: "Open PredictiveMaintenanceAerospace.pbix in Power BI". Részletes útmutatást a [Közzététel a Power BI Desktopról című témakörben talál.](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop)
   * Új irányítópult létrehozásához kattintson a **+** bal oldali **ablaktábla Irányítópultok** szakasza melletti jelre. Adja meg az új irányítópult "Prediktív karbantartási bemutatója" nevet.
   * Miután megnyitotta a ![jelentést,](./media/predictive-maintenance-technical-guide/icon-pin.png) a PIN-ikonra kattintva rögzítheti az összes vizualizációt az irányítópulton. Részletes útmutatást a [Csempe rögzítése egy Power BI-irányítópultra jelentésből című témakörben talál.](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report)
     Lépjen az irányítópult oldalára, és módosítsa a vizualizációk méretét és helyét, és módosítsa a címüket. A csempék szerkesztésével kapcsolatos részletes útmutatásról a [Csempe szerkesztése – átméretezés, áthelyezés, átnevezés, rögzítés, törlés, hivatkozás hozzáadása](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename)című témakörben talál részletes útmutatást. Íme egy példa irányítópult néhány hideg út vizualizációk rögzített rá.  Attól függően, hogy mennyi ideig futtatja az adatgenerátort, a képi megjelenítések száma eltérő lehet.
     <br/>
     ![Végső nézet](./media/predictive-maintenance-technical-guide/final-view.png)
     <br/>
   * Az adatok frissítésének ütemezéséhez vigye az egérmutatót a **PrediktívKarbantartásAerospace** adatkészlet fölé, kattintson az Ellipszis ikonra, ![](./media/predictive-maintenance-technical-guide/icon-elipsis.png) és válassza a Frissítés **ütemezése parancsot.**
     <br/>
     > [!NOTE]
     > Ha figyelmeztető üzenet jelenik meg, kattintson a **Hitelesítő adatok szerkesztése gombra,** és győződjön meg arról, hogy az adatbázis hitelesítő adatai megegyeznek az 1.
     <br/>
     ![Frissítés ütemezése](./media/predictive-maintenance-technical-guide/schedule-refresh.png)
     <br/>
   * Bontsa ki az **Ütemezés frissítése szakaszt.** Kapcsolja be az "adatok naprakészen tartása" bekapcsolást.
     <br/>
   * Ütemezze a frissítést az igényeinek megfelelően. További információt az [Adatfrissítés a Power BI-ban című témakörben talál.](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi)

### <a name="setup-hot-path-dashboard"></a>Elérési út beállítása
Az alábbi lépések bemutatják, hogyan jelenítheti meg a megoldás üzembe helyezésekor létrehozott Stream Analytics-feladatok ból származó adatkimeneteket. A következő lépések végrehajtásához [Power BI online](https://www.powerbi.com/) fiók szükséges. Ha nem rendelkezik fiókkal, [létrehozhat egyet.](https://powerbi.microsoft.com/pricing)

1. Power BI-kimenet hozzáadása az Azure Stream Analytics (ASA) szolgáltatásban.
   
   * Az Azure Stream [Analytics & Power BI: A streamelési adatok valós idejű láthatóságát szolgáló elemzési irányítópult on-k](../../stream-analytics/stream-analytics-power-bi-dashboard.md) ban található utasításokat kell követnie, hogy az Azure Stream Analytics-feladat kimenetét Power BI-irányítópultként állítsa be.
   * Az ASA-lekérdezés három kimenettel rendelkezik , amelyek a **következők: aircraftmonitor**, **aircraftalert**és **flightsbyhour**. Megtekintheti a lekérdezést a lekérdezés fülre kattintva. Az első kimenet **(aircraftmonitor)** hozzáadásakor győződjön meg arról, hogy a **Kimeneti alias**, **az adatkészlet neve** és a **táblanév** megegyezik (**aircraftmonitor**). Ismételje meg a lépéseket, hogy adjunk kimenetek **aircraftalert**, és **a flightsbyhour**. Miután hozzáadta mindhárom kimeneti táblát, és elindította az ASA-feladatot, egy megerősítő üzenetet kell kapnia ("A Stream Analytics-feladat karbantartásának indítása sikeresen sikerült").
2. Bejelentkezés a [Power BI online verzióba](https://www.powerbi.com)
   
   * A My Workspace bal oldali paneladatkészletek szakaszában meg kell jelennie a ***DATASET-neveknek,*** a **aircraftmonitornak**, a **aircraftalert**-nek és a **flightsbyhour-nak.** Ez az azure Stream Analytics az előző lépésben leadott streamelési adatok. Előfordulhat, hogy a mögötte lévő SQL-lekérdezés jellege miatt előfordulhat, hogy a **flightsbyhour nem** jelenik meg a másik két adatkészlettel egy időben. Azonban egy óra múlva meg kell jelennie.
   * Győződjön meg arról, hogy a ***Képimegjelenítések*** ablaktábla meg van nyitva, és a képernyő jobb oldalán látható.
3. Miután az adatok beáramlottak a Power BI-ba, elkezdheti vizualizációs az adatfolyam-adatokat. Az alábbiakban egy példa irányítópult néhány forró elérési út vizualizációk rögzített rá. A megfelelő adatkészletek alapján más irányítópult-csempéket is létrehozhat. Attól függően, hogy mennyi ideig futtatja az adatgenerátort, a képi megjelenítések száma eltérő lehet.

    ![Irányítópult nézet](media/predictive-maintenance-technical-guide/dashboard-view.png)

1. Íme néhány lépés a fenti csempék egyikének létrehozásához – a "Flottanézet az érzékelő 11 vs. Threshold 48.26" csempéhez:
   
   * Kattintson az adatkészlet **aircraftmonitor** a bal oldali panelen Adatkészletek szakaszban.
   * Kattintson a **Vonaldiagram** ikonra.
   * Kattintson a **Mezők** ablaktáblán a **Feldolgozott** gombra, hogy az megjelenjön a **Képimegjelenítések** ablaktábla "Tengely" területén.
   * Kattintson az "s11"\_és az "s11 riasztás" gombra, hogy mindkettő megjelenjen az "Értékek" alatt. Kattintson az **s11** és **\_s11 riasztás**melletti kis nyílra, és módosítsa az "Összeg" szót "Átlagos" értékre.
   * Kattintson a **mentés gombra** a tetején, és nevezze el a jelentést "aircraftmonitor" néven. A "aircraftmonitor" nevű jelentés a bal oldali **Navigátor** ablaktábla **Jelentések** szakaszában látható.
   * Kattintson a **vonaldiagram** jobb felső sarkában található Vizuális rögzítés ikonra. Megjelenhetnek egy "Rögzítés az irányítópultra" ablakban, amelyen kiválaszthatja az irányítópultot. Válassza a "Prediktív karbantartás demó" lehetőséget, majd kattintson a "Rögzítés" gombra.
   * Vigye az egeret a csempére az irányítópulton, kattintson a jobb felső sarokban található "Szerkesztés" ikonra, és a cím "Flottanézet az érzékelő 11 vs. Threshold 48.26" értékre változik, a feliratpedig "Átlagos flotta idővel" értékre.

## <a name="delete-your-solution"></a>A megoldás törlése
Győződjön meg arról, hogy állítsa le az adatgenerátor, ha nem aktívan használja a megoldást, mint az adatgenerátor futtatása magasabb költségeket. Törölje a megoldást, ha nem használja. A megoldás törlése törli az előfizetésben a megoldás üzembe helyezésekor kiépített összes összetevőt. A megoldás törléséhez kattintson a megoldás nevére a megoldássablon bal oldali paneljén, majd kattintson a **Törlés gombra.**

## <a name="cost-estimation-tools"></a>Költségbecslési eszközök
A következő két eszköz segít jobban megérteni az előfizetésben a prediktív karbantartás a repülőgépipari megoldássablon futtatásának teljes költségét:

* [Microsoft Azure költségbecslő eszköz (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure költségbecslő eszköz (asztali)](https://www.microsoft.com/download/details.aspx?id=43376)

