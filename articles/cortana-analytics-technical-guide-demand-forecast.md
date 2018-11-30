---
title: Kereslet-előrejelzés, energia műszaki útmutató
description: Technikai útmutató a Microsoft Cortana Intelligence-Megoldássablon az energiaellátás előrejelzése igény.
services: machine-learning
author: garyericson
manager: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/16/2016
ms.author: garye
ms.openlocfilehash: 88f6a27d4092e638403c641d72916ed9d2540708
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427063"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>Technikai útmutató a Cortana Intelligence Megoldássablon igény szerint az energiaellátás előrejelzése
## <a name="overview"></a>**Áttekintés**
Gyorsítsa fel a folyamat létrehozásának felül a Cortana Intelligence Suite-E2E bemutató Megoldássablonok lettek kialakítva. Egy üzembe helyezett sablon látja el az előfizetés szükséges a Cortana Intelligence-összetevővel, és a közötti kapcsolatokat hozhat létre. Első generált adatok szimuláció alkalmazás mintaadatokkal adatfolyamat is feltölti. Az adatszimuláló töltse le a megadott, és telepítheti a helyi gépén, tekintse meg a readme.txt fájlt a utasítás a szimulátor használatával. A szimulátor generált adatok hydrates a adatfolyamat és kezdő létrehozása a machine learning-előrejelzés, amelyek megjeleníthetők a Power BI-irányítópulton.

A megoldássablon található [Itt](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1)

Az üzembe helyezési folyamat végigvezeti a megoldás hitelesítő adatok beállításához számos lépést. Győződjön meg arról, jegyezze fel ezeket a hitelesítő adatokat például a megoldás nevét, a felhasználónevet és jelszót az üzembe helyezés során.

A jelen dokumentum célja, hogy a referencia-architektúra és a különböző összetevőket az előfizetésében, ez a Megoldássablon részeként. A dokumentum is ismerteti a mintaadatokat, cserélje le tényleges adatok insights/előrejelzéseket, elért adatok láthatók a saját módjáról. Emellett a dokumentum előadások kapcsolatos azon részei a Megoldássablon, ha testre szeretné szabni a megoldást a saját adataival módosítani kellene. Ez a Megoldássablon hozhat létre a Power BI-irányítópulton való utasításokat a végén.

## <a name="details"></a>**Részletek**
![](media/cortana-analytics-technical-guide-demand-forecast/ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>Az architektúra ismertetése
Ha a megoldás üzembe lett helyezve, Cortana Analytics Suite belül különböző Azure-szolgáltatások rendszer aktiválja (azt jelenti, a Event Hub, az Stream Analytics, a HDInsight, a Data Factory, a Machine Learning, *stb.*). Architektúradiagram bemutatja, magas szinten, hogyan energia Megoldássablon készült kereslet-előrejelzési értékekből összeállított teljes körű. Ezek a szolgáltatások segítségével megvizsgálhatja a központi telepítés a megoldás használatával létrehozott sablon megoldásdiagramon rájuk kattintva. A következő szakaszok ismertetik az egyes.

## <a name="data-source-and-ingestion"></a>**Adatforrás és feldolgozó**
### <a name="synthetic-data-source"></a>Szintetikus adatforrás
Ehhez a sablonhoz használt adatforrás egy asztali alkalmazás, töltse le és futtassa helyileg a sikeres telepítést követően a jön létre. Töltse le és telepítse az alkalmazást a Tulajdonságok sávon, energia-előrejelzés Adatszimuláló nevű sablon megoldásdiagramon első csomópontjának kiválasztásakor utasításokat találja. Ez az alkalmazás-csatornák a [Azure Event Hub](#azure-event-hub) adatpontokat, vagy a megoldás folyamatának többi használt események szolgáltatásként.

Az esemény generálása alkalmazás feltöltése az Azure Event Hubs, csak azt a számítógépen végrehajtása közben.

### <a name="azure-event-hub"></a>Azure-eseményközpont
A [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) szolgáltatás a címzett, a bemeneti leírt szintetikus adatforrás által biztosított.

## <a name="data-preparation-and-analysis"></a>**Adat-előkészítési és -elemzés**
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
A [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) érkező bemeneti Stream közel valós idejű szolgáltatást használja a [Azure Event Hub](#azure-event-hub) szolgáltatást, és tegye közzé az eredményeket a egy [Power BI](https://powerbi.microsoft.com)irányítópultot, valamint a nyers bejövő eseményeket, az archiválás a [Azure Storage](https://azure.microsoft.com/services/storage/) szolgáltatás később feldolgozásra, amelyet a [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) szolgáltatás.

### <a name="hdinsight-custom-aggregation"></a>HDInsight egyéni összesítés
Az Azure HDInsight szolgáltatás futtatásához használt [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) parancsfájlok (Azure Data Factory által előkészített) az Azure Stream Analytics szolgáltatással archivált nyers események összesítését.

### <a name="azure-machine-learning"></a>Azure Machine Learning
A [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) szolgáltatás használható-e (az Azure Data Factory által előkészített) alapján előrejelzi egy adott régió kapott bemeneti adatok jövőbeli fogyasztást.

## <a name="data-publishing"></a>**Adatok közzététele**
### <a name="azure-sql-database-service"></a>Azure SQL Database Service
A [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) szolgáltatás tárolására szolgál (az Azure Data Factory kezeli) az előrejelzés fogadja az Azure Machine Learning szolgáltatás a felhasznált a [Power BI](https://powerbi.microsoft.com) irányítópultot.

## <a name="data-consumption"></a>**Adathasználat**
### <a name="power-bi"></a>Power BI
A [Power BI](https://powerbi.microsoft.com) szolgáltatás által biztosított összesítések tartalmazó irányítópult megjelenítéséhez használja a [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) szolgáltatás, valamint igény szerinti előrejelzési eredményeket tárolja [Azure SQL Adatbázis](https://azure.microsoft.com/services/sql-database/) használatával keletkezett a [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) szolgáltatás. Ez a Megoldássablon hozhat létre a Power BI-irányítópult kapcsolatos utasításokért tekintse meg a következő szakaszt.

## <a name="how-to-bring-in-your-own-data"></a>**Hogyan lehet a saját adatok beolvasása**
Ez a szakasz ismerteti, hogyan lehet a saját adatok importálása az Azure-ba, és milyen területeken igényel módosításokat az adatok, ez az architektúra állapotba.

Nem valószínű, hogy minden olyan adatkészletben tenné az adatkészlet, ez a megoldássablon használt megfelel. Az adatok és a követelmények ismertetése alapvető fontosságúak a saját adatok sablon módosítása. Ha nem ismeri az Azure Machine Learning szolgáltatáshoz, kérheti, bemutató példa használatával [az első kísérlet létrehozása](machine-learning/studio/create-experiment.md).

A következő részekben bemutatjuk a sablont, amikor egy új adatkészlet bevezetett módosításokat igényel a szakaszait.

### <a name="azure-event-hub"></a>Azure-eseményközpont
A [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) szolgáltatást az általános, úgy, hogy az adatok a hub CSV vagy JSON formátumban adhatók fel. Speciális feldolgozás nem következik be az Azure Event Hubs, de fontos tisztában bele adatkéréseket az adatokat.

Ez a dokumentum nem ismerteti, hogy az adatokat, de az egyik egyszerűen küldhet eseményeket vagy adatokat egy Azure-eseményközpontba használatával a [Event Hub API](event-hubs/event-hubs-programming-guide.md).

### <a name="azure-stream-analytics"></a>Azure Stream Analytics
A [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) szolgáltatás közel valós idejű adatfolyamok olvasásakor és ad ki adatokat tetszőleges számú források által használható.

A készült kereslet-előrejelzési energia Megoldássablon az Azure Stream Analytics-lekérdezés két segédlekérdezések, minden egyes esemény az Azure Event Hubs szolgáltatásból fel bemenetként, és a kimenetek két különböző helyre áll. Ezeket a kimeneteket áll egy Power BI-adatkészletet, és a egy Azure Storage-helyre.

A [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) lekérdezés találhatók:

* Bejelentkezett a [Azure Portalon](https://portal.azure.com/)
* A stream analytics-feladatok megkeresése ![](media/cortana-analytics-technical-guide-demand-forecast/icon-stream-analytics.png) , amely a megoldás telepítésekor jött létre. Egyet az adatok leküldése a blob storage (például mytest1streaming432822asablob) és a másik egy az adatok leküldése Power bi-ban (például mytest1streaming432822asapbi).
* Kiválasztása

  * ***BEMENETEK*** adjon meg a lekérdezés megtekintése
  * ***LEKÉRDEZÉS*** magát a lekérdezés megtekintése
  * ***OUTPUTS*** a különböző kimenetek megtekintése

Az Azure Stream Analytics lekérdezési konstrukció információ található a [Stream Analytics lekérdezési leírása](https://msdn.microsoft.com/library/azure/dn834998.aspx) az MSDN Webhelyén.

Ebben a megoldásban ez a megoldássablon részeként az Azure Stream Analytics-feladat, amely közel valós idejű elemzési információ a Power BI-irányítópultra a beérkező adatfolyam adatkészlet áll rendelkezésre. Mivel a bejövő adatok formátumának beállításszolgáltató jellegéből fakadóan ismeri, ezeket a lekérdezéseket kell módosítani az adatformátum alapján.

A többi Azure Stream Analytics-feladat kimenete összes [Eseményközpont](https://azure.microsoft.com/services/event-hubs/) események [Azure Storage](https://azure.microsoft.com/services/storage/) , és ezért kell függetlenül az adatok formátuma nem lapjával, mivel a teljes esemény információk adatfolyamként történő tárolás.

### <a name="azure-data-factory"></a>Azure Data Factory
A [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) szolgáltatás koordinálja az adatáthelyezési és -adatok feldolgozása. A Megoldássablon energia készült kereslet-előrejelzési adat-előállító épül fel 12 [folyamatok](data-factory/concepts-pipelines-activities.md) áthelyezése és feldolgozása az adatokat különböző technológiák használatával, amely a.

  Az adat-előállító a Data Factory csomópont alján, a központi telepítés a megoldás használatával létrehozott sablon megoldásdiagramon megnyitásával érheti el. Megjelenik a data factory, az Azure Portalon. Ha hibába ütközik, az adatkészletek alatt, figyelmen kívül hagyhatja azokat, mivel ezek miatt a data factory-folyamatban üzembe helyezése előtt az adatgenerálást lett elindítva. Ezek a hibák nem akadályozzák az adat-előállító működik.

Ez a szakasz ismerteti a szükséges [folyamatok](data-factory/concepts-pipelines-activities.md) és [tevékenységek](data-factory/concepts-pipelines-activities.md) szerepel a [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Következő képen látható a megoldás a diagram nézet:

![](media/cortana-analytics-technical-guide-demand-forecast/ADF2.png)

Az öt az e-előállító folyamatok tartalmazhat [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) particionálja, és az adatok összesítéséhez használt parancsfájlok. Nincs feltüntetve, ha a parancsfájlok a találhatók-e a [Azure Storage](https://azure.microsoft.com/services/storage/) telepítés során létrehozott fiókot. A hely a következő: demandforecasting\\\\parancsfájl\\\\hive\\ \\ (vagy https://[Your megoldás name].blob.core.windows.net/demandforecasting).

Hasonló a [Azure Stream Analytics](#azure-stream-analytics-1) lekérdezéseket, a [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) parancsfájlok beállításszolgáltató jellegéből fakadóan ismeri a bejövő adatok formátumának, ezeket a lekérdezéseket lenne kell úgy kell módosítani az adatok formázása és alapján[jellemzőkiemelés](machine-learning/team-data-science-process/create-features.md) követelményeinek.

#### <a name="aggregatedemanddatato1hrpipeline"></a>*AggregateDemandDataTo1HrPipeline*
Ez [folyamat](data-factory/concepts-pipelines-activities.md) tartalmaz egyetlen tevékenység – egy [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) tevékenység használatával egy [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) futtat egy [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) összesített adatfolyamként továbbított az igény szerinti adatok szkriptet 10 másodpercenként állomás szint óránként régió szintre, és helyezze [Azure Storage](https://azure.microsoft.com/services/storage/) keresztül az Azure Stream Analytics-feladatot.

A [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) parancsfájl esetében ez a particionálási feladat ***AggregateDemandRegion1Hr.hql***

#### <a name="loadhistorydemanddatapipeline"></a>*LoadHistoryDemandDataPipeline*
Ez [folyamat](data-factory/concepts-pipelines-activities.md) két tevékenységet tartalmaz:

* [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) tevékenység használatával egy [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) állomás szinten óránként régió szintre óránkénti előzmények igény szerint adatokat tudnak összesíteni, és az Azure Storage alatt az Azure Stream egy Hive-szkriptet futtat Analytics-feladat
* [Másolás](https://msdn.microsoft.com/library/azure/dn835035.aspx) tevékenységgel, amely helyezi át az összesített adatokat az Azure Storage-blobból az Azure SQL Database, amely a megoldás sablon telepítésének részeként lett üzembe helyezve.

A [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) parancsfájl esetében ez a feladat ***AggregateDemandHistoryRegion.hql***.

#### <a name="mlscoringregionxpipeline"></a>*MLScoringRegionXPipeline*
Ezek [folyamatok](data-factory/concepts-pipelines-activities.md) több tevékenységet tartalmaz, és amelynek a végeredmény a pontozott előrejelzéseket, ez a megoldássablon társított Azure Machine Learning-kísérletből. Azok majdnem teljesen megegyezik azzal a különbséggel azok csak a másik régióban, amely minden olyan régió esetében az ADF folyamat és a hive-parancsfájlnak átadott különböző RegionID végzett kezeli.  
Ez a folyamat található tevékenységeket a következők:

* [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) tevékenység használatával egy [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) összesítést végez, és az Azure Machine Learning-kísérlet szükséges jellemzőkiemelés egy Hive-szkriptet futtat. A tevékenység a Hive-parancsprogramok megfelelő ***PrepareMLInputRegionX.hql***.
* [Másolás](https://msdn.microsoft.com/library/azure/dn835035.aspx) helyezi át az eredményeket a tevékenység a [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) tevékenység, amely egy Azure Storage blob, amely szerint érhetők el a [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) tevékenység.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) tevékenység, amely meghívja ezt az Azure Machine Learning kísérletet üzembe egy Azure Storage blob az eredmények eredményez.

#### <a name="copyscoredresultregionxpipeline"></a>*CopyScoredResultRegionXPipeline*
Ez [folyamat](data-factory/concepts-pipelines-activities.md) egyetlen tevékenységgel - tartalmaz egy [másolási](https://msdn.microsoft.com/library/azure/dn835035.aspx) tevékenységgel, amely helyezi át az Azure Machine Learning-kísérlet eredményeit a megfelelő ***MLScoringRegionXPipeline***az Azure SQL Database, amely a megoldás sablon telepítésének részeként lett üzembe helyezve.

#### <a name="copyaggdemandpipeline"></a>*CopyAggDemandPipeline*
Ez [folyamat](data-factory/concepts-pipelines-activities.md) egyetlen tevékenységgel - tartalmaz egy [másolási](https://msdn.microsoft.com/library/azure/dn835035.aspx) helyezi át az összesített folyamatos igény szerint adatokat a tevékenységen ***LoadHistoryDemandDataPipeline*** az Azure SQL Adatbázis, amely a megoldás sablon telepítésének részeként lett üzembe helyezve.

#### <a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>*CopyRegionDataPipeline, CopySubstationDataPipeline, CopyTopologyDataPipeline*
Ez [folyamat](data-factory/concepts-pipelines-activities.md) egyetlen tevékenységgel - tartalmaz egy [másolási](https://msdn.microsoft.com/library/azure/dn835035.aspx) tevékenységgel, amely áthelyezi a régió/állomás/Topologygeo a referenciaadatokat a megoldássablon részeként az Azure Storage-blobba feltölteni a telepítés az Azure SQL Database, amely a megoldás sablon telepítésének részeként lett üzembe helyezve.

### <a name="azure-machine-learning"></a>Azure Machine Learning
A [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) kísérletezéshez felhasznált esetében ez a megoldássablon biztosít az igény szerinti régió előrejelzését. A kísérlet csak a felhasznált adathalmaz, és ezért van szükség, módosítása vagy adott állapotba kerül, az adatok cseréje.

## <a name="monitor-progress"></a>**A figyelő folyamatban**
Az Adatgenerátor indul el, miután a folyamat megkezdi az első hidratált, és a megoldás összetevői, indítsa el a Data Factory által kiadott parancsok művelet következő be megkezdése. Kétféleképpen figyelheti a folyamatot.

1. Ellenőrizze az adatok Azure Blob Storage-ból.

    A blob storage a Stream Analytics-feladatok egyik ír a nyers bejövő adatok. Kattintva **Azure Blob Storage** sikeresen üzembe helyezte a megoldást, és kattintson a képernyőn, ahonnan a megoldás az **nyílt** a jobb oldali panelen, amíg a [Azure portál](https://portal.azure.com). Ezután kattintson a **Blobok**. A következő panelen láthatja a tárolók listáját. Kattintson a **"energysadata"**. A következő panelen láthatja a **"demandongoing"** mappát. A rawdata a mappába, például dátum nevekkel mappák látja = 2016-01-28 stb. Ha látja ezeket a mappákat, azt jelzi, hogy a nyers adatok sikeresen létrehozva: a számítógép és a blob storage-ban tárolt. Fájlokat, mappákat MB-ot véges méretek rendelkeznie kell megjelennie.
2. Ellenőrizze az adatokat az Azure SQL Database-ből.

    A folyamat utolsó lépéseként írja az adatokat (például előrejelzéseket a gépi tanulás), SQL Database. Előfordulhat, hogy várjon legfeljebb két órával az SQL Database-ben jelennek meg adatok. Figyelheti, hogy mennyi adatot érhető el az SQL Database egyik módja, keresztül [az Azure portal](https://portal.azure.com/). Keresse meg a bal oldali panelen, az SQL Database-ADATBÁZISOK![](media/cortana-analytics-technical-guide-demand-forecast/SQLicon2.png) , és kattintson rá. Ezután keresse meg az adatbázis (azaz demo123456db), és kattintson rá. A következő oldalon **"Kapcsolódás az adatbázishoz"** területén kattintson **"Futtatás Transact-SQL-lekérdezéseket az SQL database"**.

    Itt kattintson új lekérdezés és a lekérdezés (például "válassza count(*) DemandRealHourly származó) sorok számát a" gyorsan növekszik az adatbázis, a tábla sorainak száma növelje.)
3. Ellenőrizze az adatokat a Power BI-irányítópulton.

    Gyakori elérésű útvonal Power BI-irányítópult állíthat a nyers bejövő adatok figyelésére. Kérjük, kövesse a "Power BI-irányítópult" szakaszban.

## <a name="power-bi-dashboard"></a>**Power BI-irányítópult**
### <a name="overview"></a>Áttekintés
Ez a szakasz ismerteti, hogyan állítható be a Power BI-irányítópulton jelenítheti meg az Azure stream analytics (gyakori elérésű útvonal) valós idejű adatok, valamint az Azure machine learning (ritka elérésű útvonal) származó eredmények előrejelzése.

### <a name="setup-hot-path-dashboard"></a>Gyakori elérésű útvonal irányítópult beállítása
A következő lépések végigvezetik hogyan jelenítheti meg a Stream Analytics-feladatok, a megoldás üzembe helyezése során létrehozott valós idejű adatok kimenetét. A [online a Power BI](https://www.powerbi.com/) -fiók szükséges a következő lépésekkel. Ha nincs fiókja, akkor az [hozzon létre egyet](https://powerbi.microsoft.com/pricing).

1. Adja hozzá a Power BI-kimenet az Azure Stream Analytics (ASA).

   * Kövesse a kell [Azure Stream Analytics és a Power bi-ban: A valós idejű elemzési irányítópultok a valós idejű streamelési adatok láthatóságának](stream-analytics/stream-analytics-power-bi-dashboard.md) állíthatja be a kimenet az Azure Stream Analytics-feladat, a Power BI-irányítópulton .
   * Keresse meg a stream analytics-feladat a [az Azure portal](https://portal.azure.com). A feladat a névnek kell lennie: YourSolutionName + "streamingjob" + véletlenszerű szám + "asapbi" (azaz demostreamingjob123456asapbi).
   * Adjon hozzá egy Power bi az ASA-feladat kimenetét. Állítsa be a **kimeneti Alias** , **"PBIoutput"**. Állítsa be a **adatkészlet neve** és **táblanév** , **"EnergyStreamData"**. Miután hozzáadta a kimenetet, kattintson a **"Start"** a Stream Analytics-feladat indítása az oldal alján. Érdemes kap egy megerősítő üzenetet (például "kezdő stream analytics-feladat sikeres myteststreamingjob12345asablob").
2. Jelentkezzen be [online a Power bi-ban](https://www.powerbi.com)

   * A bal oldali panelen, a saját munkaterületen, az adatkészletek a szakasz egy új adatkészlet megjelenítése a Power bi bal oldali panelen láthatja kell lennie. Ez az az előző lépésben az Azure Stream Analytics leküldve a streamelt adatokat.
   * Győződjön meg arról, hogy a ***Vizualizációk*** panel meg nyitva, és a képernyő jobb oldalán jelenik meg.
3. Hozza létre a "Igény szerint időbélyegző" csempére:

   * Kattintson az adatkészlet **"EnergyStreamData"** bal oldali panelen adatkészletek szakaszban.
   * Kattintson a **"Vonaldiagram"** ikon ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic8.png).
   * Kattintson a "EnergyStreamData" a **mezők** panel.
   * Kattintson a **"Timestamp"** és ellenőrizze, hogy a "Tengely" jeleníti meg. Kattintson a **"Betöltés"** és ellenőrizze, hogy "Values" alatt jelenik meg.
   * Kattintson a **mentése** felül és neve a "EnergyStreamDataReport" jelentést. Bal oldali navigációs ablaktábláján a jelentések szakaszban látható "EnergyStreamDataReport" nevű jelentés.
   * Kattintson a **"PIN-kód Visual"** ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) ikonra a jobb felső sarokban található ezen a vonaldiagramon, a "Rögzítés az irányítópulton" ablak előfordulhat, hogy meg, hogy válasszon ki egy irányítópultot. Válassza a "EnergyStreamDataReport", majd kattintson a "PIN-kód".
   * Vigye az egérmutatót át a csempét az irányítópulton, kattintson a "Szerkesztés" annak címe időbélyegzőként"igény szerint" módosításához a jobb felső sarokban lévő ikonra
4. Egyéb irányítópult-csempék alapján a megfelelő adatkészletek létrehozása. Az utolsó irányítópult-nézet: ![](media/cortana-analytics-technical-guide-demand-forecast/PBIFullScreen.png)

### <a name="setup-cold-path-dashboard"></a>Ritka elérésű útvonal irányítópult beállítása
A ritka elérésű útvonal adatfolyamatok alapvető célja az, hogy minden egyes régió kereslet-előrejelzés. Power BI adatforrásként, az előrejelzési eredményeket tároló Azure SQL Database-adatbázishoz kapcsolódik.

> [!NOTE]
> 1) Elég előrejelzési eredményeket az irányítópult gyűjtéséhez néhány órát vesz igénybe. Azt javasoljuk, hogy a folyamat 2-3 óra után, az adatgenerálást delek megkezdéséhez. 2.) ebben a lépésben a szükséges előfeltétel, hogy töltse le és telepítse az ingyenes szoftvereket [Power BI desktop](https://powerbi.microsoft.com/desktop).
>
>

1. Az adatbázis hitelesítő adatainak lekérése.

   Szükséges **adatbázis-kiszolgáló nevét, adatbázisnév, felhasználónév és jelszó** áthelyezése a következő lépések előtt. Az alábbiakban útmutató, hogyan találhatja meg azokat a lépéseket.

   * Egyszer **"Azure SQL Database"** a megoldássablon az ábra a színe zöldre vált, kattintson rá, és kattintson a **"Nyitott"**. Megismerheti az Azure Portalra, és az adatbázis-információ lapon is megnyílik.
   * Az oldalon a "Adatbázis" szakaszban találja. Ki a létrehozott adatbázis sorolja fel. Az adatbázis neve legyen **"A megoldás neve + véletlenszerű szám +"db""** (például "mytest12345db").
   * Az adatbázishoz, kattintson a panel ki az új pop, annak az adatbázis-kiszolgáló neve a felső. Az adatbázis-kiszolgáló nevét kell `"Your Solution Name + Random Number + 'database.windows.net,1433'"` (például "mytest12345.database.windows.net,1433").
   * Az adatbázis **felhasználónév** és **jelszó** ugyanaz, mint a felhasználónév és jelszó korábban rögzíti a megoldás üzembe helyezése során.
2. Az adatforrás a ritka elérésű útvonal a Power BI-fájl frissítése

   * Győződjön meg arról, hogy telepítette a legújabb [Power BI desktop](https://powerbi.microsoft.com/desktop).
   * Az a **"DemandForecastingDataGeneratorv1.0"** letöltött mappát, kattintson duplán a **"Power BI Template\DemandForecastPowerBI.pbix"** fájlt. A kezdeti Vizualizációk helyőrző adatokon alapulnak. **Megjegyzés:** hibaüzenet jelenik meg, ha győződjön meg arról, hogy telepítette a Power BI Desktop legújabb verzióját.

     Kattintson a megnyitott, a fájl felső részén, **lekérdezések szerkesztése**. A kiugró ablakban kattintson duplán **"Forrás"** a jobb oldali panelen.
     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic1.png)
   * Cserélje le a kiugró ablakban **"Kiszolgáló"** és **"Adatbázis"** a saját kiszolgáló és az adatbázis nevét, és kattintson a **"OK"**. A kiszolgáló nevét, győződjön meg arról, adja meg az 1433-as porton (**YourSolutionName.database.windows.net, 1433-as**). Figyelmen kívül hagyhatja a figyelmeztetéseket, a képernyőn megjelenő.
   * A következő kiugró ablakban, a két lehetőség a bal oldali panelen láthatja (**Windows** és **adatbázis**). Kattintson a **"Adatbázis"**, töltse ki a **"Felhasználónév"** és **"Password"** (azt a felhasználónevet és jelszót, amikor először üzembe helyezte a megoldást és létrehozott egy Azure SQL-adatbázis). A ***válassza ki, melyik szintre legyenek érvényesek a beállítások***, ellenőrizze az adatbázis-szintű beállítás. Kattintson a **"Csatlakozás"**.
   * Miután, interaktív van az előző oldalra, zárja be az ablakot. Felskálázás-– kattintson egy üzenet POP **alkalmaz**. Végül kattintson a **mentése** gombra kattintva mentse a módosításokat. A Power BI-fájl most létesített kapcsolat a kiszolgálóval. Ha a Vizualizációk üres, győződjön meg arról, törölje a jelet a választott beállításokat, a Vizualizációk adatok megjelenítése a jelmagyarázat jobb felső sarkában a radír ikonra kattintva. A frissítés gomb használatával új adatokat tükrözik a vizualizációt. Kezdetben csak látható a kezdőérték-adatok a Vizualizációk az adat-előállító 3 óránként ütemezett módon. 3 óra múlva megjelennek a Vizualizációk az adatok frissítésekor új előrejelzéseket láthatja.
3. (Nem kötelező) A ritka elérésű útvonal irányítópult közzététele [online a Power BI](https://www.powerbi.com/). Vegye figyelembe, hogy ezt a lépést kell egy Power BI-fiók (vagy Office 365-fiókkal).

   * Kattintson a **"Közzététel"** és néhány másodperc múlva megjelenik egy ablak, "Közzététel a Power BI sikeres!" megjelenítése egy zöld pipának. A következő hivatkozás "A Power bi-ban megnyitott demoprediction.pbix" gombra. Részletes utasításokért lásd: [közzététel a Power BI Desktopból](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Egy új irányítópult létrehozását: kattintson a **+** melletti jelentkezzen a **irányítópultok** szakaszban a bal oldali panelen. Adja meg az új irányítópult nevét "Kereslet-előrejelzés bemutató".
   * Amikor megnyitja a jelentést, kattintson a ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) rögzítése az irányítópulton a vizualizációkat. Részletes utasításokért lásd: [csempe rögzítése Power BI-irányítópultra egy jelentésből](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Nyissa meg az irányítópult-oldalon és a méret- és a Vizualizációk és szerkessze a címben. Részletes útmutatás a csempék szerkesztésére vonatkozó információért lásd: [szerkesztése – átméretezés, áthelyezés, átnevezés, PIN-kód, csempe törlés, hivatkozás hozzáadása](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Íme egy példa irányítópult egyes ritka elérésű útvonal vizualizációkkal rögzítve rajta.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic7.png)
4. (Nem kötelező) Az adatforrás-frissítés ütemezése.

   * Frissítés ütemezéséhez az adatokat, az egérmutatóval rámutat a **EnergyBPI végleges** adatkészletet, kattintson a ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic3.png) majd **frissítés ütemezése**.
     **Megjegyzés:** Ha megjelenik egy figyelmeztetés masszírozó, kattintson a **hitelesítő adatok szerkesztése** , és győződjön meg arról, hogy az adatbázis hitelesítő adatai ugyanazok, mint az 1. lépésben leírt.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic4.png)
   * Bontsa ki a **frissítés ütemezése** szakaszban. Kapcsolja be "tartsa adatait naprakészen".
   * A frissítés ütemezése az igényei szerint. További információkért lásd: [Adatfrissítés a Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).

## <a name="how-to-delete-your-solution"></a>**A megoldás törlése**
Győződjön meg arról, hogy leállítsa az adatgenerálást amikor nem használja aktívan a megoldást, az adatgeneráló költsége magasabb szintre. Törölje a megoldást, ha nem használ. A megoldás törlésekor a megoldás üzembe helyezésekor az előfizetésben kiépített összes összetevőt. Törölje a megoldást kattintson a megoldás nevét a megoldássablon, majd kattintson a törlés, a bal oldali panelen.

## <a name="cost-estimation-tools"></a>**Költségek becslése eszközök**
A következő két eszközök segítségével jobban megismerheti az a teljes költség a kereslet-előrejelzés energia Megoldássablon az előfizetésében futó részt érhetők el:

* [A Microsoft Azure Cost Estimator eszközt (online)](https://azure.microsoft.com/pricing/calculator/)
* [A Microsoft Azure Cost Estimator eszközt (asztali verzió)](https://www.microsoft.com/download/details.aspx?id=43376)

## <a name="acknowledgements"></a>**Nyugtázás**
Ez a cikk adattudós JI Chen és Szoftvermérnök Qiu perc, a Microsoft hozta létre.
