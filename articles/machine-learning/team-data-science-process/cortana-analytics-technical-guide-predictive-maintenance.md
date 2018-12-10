---
title: Útmutató a prediktív karbantartás a légi közlekedésben – a csoportos adatelemzési folyamat
description: Technikai útmutató a Microsoft Cortana Intelligence-Megoldássablon prediktív karbantartás a légi közlekedésben, segédprogramok és a szállítási.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 03/15/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: d7acb24a6fef0435d59e5a07f5312f1e6368fe52
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140183"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace"></a>Technikai útmutató a Cortana Intelligence Megoldássablon prediktív karbantartás a légi közlekedésben

>[!Important]
Ez a cikk elavult. A prediktív karbantartás a légi közlekedésben szóló vita továbbra is szükséges, de információk, tekintse meg [megoldás áttekintése üzleti célközönség](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Gyorsítsa fel a folyamat létrehozásának felül a Cortana Intelligence Suite-E2E bemutató megoldássablonok lettek kialakítva. Egy telepített sablon látja el az előfizetés szükséges a Cortana Intelligence-összetevőkkel, és ezt követően felépíti az ezek közötti kapcsolatok. Azt is feltölti a adatfolyamat adatok-készítő alkalmazás, amely letölti és telepíti a helyi gépen a megoldássablon telepítése után a mintaadatokkal. A generátor származó adatok hydrates a adatfolyamat és kezdő létrehozása a machine learning előrejelzéseket, amelyek megjeleníthetők a Power BI-irányítópulton.

Az üzembe helyezési folyamat végigvezeti a megoldás hitelesítő adatok beállításához számos lépést. Győződjön meg arról, jegyezze fel a hitelesítő adatokat, például a megoldás nevét, a felhasználónevet és jelszót, amely az üzembe helyezés során. 


Ez a cikk célja a következők:
- Írja le a referencia-architektúra és összetevők az előfizetésében.
- Cserélje le a mintaadatokat a saját adatok bemutatásához. 
- A megoldássablon módosítása megjelenítése.  

> [!TIP]
> Töltse le, és nyomtassa ki a [PDF-verzió, ez a cikk](https://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).
> 
> 

## <a name="overview"></a>Áttekintés
![Prediktív karbantartás architektúra](./media/cortana-analytics-technical-guide-predictive-maintenance/predictive-maintenance-architecture.png)

A megoldás üzembe helyezésekor, aktiválja az Azure-szolgáltatásokhoz (beleértve az Event Hub, a Stream Analytics, a HDInsight, a Data Factory és a Machine Learning), a Cortana Analytics csomag belül. Architektúradiagram bemutatja, hogyan a karbantartás a légi és Űrközlekedés Megoldássablon jön létre. Ezek a szolgáltatások az Azure Portalon létrehozott (kivéve a HDInsight, amely igény szerint van kiépítve, ha a kapcsolódó folyamat tevékenységek futtatásához szükséges, és a megoldás üzembe helyezése sablon megoldásdiagramon kattintva megvizsgálhatja törli ezt követően).
Töltse le a [a diagram teljes méretű változatát](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

A következő szakaszok ismertetik a megoldás részei.

## <a name="data-source-and-ingestion"></a>Adatforrás és feldolgozó
### <a name="synthetic-data-source"></a>Szintetikus adatforrás
Ehhez a sablonhoz használt adatforrás egy asztali alkalmazás, töltse le és futtassa helyileg a sikeres telepítést követően a jön létre.

Töltse le és telepítse az alkalmazás vonatkozó utasításokat talál, jelölje be az első fürtcsomópont, prediktív karbantartás Adatgenerátor, a megoldás sablon diagramon. A Tulajdonságok sávon található utasításokat. Ez az alkalmazás-csatornák a [Azure Event Hub](#azure-event-hub) , vagy eseményekként, használja a megoldás folyamatának többi szolgáltatáshoz. Ez az adatforrás van származtatva nyilvánosan elérhető adataiból származnak az [NASA adattár](https://c3.nasa.gov/dashlink/resources/139/) használatával a [Turbofan Engine Degradation Simulation Data Set](http://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Az esemény generálása alkalmazás feltöltése az Azure Event Hubs, csak azt a számítógépen végrehajtása közben.  

### <a name="azure-event-hub"></a>Azure-eseményközpont  
A [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) szolgáltatás a címzett, a bemenet a szintetikus adatforrás által biztosított.

## <a name="data-preparation-and-analysis"></a>Adat-előkészítési és -elemzés  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Használat [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) érkező bemeneti Stream közel valós idejű, a [Azure Event Hub](#azure-event-hub) szolgáltatás. Ezután közzéteheti az eredményeket a egy [Power BI](https://powerbi.microsoft.com) irányítópultot, valamint archív tárolási szint minden nyers bejövő eseményeket, hogy a [Azure Storage](https://azure.microsoft.com/services/storage/) későbbi feldolgozásra, amelyet a szolgáltatás a [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)szolgáltatás.

### <a name="hdinsight-custom-aggregation"></a>HDInsight egyéni összesítés
Futtassa [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (Azure Data Factory által előkészített) parancsfájlok használata a HDInsight használata az Azure Stream Analytics szolgáltatás archivált nyers események összesítését.

### <a name="azure-machine-learning"></a>Azure Machine Learning
A fennmaradó hasznos élettartamát (RUL) egy adott repülőgépmotor használatával a kapott bemeneti adatok az előrejelzéseket [Azure Machine Learning szolgáltatás](https://azure.microsoft.com/services/machine-learning/) (Azure Data Factory által előkészített). 

## <a name="data-publishing"></a>Adatok közzététele
### <a name="azure-sql-database"></a>Azure SQL Database
Használat [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) tárolására az Azure Machine Learning szolgáltatás, amely majd felhasznált fogadja az előrejelzés a [Power BI](https://powerbi.microsoft.com) irányítópultot.

## <a name="data-consumption"></a>Adathasználat
### <a name="power-bi"></a>Power BI
Használat [Power BI](https://powerbi.microsoft.com) összesítések és a riasztások által biztosított tartalmazó irányítópult megjelenítéséhez [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), valamint tárolt RUL előrejelzéseket [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) használatával keletkezett [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Hogyan lehet a saját adatok beolvasása
Ez a szakasz ismerteti, hogyan lehet a saját adatok importálása az Azure-ba, és milyen területeken módosítások szükségesek az adatok, ez az architektúra állapotba.

Nem valószínű, hogy az adatkészlet által használt adatkészlet megegyezik-e a [Turbofan Engine Degradation Simulation Data Set](http://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) Ez a megoldássablon használatos. Az adatok és a követelmények ismertetése alapvető fontosságúak a saját adatok sablon módosítása. 

A következő részekben bemutatjuk a sablon a részeit, amikor egy új adatkészlet bevezetett módosításokat igénylő.

### <a name="azure-event-hub"></a>Azure-eseményközpont
Az Azure Event Hubs szolgáltatás általános; adatok feladható a hub CSV vagy JSON formátumban. Speciális feldolgozás nem következik be az Azure Event Hubs, de fontos, hogy ismeri az adatkéréseket bele adatokat.

Ez a dokumentum nem ismerteti, hogy az adatokat, de egyszerűen küldhet eseményeket vagy adatokat egy Azure Event Hub, Event Hub API-val.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Az Azure Stream Analytics szolgáltatás segítségével közel valós idejű adatstreamek olvasása és ad ki adatokat a forrásból tetszőleges számú.

A karbantartás a légi és Űrközlekedés Megoldássablon, az Azure Stream Analytics-lekérdezés áll négy sub-lekérdezések minden lekérdezés felhasználni az eseményeket az Azure Event Hubs szolgáltatásból a kimenetek négy különböző helyekre. Ezeket a kimeneteket három Power BI-adatkészletek és a egy Azure Storage-helyen állnak.

Az Azure Stream Analytics-lekérdezés találhatók:

* Csatlakozás az Azure Portalon
* A Stream Analytics-feladatok megkeresése ![Stream Analytics ikonja](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-stream-analytics.png) , amely a megoldás telepítésekor generált (*például*, **maintenancesa02asapbi** és **maintenancesa02asablob** a prediktív karbantartási megoldás)
* Kiválasztása
  
  * ***BEMENETEK*** adjon meg a lekérdezés megtekintése
  * ***LEKÉRDEZÉS*** magát a lekérdezés megtekintése
  * ***OUTPUTS*** a különböző kimenetek megtekintése

Az Azure Stream Analytics lekérdezési konstrukció információ található a [Stream Analytics lekérdezési leírása](https://msdn.microsoft.com/library/azure/dn834998.aspx) az MSDN Webhelyén.

Ebben a megoldásban a lekérdezések kimeneti három adatkészletek a közel valós idejű elemzési információkat a bejövő adatfolyam, ez a megoldássablon részeként a Power BI-irányítópultra. Mivel a bejövő adatok formátumának beállításszolgáltató jellegéből fakadóan ismeri, ezeket a lekérdezéseket módosítani kell az adatok formátum alapján.

A lekérdezés a második Stream Analytics-feladatban **maintenancesa02asablob** egyszerűen kiírja az összes [Eseményközpont](https://azure.microsoft.com/services/event-hubs/) események [Azure Storage](https://azure.microsoft.com/services/storage/) , és ezért az nem az megváltoztatására igényel függetlenül attól, mint a teljes eseményt az adatformátum információk adatfolyamként történő tárolására.

### <a name="azure-data-factory"></a>Azure Data Factory
A [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) szolgáltatás koordinálja az adatáthelyezési és -adatok feldolgozása. A a tervezett karbantartás a légi és Űrközlekedés Megoldássablon, az adat-előállító készül három [folyamatok](../../data-factory/concepts-pipelines-activities.md) áthelyezése és feldolgozása az adatokat különböző technológiák használatával, amely a.  Az adat-előállító eléréséhez nyissa meg a Data Factory csomópontra való a megoldás üzembe helyezéséhez létrehozott sablon megoldásdiagramon alján. Az adatkészletek alatt hibák miatt a data factory folyamatban már telepítve vannak az adatgenerálást lett elindítva. Ezeket a hibákat figyelmen kívül lesz hagyva, és nem akadályozzák az adat-előállító működése

![Data Factory adatkészlet hibák](./media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Ez a szakasz ismerteti a szükséges [folyamatok és tevékenységek](../../data-factory/concepts-pipelines-activities.md) szerepel a [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Ez a megoldás diagram nézetet.

![Azure Data Factory](./media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Az e-előállító folyamatok két tartalmaz [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) particionálja, és az adatok összesítéséhez használt parancsfájlokat. Nincs feltüntetve, ha a parancsfájlok a találhatók-e a [Azure Storage](https://azure.microsoft.com/services/storage/) telepítés során létrehozott fiókot. A hely a következő: maintenancesascript\\\\parancsfájl\\\\hive\\ \\ (vagy https://[Your megoldás name].blob.core.windows.net/maintenancesascript).

Hasonló [Azure Stream Analytics](#azure-stream-analytics-1) lekérdezéseket, a [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) parancsfájlok beállításszolgáltató jellegéből fakadóan ismeri a bejövő adatok formátumának rendelkezik, és módosítani kell az adatok formátum alapján.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Ez [folyamat](../../data-factory/concepts-pipelines-activities.md) tartalmaz egyetlen tevékenység – egy [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) tevékenység használatával egy [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) futtat egy [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) az adatok particionálásához parancsfájlt helyezze [Azure Storage](https://azure.microsoft.com/services/storage/) során a [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) feladat.

A [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) parancsfájl esetében ez a particionálási feladat ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Ez [folyamat](../../data-factory/concepts-pipelines-activities.md) tartalmazza, amelynek végeredmény a pontozott előrejelzéseket több tevékenységet, a [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) Ez a megoldássablon társított kísérlet.

Foglalt tevékenységek a következők:

* [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) tevékenység használatával egy [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) futtat egy [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) összesítést végez, és a szükséges jellemzőkiemelés parancsfájl a [Azure Machine Tanulási](https://azure.microsoft.com/services/machine-learning/) kísérletezhet.
  A [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) parancsfájl esetében ez a particionálási feladat ***PrepareMLInput.hql***.
* [Másolás](https://msdn.microsoft.com/library/azure/dn835035.aspx) helyezi át az eredményeket a tevékenység a [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) egyetlen tevékenység [Azure Storage](https://azure.microsoft.com/services/storage/) blob által elért a [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) tevékenység.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) tevékenység hívások a [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) kísérletezzen velük, az eredmények egy put [Azure Storage](https://azure.microsoft.com/services/storage/) blob.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Ez [folyamat](../../data-factory/concepts-pipelines-activities.md) egyetlen tevékenységgel - tartalmaz egy [másolási](https://msdn.microsoft.com/library/azure/dn835035.aspx) tevékenységgel, amely eredményeit helyezi a [Azure Machine Learning](#azure-machine-learning) való kísérletezéshez a  ***MLScoringPipeline*** , a [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) kiépítve a megoldás sablon telepítésének részeként.

### <a name="azure-machine-learning"></a>Azure Machine Learning
A [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) kísérletezéshez felhasznált esetében ez a megoldássablon fennmaradó hasznos élettartama (RUL) felépítette egy repülőmotor biztosít. A kísérlet az adatkészletben használt, és módosítást igényel, vagy az adatok adott helyettesítő állapotba hozni.

További információ az Azure Machine Learning-kísérletek létrehozásának módja: [prediktív karbantartás: 1. lépés a 3-ból, adat-előkészítési és funkciófejlesztési feladatok](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>A figyelő folyamatban
Az Adatgenerátor indul el, miután dehidratált megkezdi a folyamat, és a megoldás összetevői, indítsa el a data factory által kiadott parancsok művelet következő be megkezdése. Kétféleképpen figyelheti a folyamatot.

1. A blob storage a Stream Analytics-feladatok egyik ír a nyers bejövő adatok. Ha kattint a képernyőn, ahonnan a megoldás a Blob Storage-összetevő, sikeresen üzembe helyezte a megoldást, és a jobb oldali panelen kattintson a Megnyitás, vesz igénybe, hogy a [az Azure portal](https://portal.azure.com/). Ezután kattintson a Blobok. A következő panelen láthatja a tárolók listáját. Kattintson a **maintenancesadata**. A következő panelen van a **rawdata** mappát. A rawdata mappában vannak a mappák nevekkel például óra = és 17 óra = 18. Ezek a mappák jelenlétét jelzi, hogy folyamatban van a nyers adatokat, a számítógép generált, és a blob storage-ban tárolt. Csv-fájlok (MB), a mappákat véges méretű kell megjelennie.
2. A folyamat utolsó lépéseként írja az adatokat (például előrejelzéseket a gépi tanulás), SQL Database. Előfordulhat, hogy várjon legfeljebb három óra, az SQL Database-ben jelennek meg adatok. Figyelheti, hogy mennyi adatot érhető el az SQL Database egyik lehetőség a [az Azure portal](https://portal.azure.com/). Bal oldali panelen keresse meg az SQL Database-ADATBÁZISOK ![SQL ikon](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-SQL-databases.png) , és kattintson rá. Keresse meg az adatbázis **pmaintenancedb** és kattintson rá. A következő oldalon a lap alján kattintson a kezelés
   
    ![Ikon kezelése](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-manage.png)
   
    Itt kattintson a új lekérdezés és a lekérdezés (például a PMResult válassza count(*)) sorok számát. Az adatbázis növekedésével növelje a tábla sorainak számát.

## <a name="power-bi-dashboard"></a>Power BI-irányítópult

Állítsa be a Power BI-irányítópulton jelenítheti meg az Azure Stream Analytics-adatok (gyakori elérésű útvonal) és az Azure machine learning (ritka elérésű útvonal) a batch-előrejelzési eredményeket.

### <a name="set-up-the-cold-path-dashboard"></a>A ritka elérésű útvonal irányítópult beállítása
A ritka elérésű útvonal adatok folyamat célja (ciklus) repülőjegyet befejeződésekor egyes repülőmotor prediktív RUL (fennmaradó hasznos élettartama) beolvasásához. Az előrejelzési eredmények, amelyek az elmúlt 3 órában repülőjegyet befejezte a repülőgép-hajtóművek előrejelzésére szolgáló 3 óránként frissül.

Power BI adatforrásként, az előrejelzési eredményeket tároló Azure SQL Database-adatbázishoz kapcsolódik. Megjegyzés: 1) a megoldás üzembe helyezése, az előrejelzési jelenik meg az adatbázisban 3 órát vehet.
A pbix-fájl, melyet a generátor letöltési néhány kezdőérték adatokat tartalmaz, így azonnal is létrehozhat a Power BI-irányítópulton. 2.) ebben a lépésben a szükséges előfeltétel, hogy töltse le és telepítse az ingyenes szoftvereket [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Az alábbi lépéseket leírja, hogyan csatlakozhat az SQL-adatbázis, amely a megoldás üzembe helyezése tartalmazó adatok (például előrejelzési eredményeket) megjelenítési időpontjában hoz létre a pbix-fájlt.

1. Az adatbázis hitelesítő adatainak lekérése.
   
   Szüksége lesz **adatbázis-kiszolgáló nevét, adatbázisnév, felhasználónév és jelszó** áthelyezése a következő lépések előtt. Az alábbiakban útmutató, hogyan találhatja meg azokat a lépéseket.
   
   * Egyszer **"Azure SQL Database"** a megoldássablon az ábra a színe zöldre vált, kattintson rá, és kattintson a **"Nyitott"**.
   * Egy új böngészőlapon vagy-ablakban, amely megjeleníti az Azure portal oldalán láthatja. Kattintson a **"Erőforráscsoportok"** bal oldali panelen.
   * Válassza ki az előfizetést, a megoldás üzembe helyezése használja, és válassza **"YourSolutionName\_ResourceGroup"**.
   * Az új pop ki panelen, kattintson a ![SQL ikon](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-sql.png) ikonra az adatbázis eléréséhez. Ez az ikon mellett van az adatbázis nevét (például **"pmaintenancedb"**), és a **adatbázis-kiszolgáló neve** a Server name tulajdonság van felsorolva, és hasonlóan kell kinéznie  **YourSoutionName.database.windows.net**.
   * Az adatbázis **felhasználónév** és **jelszó** ugyanaz, mint a felhasználónév és jelszó korábban rögzíti a megoldás üzembe helyezése során.
2. Frissítse az adatforrást a ritka elérésű útvonal fájlját a Power BI Desktopban.
   
   * A mappát, amelyben letöltött és a generátor fájl kicsomagolt, kattintson duplán a **Power bi\\PredictiveMaintenanceAerospace.pbix** fájlt. Ha a fájl megnyitásakor a figyelmeztető üzeneteket látja, figyelmen kívül hagyja őket. A fájl felső részén kattintson **lekérdezések szerkesztése**.
     
     ![Lekérdezések szerkesztése](./media/cortana-analytics-technical-guide-predictive-maintenance/edit-queries.png)
   * Látni fogja a két táblázat **RemainingUsefulLife** és **PMResult**. Válassza ki az első táblázat, és kattintson a ![lekérdezési beállítások ikon](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-query-settings.png) melletti **"Forrás"** alatt **alkalmazott lépések** a jobb oldali **"Lekérdezés beállításai"** panel. Hagyja figyelmen kívül a megjelenő figyelmeztető üzeneteket.
   * Cserélje le a kiugró ablakban **"Kiszolgáló"** és **"Adatbázis"** a saját kiszolgáló és az adatbázis nevét, és kattintson a **"OK"**. A kiszolgáló nevét, győződjön meg arról, adja meg az 1433-as porton (**YourSoutionName.database.windows.net, 1433-as**). Hagyja meg az adatbázis mező **pmaintenancedb**. Figyelmen kívül hagyhatja a figyelmeztetéseket, a képernyőn megjelenő.
   * A következő kiugró ablakban, a két lehetőség a bal oldali panelen láthatja (**Windows** és **adatbázis**). Kattintson a **"Adatbázis"**, töltse ki a **'Felhasználónév'** és **'Password'** (azt a felhasználónevet és jelszót, amikor először üzembe helyezte a megoldást és létrehozott egy Azure SQL-adatbázis). A ***válassza ki, melyik szintre legyenek érvényesek a beállítások***, ellenőrizze az adatbázis-szintű beállítás. Kattintson a **"Csatlakozás"**.
   * Kattintson a második táblázat **PMResult** kattintson ![navigációs ikonja](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-navigation.png) melletti **"Forrás"** alatt **alkalmazott lépések** a jobb oldali **"Lekérdezés beállításai"** panelen, és frissítse a kiszolgáló és az adatbázis nevének, mint a fenti lépéseket, és kattintson az OK gombra.
   * Miután, interaktív van az előző oldalra, zárja be az ablakot. Egy üzenet jelenik meg – kattintson **alkalmaz**. Végül kattintson a **mentése** gombra kattintva mentse a módosításokat. A Power BI-fájl most létesített kapcsolat a kiszolgálóval. Ha a Vizualizációk üres, győződjön meg arról, törölje a jelet a választott beállításokat, a Vizualizációk adatok megjelenítése a jelmagyarázat jobb felső sarkában a radír ikonra kattintva. A frissítés gomb használatával új adatokat tükrözik a vizualizációt. Kezdetben csak látható a kezdőérték-adatok a Vizualizációk az adat-előállító 3 óránként ütemezett módon. 3 óra után látni fogja a Vizualizációk megjelennek, amikor az adatok frissítése új előrejelzéseket.
3. (Nem kötelező) A ritka elérésű útvonal irányítópult közzététele [online a Power BI](http://www.powerbi.com/). Vegye figyelembe, hogy ezt a lépést kell egy Power BI-fiók (vagy Office 365-fiókkal).
   
   * Kattintson a **"Közzététel"** és néhány másodperc múlva megjelenik egy ablak, "Közzététel a Power BI sikeres!" megjelenítése egy zöld pipának. Kattintson az alábbi "Nyílt PredictiveMaintenanceAerospace.pbix a Power BI" hivatkozásra. Részletes utasításokért lásd: [közzététel a Power BI Desktopból](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Egy új irányítópult létrehozását: kattintson a **+** melletti jelentkezzen a **irányítópultok** szakaszban a bal oldali panelen. Adja meg az új irányítópult nevét "Prediktív karbantartási bemutató".
   * Amikor megnyitja a jelentést, kattintson a ![GOMBOSTŰ ikon](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-pin.png) rögzítése az irányítópulton a vizualizációkat. Részletes utasításokért lásd: [csempe rögzítése Power BI-irányítópultra egy jelentésből](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Nyissa meg az irányítópult-oldalon és a méret- és a Vizualizációk és szerkessze a címben. Részletes útmutatás a csempék szerkesztésére vonatkozó információért lásd: [szerkesztése – átméretezés, áthelyezés, átnevezés, PIN-kód, csempe törlés, hivatkozás hozzáadása](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Íme egy példa irányítópult egyes ritka elérésű útvonal vizualizációkkal rögzítve rajta.  Attól függően, hogy mennyi ideig futtatja az adatgenerátor a Vizualizációk a számok eltérő lehet.
     <br/>
     ![Végső megtekintése](./media/cortana-analytics-technical-guide-predictive-maintenance/final-view.png)
     <br/>
   * Frissítés ütemezéséhez az adatokat, az egérmutatóval rámutat a **PredictiveMaintenanceAerospace** adatkészletet, kattintson a ![jelölő három pontra ikon](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-elipsis.png) majd **frissítés ütemezése**.
     <br/>
     **Megjegyzés:** Ha megjelenik egy figyelmeztetés masszírozó, kattintson a **hitelesítő adatok szerkesztése** , és győződjön meg arról, hogy az adatbázis hitelesítő adatai ugyanazok, mint az 1. lépésben leírt.
     <br/>
     ![A frissítés ütemezése](./media/cortana-analytics-technical-guide-predictive-maintenance/schedule-refresh.png)
     <br/>
   * Bontsa ki a **frissítés ütemezése** szakaszban. Kapcsolja be "tartsa adatait naprakészen".
     <br/>
   * A frissítés ütemezése az igényei szerint. További információkért lásd: [Adatfrissítés a Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Gyakori elérésű útvonal irányítópult beállítása
A következő lépések végigvezetik hogyan jelenítheti meg a Stream Analytics-feladatok a megoldás üzembe helyezése során előállított kimeneti adatokat. A [online a Power BI](http://www.powerbi.com/) -fiók szükséges a következő lépésekkel. Ha nincs fiókja, akkor az [hozzon létre egyet](https://powerbi.microsoft.com/pricing).

1. Adja hozzá a Power BI-kimenet az Azure Stream Analytics (ASA).
   
   * Meg kell kövesse a [Azure Stream Analytics és a Power bi-ban: egy elemző irányítópultban a valós idejű streamelési adatok láthatóságának](../../stream-analytics/stream-analytics-power-bi-dashboard.md) állíthatja be a kimenet az Azure Stream Analytics-feladat, a Power BI-irányítópulton.
   * Az ASA-lekérdezésben található három kimenetek, amelyek **aircraftmonitor**, **aircraftalert**, és **flightsbyhour**. A lekérdezés a lekérdezés fülre kattintva tekintheti meg. Ezek a táblák mindegyike megfelelő, hozzá kell kimenet az ASA. Az első kimeneti hozzáadásakor (**aircraftmonitor**) Győződjön meg arról, a **kimeneti Alias**, **adatkészlet neve** és **táblanév** az azonos (vannak**aircraftmonitor**). Hozzáadásához ismételje meg a lépéseket a kimenetek **aircraftalert**, és **flightsbyhour**. Miután hozzáadta a három kimeneti táblák és az ASA-feladat elindult, egy megerősítő üzenetet ("Stream Analytics indítása feladat maintenancesa02asapbi sikeres") szerezheti be.
2. Jelentkezzen be [online a Power bi-ban](http://www.powerbi.com)
   
   * A bal oldali panelen a saját munkaterületen, az adatkészletek a szakasz a ***ADATKÉSZLET*** nevek **aircraftmonitor**, **aircraftalert**, és **flightsbyhour** meg kell jelennie. Ez az az előző lépésben az Azure Stream Analytics leküldve a streamelt adatokat. Az adatkészlet **flightsbyhour** előfordulhat, hogy nem jelennek meg a másik két adatkészletet az SQL-lekérdezést alapjául szolgáló mögöttes jellege miatt egyszerre. Azonban ennek meg kell jelennie egy óra elteltével.
   * Győződjön meg arról, hogy a ***Vizualizációk*** panel meg nyitva, és a képernyő jobb oldalán jelenik meg.
3. Miután a Power BI szolgáltatásba áramló adatokat, elkezdheti a streamelési adatok megjelenítése. Alább néhány gyakori elérésű útvonal Vizualizációk egy példa az irányítópulton rögzítve van rá. Más irányítópult-csempék megfelelő adathalmazok alapján létrehozhat. Attól függően, hogy mennyi ideig futtatja az adatgenerátor a Vizualizációk a számok eltérő lehet.

    ![Irányítópult nézet](media/cortana-analytics-technical-guide-predictive-maintenance/dashboard-view.png)

1. Az alábbiakban néhány lépését a hozzon létre egyet a fenti – csempék "11. érzékelő és a flotta nézetét. Küszöbérték 48.26" csempére:
   
   * Kattintson az adatkészlet **aircraftmonitor** bal oldali panelen adatkészletek szakaszban.
   * Kattintson a **vonaldiagram** ikonra.
   * Kattintson a **feldolgozott** a a **mezők** úgy, hogy az informatikai jeleníti meg a "Tengely" panelen a **Vizualizációk** ablaktáblán.
   * Kattintson a "s11" és "s11\_riasztás", mindkettő "Values" alatt jelenik meg. Kattintson a Tovább gombra a kis nyílra **s11** és **s11\_riasztás**, módosítsa a "Sum" "Átlagos".
   * Kattintson a **mentése** felül és neve a jelentés "aircraftmonitor." A "aircraftmonitor" nevű jelentésben látható a **jelentések** című rész a **kezelő** a bal oldali panelen.
   * Kattintson a **PIN-kód Visual** ezen a vonaldiagramon jobb felső sarkában található ikonra. A "Rögzítés az irányítópulton" ablak előfordulhat, hogy jelennek meg, hogy válasszon ki egy irányítópultot. Válassza ki a "Prediktív karbantartási bemutató", majd kattintson a "Rögzítés."
   * Vigye az egérmutatót a csempét az irányítópulton, kattintson a "11. érzékelő és a flotta nézetét a cím módosításához a jobb felső sarokban található"edit"ikonra. Küszöbérték 48.26" és"Idővel flotta közötti átlagos."felirat

## <a name="delete-your-solution"></a>A megoldás törlése
Győződjön meg arról, hogy leállítsa az adatgenerálást amikor nem használja aktívan a megoldást, az adatgeneráló magasabb költségekkel jár. Törölje a megoldást, ha nem használ. A megoldás törlésekor a megoldás üzembe helyezésekor az előfizetésben kiépített összes összetevőt. Törölje a megoldást, a megoldás nevét a megoldássablon bal oldali panelén kattintson, és kattintson **törlése**.

## <a name="cost-estimation-tools"></a>Költségbecslés eszközök
A következő két eszközök segítségével jobban megismerheti az a teljes költség a prediktív karbantartás a légi és Űrközlekedés Megoldássablon előfizetésében futó részt érhetők el:

* [A Microsoft Azure Cost Estimator eszközt (online)](https://azure.microsoft.com/pricing/calculator/)
* [A Microsoft Azure Cost Estimator eszközt (asztali verzió)](https://www.microsoft.com/download/details.aspx?id=43376)

