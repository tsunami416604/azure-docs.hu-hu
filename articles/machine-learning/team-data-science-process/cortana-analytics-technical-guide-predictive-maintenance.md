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
ms.date: 03/15/2017
ms.author: fboylu
ms.openlocfilehash: 03ae6245e83c1f26546ec2a33c74dc9519847d7b
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace-and-other-businesses"></a>A Cortana Intelligence megoldás sablont űrtechnikai és más vállalatok számára a prediktív karbantartási műszaki útmutatója

## <a name="important"></a>**Fontos**
Ez a cikk elavult. Az ismertető a probléma megírja, ez azt jelenti, hogy prediktív karbantartás miatt űrtechnikai, továbbra is kapcsolódik, de tekintse meg [megoldási áttekintés üzleti célközönség](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace) aktuális információk.

## <a name="acknowledgements"></a>**A nyugtázás**
Ez a cikk történt adatszakértőkön Pozsony Zhang Gauher Shaheen, Fidan Boylu Uz, által, ezért szoftver Dan Grecoe Microsoft tervezését.

## <a name="overview"></a>**Áttekintés**
Megoldás sablonok készültek, annak érdekében, a folyamatot nevezzük, egy E2E bemutató Cortana Intelligence Suite felett. A telepített sablon látja el az előfizetés szükséges a Cortana Intelligence összetevőkkel, és a köztük lévő viszonyt is létre. Azt is magok az adatok csővezeték adatok-készítő alkalmazás, amely letölti és telepíti a helyi számítógépen, a megoldás sablon telepítése után létrehozott mintaadatokkal. A készítő által létrehozott adatok hydrates az adatok feldolgozási sorban lévő és a gépi tanulás előrejelzéseket, amelyek megjeleníthetők a Power BI-irányítópult a létrehozásakor start. A telepítési folyamat végigvezeti a megoldás hitelesítő adatok beállításához számos lépést. Győződjön meg arról, például a megoldás neve, a felhasználónevet és jelszót a telepítés során megadott hitelesítő adatokat rögzíti.  

Ez a dokumentum célja bemutatja a referencia-architektúrában, és ez a megoldás sablon részeként az előfizetésében összetevők, a mintaadatok lecserélése a saját adatait, és a megoldás sablont módosításához jeleníti meg.  

> [!TIP]
> Töltse le, és nyomtassa ki a [PDF-verziót a dokumentum](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).
> 
> 

## <a name="overview"></a>**Áttekintés**
![A prediktív karbantartási architektúrája](./media/cortana-analytics-technical-guide-predictive-maintenance/predictive-maintenance-architecture.png)

Telepítheti a megoldás aktiválja az Azure-szolgáltatások üzemeltetését a Cortana Analytics Suite (Eseményközpont, Stream Analytics, HDInsight, adat-előállítót, Machine Learning *stb*). Az architektúra látható a prediktív karbantartási megoldás repüléstechnikai sablon összeállított módját. A megoldás központi telepítés (kivéve a HDInsight, amely az igény szerinti lett kiépítve, ha a kapcsolódó folyamat tevékenységek futtatásához szükséges létre megoldás sablon diagram rájuk kattintva használatával megvizsgálhatja a ezeket a szolgáltatásokat az Azure-portálon és törölte ezt követően).
Töltse le a [a diagram teljes méretű változatát](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

A következő szakaszok ismertetik a megoldás részeit.

## <a name="data-source-and-ingestion"></a>**Az adatforrás és feldolgozási**
### <a name="synthetic-data-source"></a>Szintetikus adatforrás
Ehhez a sablonhoz használt adatforrás egy asztali alkalmazás letöltése és futtatása helyileg, a sikeres telepítést jönnek létre. Útmutatást követve töltse le és telepítse az alkalmazás esetén a Tulajdonságok sávon választja, a prediktív karbantartási Adatgenerátor nevű megoldás sablon diagram első csomópontjára. Ez az alkalmazás-hírcsatornák a [Azure Event Hubs](#azure-event-hub) szolgáltatás mellett vagy ugyanazt a megoldás folyamatot a többi használt események. Ez az adatforrás eredete nyilvánosan elérhető adatait a [NASA adattárház](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/) használatával a [turbóventillátoros motor teljesítménycsökkenést szimuláció adatkészlet](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan).

Az esemény generációs alkalmazás tölti fel az Azure Event Hubs csak, amíg a számítógép végrehajtása történik.

### <a name="azure-event-hub"></a>Az Azure event hubs
A [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) szolgáltatása címzettje a szintetikus adatforrás által megadott bemenetet.

## <a name="data-preparation-and-analysis"></a>**Adatok előkészítése és elemzése**
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Használjon [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) közel valós idejű elemzés a bemeneti adatfolyam a szolgáltatás a [Azure Event Hubs](#azure-event-hub) alakzatot eredmények közzététele és a szolgáltatás egy [Power BI](https://powerbi.microsoft.com) irányítópult, valamint az összes nyers bejövő események archiválása a [Azure Storage](https://azure.microsoft.com/services/storage/) szolgáltatás később feldolgozásra, amelyet a [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) szolgáltatás.

### <a name="hdinsight-custom-aggregation"></a>Egyéni összesítési HDInsight
Futtatás [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) parancsfájlok (Azure Data Factory által összehangolva) összesítések ad a nyers eseményeket az Azure Stream Analytics szolgáltatással archivált HDInsight eszközzel.

### <a name="azure-machine-learning"></a>Azure Machine Learning
A hasznos élettartama (Szabályainak) előrejelzéseket készítsen a bemenetek érkezett a megadott adott repülőgép motor [Azure Machine Learning szolgáltatás](https://azure.microsoft.com/services/machine-learning/) (Azure Data Factory által összehangolva) számára 

## <a name="data-publishing"></a>**Adatok közzététele**
### <a name="azure-sql-database-service"></a>Az Azure SQL adatbázis-szolgáltatás
Használjon [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) tárolásához (által felügyelt Azure Data Factory) az Azure Machine Learning szolgáltatás az előrejelzés során a [Power BI](https://powerbi.microsoft.com) irányítópult.

## <a name="data-consumption"></a>**Adatok felhasználásához**
### <a name="power-bi"></a>Power BI
Használjon [Power BI](https://powerbi.microsoft.com) szolgáltatást, hogy egy irányítópultot, amely tartalmazza az összesítések és által biztosított értesítések megjelenítése a [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) szolgáltatás, valamint tárolt Szabályainak előrejelzéseket [Azure SQL Database ](https://azure.microsoft.com/services/sql-database/) használatával keletkezett a [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) szolgáltatás.

## <a name="how-to-bring-in-your-own-data"></a>**Útmutató saját adatok**
Ez a szakasz ismerteti, hogyan kell a saját adatait az Azure-ba, és milyen területeken módosítások szükségesek az adatok akkor vonja ebbe az architektúrába.

Nem valószínű, hogy az adatkészlet által használt adatkészlet megegyezik-e a [turbóventillátoros motor teljesítménycsökkenést szimuláció adatkészlet](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan) megoldás sablon használatos. Az adatok és a követelmények megértése alapvető fontosságúak a saját adatok sablon módosítása. 

Az alábbi szakaszok ismertetik a következő szakaszok a sablon szükséges módosítani, ha új adatkészlet megjelent.

### <a name="azure-event-hub"></a>Az Azure Event Hubs
Az Azure Event Hubs szolgáltatás nem általános; a központ JSON vagy CSV formátumú adatokat is közzé lehet tenni. Az Azure Event Hubs különleges feldolgozás nem történik, de fontos, hogy tudomásul veszi be táplált adatokat.

Ez a dokumentum nem ismerteti az adatok, de használatával egyszerűen küldhet eseményeket vagy adatokat az Azure-Eseményközpontok felé az Event Hub API-k használatával.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Az Azure Stream Analytics szolgáltatás segítségével adatfolyamok olvasása és írása források tetszőleges számú adatokat közel valós idejű elemzési nyújtanak.

A prediktív karbantartási megoldás repüléstechnikai sablon az Azure Stream Analytics lekérdezési áll négy sub lekérdezések, minden egyes lekérdezés fel az eseményeket az Azure Event Hubs szolgáltatásból négy különböző helyekre kimenettel. A kimenetek három Power BI-adatkészletek és egy Azure tárolási helye állnak.

Az Azure Stream Analytics lekérdezési találhatók:

* Csatlakozás az Azure-portálon
* A Stream Analytics-feladatok keresése ![Stream Analytics ikon](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-stream-analytics.png) , hogy a megoldás telepítésekor jött létre (*például*, **maintenancesa02asapbi** és **maintenancesa02asablob** a prediktív karbantartási megoldás)
* Kiválasztása
  
  * ***BEMENETEK*** adjon meg a lekérdezés megtekintése
  * ***LEKÉRDEZÉS*** magát a lekérdezést megtekintése
  * ***KIMENETI*** a különböző kimenetének megtekintése

Azure Stream Analytics lekérdezési konstrukció kapcsolatos információk találhatók a [Stream Analytics lekérdezési hivatkozás](https://msdn.microsoft.com/library/azure/dn834998.aspx) az MSDN Webhelyén.

Ebben a megoldásban a lekérdezések kimeneti három adatkészletek a közel valós idejű elemzési információt a bejövő adatfolyam megoldás sablon részeként a Power BI-irányítópulthoz. Mivel a bejövő adatformátum implicit ismerete, ezeket a lekérdezéseket módosítani kell az adatok formátum alapján.

A lekérdezés második Stream Analytics-feladat **maintenancesa02asablob** egyszerűen kiírja az összes [Eseményközpont](https://azure.microsoft.com/services/event-hubs/) események [Azure Storage](https://azure.microsoft.com/services/storage/) és ezért nem módosítása a teljes esemény adatformátum függetlenül információk Storage továbbítja adatfolyamként.

### <a name="azure-data-factory"></a>Azure Data Factory
A [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) szolgáltatás koordinálja a mozgás és az adatok feldolgozása. A prediktív karbantartási megoldás repüléstechnikai sablon, a data factory áll három [folyamatok](../../data-factory/v1/data-factory-create-pipelines.md) , helyezze át, és feldolgozza a különféle technológiái.  A data factory érhető el a Data Factory-csomópont a megoldás sablon diagram a megoldás üzembe helyezése a létrehozott alján megnyitásával. Az adatkészletek a hibák miatt adat-előállító folyamatban már telepítve vannak a a adatgenerátor lett elindítva. Ezek a hibák figyelmen kívül lesz hagyva, és akadályozza meg, hogy a data factory működik

![Data Factory dataset hibák](./media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Ez a szakasz ismerteti a szükséges [folyamatok](../../data-factory/v1/data-factory-create-pipelines.md) és [tevékenységek](../../data-factory/v1/data-factory-create-pipelines.md) szerepel a [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Ez a megoldás diagram nézetet.

![Azure Data Factory](./media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Két, az adat-előállító adatcsatornák tartalmazhat [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) használt particionálása és összesíti az adatokat. Az áttelepítés előtt feljegyzett, ha a parancsfájlok a találhatók a [Azure Storage](https://azure.microsoft.com/services/storage/) a telepítés során létrehozott fiók. A hely a következő: maintenancesascript\\\\parancsfájl\\\\hive\\ \\ (vagy https://[Your megoldás name].blob.core.windows.net/maintenancesascript).

Hasonló [Azure Stream Analytics](#azure-stream-analytics-1) lekérdezéseket, a [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) parancsfájlok bejövő adatformátum implicit ismerete rendelkezik, és módosítani kell az adatok formátum alapján.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Ez [csővezeték](../../data-factory/v1/data-factory-create-pipelines.md) tartalmazza egy adott tevékenység - egy [HDInsightHive](../../data-factory/v1/data-factory-hive-activity.md) tevékenység segítségével egy [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , amelyen fut a [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) az adatok particionálása-parancsprogramot be [Azure Storage](https://azure.microsoft.com/services/storage/) során a [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) feladat.

A [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) particionálási feladat érték parancsfájl ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Ez [csővezeték](../../data-factory/v1/data-factory-create-pipelines.md) több tevékenységet, amelynek végeredménynek a pontozott előrejelzéseket tartalmazza a a [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) megoldás sablonhoz társított kísérlet.

Tevékenységek tartalmazza a következők:

* [HDInsightHive](../../data-factory/v1/data-factory-hive-activity.md) tevékenység segítségével egy [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , amelyen fut a [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) összesítések elvégzéséhez és a szükséges jellemzőkiemelés parancsfájl a [Azure Gépi tanulás](https://azure.microsoft.com/services/machine-learning/) kipróbálásához.
  A [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) particionálási feladat érték parancsfájl ***PrepareMLInput.hql***.
* [Másolás](https://msdn.microsoft.com/library/azure/dn835035.aspx) tevékenység, amely helyezi át az eredményeket a [HDInsightHive](../../data-factory/v1/data-factory-hive-activity.md) egyetlen tevékenységet [Azure Storage](https://azure.microsoft.com/services/storage/) által elért blob a [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) tevékenység.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) tevékenység hívások a [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) kísérletet, egyetlen be eredményekkel [Azure Storage](https://azure.microsoft.com/services/storage/) blob.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Ez [csővezeték](../../data-factory/v1/data-factory-create-pipelines.md) tartalmazza egy adott tevékenység - egy [másolási](https://msdn.microsoft.com/library/azure/dn835035.aspx) tevékenység, amely helyezi át az eredményeket a [Azure Machine Learning](#azure-machine-learning) a kísérletezhet a  ***MLScoringPipeline*** számára a [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) kiépítve a megoldás sablon telepítésének részeként.

### <a name="azure-machine-learning"></a>Azure Machine Learning
A [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) használt kísérletezhet, ez a megoldás sablon biztosít a fennmaradó hasznos élettartama (Szabályainak) repülőgép motor számára. A kísérlet felhasznált adathalmaz és módosítást igényel, vagy az adatok adott helyettesítő állapotba hozni.

További információ az Azure Machine Learning kísérlet létrehozásának módja: [prediktív karbantartási: 1. lépés 3-as adatok előkészítése, a szolgáltatás fejlesztés](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>**A figyelő folyamatban**
A Adatgenerátor eszköztárat, a feldolgozási folyamat veszi át a dehidratált, és a megoldás a különböző összetevők start azokat a parancsokat az adat-előállítóban által kibocsátott következő kicking. A folyamat figyelése két módja van.

1. A blob storage a nyers bejövő adatokat ír a Stream Analytics-feladatok egyikét. Ha rákattint az Blob Storage-összetevő a megoldás a képernyőről, sikeresen telepített a megoldás és a jobb oldali panelen kattintson a Megnyitás, amíg a [Azure-portálon](https://portal.azure.com/). Ezután kattintson a Blobok. A következő panelen tárolók listájának megtekintéséhez. Kattintson a **maintenancesadata**. A következő panel egy a **rawdata** mappa. Az rawdata mappában találhatók a mappákról, például órát nevű = 17 és óra = 18. Ezek a mappák jelenléte azt jelzi, hogy folyamatban van a nyers adatok jön létre a számítógépen, és a blob Storage tárolóban tárolja. Meg kell jelennie a csv-fájlok a véges mérete (MB) az érintett mappákat.
2. A folyamat utolsó lépését az adatokat (például a gépi tanulás által előrejelzéseket) az SQL-adatbázisba írni. Lehetséges, hogy várnia legfeljebb három órával az adatok jelennek meg az SQL-adatbázis. Figyelheti, hogy mennyi adatot érhető el az SQL-adatbázis az egyik lehetőség a [Azure-portálon](https://portal.azure.com/). A bal oldali panelen keresse meg az SQL-ADATBÁZISOK ![SQL ikon](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-SQL-databases.png) , és kattintson rá. Keresse meg az adatbázis **pmaintenancedb** , és kattintson rá. A következő oldalon a lap alján kattintson a kezelés
   
    ![Ikon kezelése](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-manage.png).
   
    Itt kattintson az új lekérdezés és a lekérdezés megadása a sorok (pl. Válassza count(*) a PMResult) száma. Az adatbázis növekedésének megfelelően, növelje a tábla sorainak száma.

## <a name="power-bi-dashboard"></a>**A Power BI-irányítópulton**

Állítsa be a Power BI-irányítópulton jelenítheti meg az Azure Stream Analytics adatok (gyakran használt adatok elérési útja) és a kötegelt előrejelzés eredményeinek képzési (cold) az Azure machine.

### <a name="set-up-the-cold-path-dashboard"></a>Állítsa be a cold elérési irányítópult
A cold elérési adatok folyamat célja a prediktív Szabályainak (hátralévő élettartama) minden egyes repülőgép motor megszerezni a felhőszolgáltató közötti átviteléhez (ciklus) művelet befejeződése után. Az előrejelzés eredmény repülőgép motor, amely az elmúlt 3 órában a felhőszolgáltató közötti átviteléhez végzett előrejelzésére 3 óránként frissül.

A Power BI csatlakozik Azure SQL-adatbázis adatforrásként, az előrejelzési eredmények tárolásához. Megjegyzés: 1.) a megoldás telepítése, az előrejelzéshez megjelennek az adatbázisban 3 óra.
A pbix-fájl, melyet a kódgenerátor letöltési tartalmaz kezdőérték adatokat, hogy a Power BI-irányítópult azonnal létrehozhat. 2.) Ez a lépés az előfeltétel, hogy töltse le és telepítse az ingyenes szoftvert [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Az alábbi lépéseket a pbix-fájl csatlakoztatása az SQL-adatbázis, amely a képi megjelenítés adatainak (például előrejelzés eredmények) tartalmazó megoldás üzembe helyezése során hoz létre a ismerteti.

1. Az adatbázis hitelesítő adatainak lekéréséhez.
   
   Szüksége lesz **adatbázis-kiszolgáló nevét, az adatbázisnév, felhasználónevet és jelszót** előtt áthelyezése a következő lépéseket. Az alábbiakban a lépéseit ismerteti azok megkereséséről.
   
   * Egyszer **"Azure SQL Database"** a megoldás sablonban diagram zöldre, kattintson rá, és kattintson a **"Megnyitás"**.
   * Láthatja, hogy egy új lap/böngészőablakot, amely az Azure portál oldalát jeleníti meg. Kattintson a **"Erőforráscsoportok"** a bal oldali panelen.
   * Válassza ki az előfizetést, a megoldás telepítéséhez használ, és válassza **"YourSolutionName\_ResourceGroup"**.
   * Az új pop kimenő panelen, kattintson a ![SQL ikon](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-sql.png) ikon az adatbázis eléréséhez. Az adatbázisnév van erre az ikonra mellett (például **"pmaintenancedb"**), és a **adatbázis-kiszolgáló neve** megtalálható-e a Server name tulajdonság és hasonlóan kell kinéznie  **YourSoutionName.database.windows.net**.
   * Az adatbázis **felhasználónév** és **jelszó** ugyanaz, mint a felhasználónév és jelszó korábban tárolja, a megoldás üzembe helyezése során.
2. Az adatforrás a cold elérési jelentés fájl frissítése. a Power BI Desktop.
   
   * A mappát, amelyben letöltött és a kódgenerátor fájl unzipped, kattintson duplán a **Power bi\\PredictiveMaintenanceAerospace.pbix** fájlt. Ha a fájl megnyitásakor minden mellőzött figyelmeztető üzenet jelenik meg, figyelmen kívül hagyja azokat. A fájl felső részén található kattintson **szerkesztése lekérdezések**.
     
     ![Lekérdezések szerkesztése](./media/cortana-analytics-technical-guide-predictive-maintenance/edit-queries.png)
   * Látni fogja, két tábla **RemainingUsefulLife** és **PMResult**. Válassza ki az első tábla, és kattintson a ![lekérdezés beállítások ikonra](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-query-settings.png) melletti **"Forrás"** alatt **alkalmazott LÉPÉSEKET** a jobb oldali **"Lekérdezés beállításai"** panel. Hagyja figyelmen kívül minden mellőzött figyelmeztető üzenet jelenik meg.
   * Cserélje le a pop ki ablakot, **"Server"** és **"Database"** a saját kiszolgáló és az adatbázis nevének, és kattintson a **"OK"**. A kiszolgálónév, ellenőrizze, hogy az 1433-as port megadott (**YourSoutionName.database.windows.net, 1433**). Hagyja meg az adatbázis mező **pmaintenancedb**. Figyelmen kívül hagyja a képernyőn megjelenő figyelmeztető üzeneteket.
   * A következő pop ki ablakot, két lehetőség közül választhat a bal oldali ablaktáblán látható (**Windows** és **adatbázis**). Kattintson a **"Database"**, töltse ki a **"Username adat"** és **'Password'** (azt a felhasználónevet és jelszót, amikor először a megoldás telepített és létrehozott egy Azure SQL-adatbázis). A ***válassza ki, melyik szintre legyenek érvényesek a beállítások***, ellenőrizze az adatbázis-szintű beállítás. Kattintson a **"Csatlakozás"**.
   * Kattintson a második tábla **PMResult** kattintson ![navigációs ikon](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-navigation.png) melletti **"Forrás"** alatt **alkalmazott LÉPÉSEKET** a jobb oldali **"Lekérdezés beállításai"** panelen, és módosíthatja az a kiszolgáló és az adatbázis nevének, ahogy a fenti lépéseket, és kattintson az OK gombra.
   * Amennyiben most interaktív, az előző oldalra, az ablak bezárásához. Egy üzenet jelenik meg – kattintson **alkalmaz**. Végül kattintson a **mentése** gombra a módosítások mentéséhez. A Power BI-fájl most hozott létre kapcsolatot a kiszolgálóval. Üres a képi megjelenítés esetén győződjön meg arról, hogy törli a kijelölt elemek összes adatok megjelenítéséhez a jelmagyarázatokból jobb felső sarkában a radír ikonjára kattint a képi megjelenítések. A frissítés gombra segítségével új adatok vizuális tükrözi. Kezdetben csak akkor jelenik meg a adatait a képi megjelenítések a, az adat-előállítóban ütemezett frissítése 3 óránként. 3 óra elteltével jelenik meg új előrejelzéseket megjelennek a képi megjelenítések, amikor az adatok frissítése.
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
Az alábbi lépéseket ismerteti hogyan jelenítheti meg a Stream Analytics-feladatok, a megoldás üzembe helyezése során előállított kimeneti adatokat. A [Power BI online](http://www.powerbi.com/) fiók szükséges a következő lépésekkel. Ha nincs fiókja, akkor [hozzon létre egyet](https://powerbi.microsoft.com/pricing).

1. Adja hozzá a Power BI-kimenet Azure Stream Analytics (ASA).
   
   * Hajtsa végre az utasításait [Azure Stream Analytics & Power BI: egy elemzések irányítópultján a valós idejű látható-e a streamelési adatok](../../stream-analytics/stream-analytics-power-bi-dashboard.md) állíthatja be a Power BI-irányítópultot, az Azure Stream Analytics-feladat eredményének.
   * Az ASA lekérdezés rendelkezik három kimenetek, amelyek **aircraftmonitor**, **aircraftalert**, és **flightsbyhour**. A lekérdezés fülre kattintva megtekintheti a lekérdezést. Megfelelő ezek a táblázatok, kell egy kimenet hozzáadása ASA. Az első kimeneti hozzáadásakor (**aircraftmonitor**) Győződjön meg arról, hogy a **kimeneti Alias**, **Adatkészletnevet** és **táblanév** az azonos (vannak**aircraftmonitor**). Ismételje meg a kimeneti hozzáadandó **aircraftalert**, és **flightsbyhour**. Miután hozzáadta a három kimeneti táblák, és a ASA feladat elindítása, szerezheti be egy megerősítő üzenetet ("Stream Analytics indítása feladat maintenancesa02asapbi sikeres").
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
   * Kattintson a **mentése** a a top és a név a jelentés "aircraftmonitor." A "aircraftmonitor" nevű jelentésben látható a **jelentések** szakasz a **Navigator** a bal oldali ablaktáblán.
   * Kattintson a **PIN-kód Visual** ikonra a vonaldiagram jobb felső sarkában. "PIN-kód irányítópult" ablak lehet, hogy jelenik meg, hogy válasszon egy irányítópultot. "A prediktív karbantartási bemutató", majd kattintson a "PIN" szabályzatot.
   * Az egérrel a csempe az irányítópulton keresztül, kattintson a jobb felső sarokban található módosítása "érzékelő 11 vs flotta nézetét a cím az"edit"ikonra. Küszöbérték 48.26" és"Átlagával időbeli járműflotta között."alcíme

## <a name="how-to-delete-your-solution"></a>**A megoldás törlése**
Győződjön meg arról, hogy le a adatgenerátor használatakor nem aktív a megoldás, a adatgenerátor futtató tájékozódnia magasabb költségek. Ha nem használnak, törölje a megoldás. A megoldás törlése a megoldás üzembe helyezésekor az előfizetésben kiépített összetevőit. Törölje a megoldás kattintson a bal oldali panelen, a megoldás sablont, majd kattintson a Törlés a megoldás neve.

## <a name="cost-estimation-tools"></a>**Költség becslése eszközök**
A következő két eszközök segítségével jobb megértése érdekében a prediktív karbantartási futtató repüléstechnikai Megoldássablon az előfizetésében szereplő részt a teljes költségek érhetők el:

* [A Microsoft Azure költség négyzetgyökének eszköz (online)](https://azure.microsoft.com/pricing/calculator/)
* [A Microsoft Azure költség négyzetgyökének eszköz (asztali verzió)](http://www.microsoft.com/download/details.aspx?id=43376)

