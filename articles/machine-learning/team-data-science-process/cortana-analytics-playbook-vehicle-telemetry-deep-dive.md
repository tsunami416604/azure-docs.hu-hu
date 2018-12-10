---
title: Részletes bemutató ismerteti, hogyan lehet előrejelezheti állapotát és a vezetési szokásokat – csoportos adatelemzési folyamat
description: Részletezhet minden egyes szakaszai a megoldás állapotát a valós idejű és prediktív feltárását, és a vezetési szokásokat.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 03/14/2018
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b8bd0286a2fa800dd2d5b8ac9c4df1a29809f7f9
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53142036"
---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Járműtelemetria-Telemetria elemzési megoldás forgatókönyve: részletes ismerje meg, a megoldás

Ez a cikk részletezi a megoldásarchitektúra fejezet fázisokhoz be. Útmutatás és testreszabási mutatók is. 

Ez a megoldás összefoglaló leírását áttekintéséhez lásd: [jármű-Telemetria elemzési megoldás forgatókönyve](cortana-analytics-playbook-vehicle-telemetry.md).


## <a name="data-sources"></a>Adatforrások
A megoldás két különböző adatforrásból használja:

* Szimulált jármű jelek és diagnosztikai adatok beállítása
* Járműtelemetria-katalógus

Jármű-telematika szimulátort Ez a megoldás része, az alábbi képernyőképen látható módon. Ez bocsát ki a diagnosztikai adatokat, és azt jelzi, hogy felel meg a jármű állapotát, és a vezetési szokásokkal adott időpontban.  A járműtelemetria katalógus tartalmazza a referencia-adatkészlet, amely leképezi a vehicle azonosítószámokat (VINs) modellek. Megjegyzés: A Vehicle telematika szimulátor Visual Studio-megoldás adatkészlet már nem érhető el. 

![Jármű-telematika szimulátor](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)


A JSON-formátumú adatkészlet a következő sémát tartalmaz.

| Oszlop | Leírás | Értékek |
| --- | --- | --- |
| VIN |Véletlenszerűen létrehozott VIN |10 000 véletlenszerűen generált VINs fő listáját nyert |
| Külső hőmérséklet |Ahol a jármű okoznának külső hőmérséklet |0 – 100 véletlenszerűen előállított szám |
| Motor hőmérséklet |A motor hőmérséklet a jármű méretét |0 – 500 véletlenszerűen előállított szám |
| Sebesség |A motor fordulatszámát, amelyen a jármű okoznának |0 – 100 véletlenszerűen előállított szám |
| Ösztönzésének |A járműtelemetria ösztönzésének szintje |Véletlenszerűen előállított szám 0 és 100 (ösztönzésének százalékos értéke jelzi) |
| EngineOil |A motor olaj szint a jármű méretét |Véletlenszerűen előállított szám 0 és 100 (a motor olaj százalékos értéke jelzi) |
| Kulcsszava nyomás |A kulcsszava nyomás a jármű méretét |Véletlenszerűen előállított szám 0-tól 50-re (kulcsszava nyomás százalékos értéke jelzi) |
| Kilométer |A jármű méretét a kilométer olvasása |0 – 200 000 véletlenszerűen előállított szám |
| Accelerator_pedal_position |A járműtelemetria gyorsító kerékpárok pozíciója |Véletlenszerűen előállított szám 0 és 100 (gyorsító százalékos értéke jelzi) |
| Parking_brake_status |Azt jelzi, hogy a jármű kell itt tartózkodnia vagy nem |TRUE vagy FALSE (hamis) |
| Headlamp_status |Azt jelzi, hogy a fényszóró van-e szó vagy sem |TRUE vagy FALSE (hamis) |
| Brake_pedal_status |Azt jelzi, hogy a fékpedál megnyomják, vagy nem |TRUE vagy FALSE (hamis) |
| Transmission_gear_position |A járműtelemetria átviteli fogaskerék pozíciója |Állapotok: először, a második, harmadik, negyedik ötödik, hatodik, hetedik, nyolcadik |
| Ignition_status |Azt jelzi, hogy a jármű fut vagy leállítva |TRUE vagy FALSE (hamis) |
| Windshield_wiper_status |Azt jelzi, hogy a szélvédőkeret ablaktörlő be van kapcsolva vagy nem |TRUE vagy FALSE (hamis) |
| ABS |Azt jelzi, hogy ABS bevonhat vagy sem |TRUE vagy FALSE (hamis) |
| Időbélyeg |Az időbélyeg, ha az adatpont jön létre |Dátum |
| Város |A járműtelemetria helye |Ez a megoldás négy városok: Bellevue, Redmond, Sammamish, Seattle |

A járműtelemetria modell referencia-adatkészlet VINs modellek rendeli hozzá. 

| VIN | Modell |
| --- | --- |
| FHL3O1SA4IEHB4WU1 |Sedan |
| 8J0U8XCPRGW4Z3NQE |Hibrid |
| WORG68Z2PLTNZDBI7 |Családi limuzin |
| JTHMYHQTEPP4WBMRN |Sedan |
| W9FTHG27LZN1YWO0Y |Hibrid |
| MHTP9N792PHK08WJM |Családi limuzin |
| EI4QXI2AXVQQING4I |Sedan |
| 5KKR2VB4WHQH97PF8 |Hibrid |
| W9NSZ423XZHAONYXB |Családi limuzin |
| 26WJSGHX4MA5ROHNL |Váltható |
| GHLUB6ONKMOSI7E77 |Állomás kocsi |
| 9C2RHVRVLMEJDBXLP |Kompakt autó |
| BRNHVMZOUJ6EOCP32 |Kis SUV |
| VCYVW0WUZNBTM594J |Sport autó |
| HNVCE6YFZSA5M82NY |Közepes SUV |
| 4R30FOR7NUOBL05GJ |Állomás kocsi |
| WYNIIY42VKV6OQS1J |Nagy SUV |
| 8Y5QKG27QET1RBK7I |Nagy SUV |
| DF6OX2WSRA6511BVG |Coupe |
| Z2EOZWZBXAEW3E60T |Sedan |
| M4TV6IEALD5QDS3IR |Hibrid |
| VHRA1Y2TGTA84F00H |Családi limuzin |
| R0JAUHT1L1R3BIKI0 |Sedan |
| 9230C202Z60XX84AU |Hibrid |
| T8DNDN5UDCWL7M72H |Családi limuzin |
| 4WPYRUZII5YV7YA42 |Sedan |
| D1ZVY26UV2BFGHZNO |Hibrid |
| XUF99EW9OIQOMV7Q7 |Családi limuzin |
| 8OMCL3LGI7XNCC21U |Váltható |
| ……. | |

## <a name="ingestion"></a>Adatbetöltési
Az Azure Event Hubs, az Azure Stream Analytics és az Azure Data Factory kombinációit használt betöltését járműtelemetria jelek, a diagnosztikai események, és valós idejű és kötegelt elemzés. Ezek az összetevők létrehozni és konfigurálni a megoldás központi telepítésének részeként. 

### <a name="real-time-analysis"></a>Valós idejű elemzése
Az event hubs SDK használatával a jármű-telematika szimulátor által létrehozott eseményeket az eseményközpontba lettek közzétéve.  

![Event hub-irányítópult](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

A Stream Analytics-feladat feltölti ezeket az eseményeket az eseményközpontból, és feldolgozza az adatokat valós időben elemezheti az állapotát.

![Stream Analytics-feladat adatainak feldolgozása](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 


A Stream Analytics-feladatot:

* Betölti az adatokat az eseményközpontból.
* Egy összekapcsolás a jármű VIN leképezése a megfelelő modellt a referenciaadatok hajt végre. 
* Gazdag kötegelt elemzésre az Azure Blob storage-bA fenntartása őket. 

A következő Stream Analytics-lekérdezés szolgál a szeretné megtartani az adatokat Blob storage-bA: 

![Stream Analytics-Feladatlekérdezés adatbetöltés](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 


### <a name="batch-analysis"></a>Batch-elemzés
Egy szimulált jármű jelek és diagnosztikai adatok további kötet is létrejön a részletesebb kötegelt feldolgozáshoz. Ehhez a kötethez szükség a kötegelt feldolgozáshoz jó reprezentatív adatmennyiség biztosítása érdekében. Erre a célra PrepareSampleDataPipeline szolgál a Data Factory-munkafolyamat egy év alatt az szimulált jármű jelek és diagnosztikai adatkészlet létrehozásához. Töltse le a Data Factory egyéni .NET-tevékenységet a testreszabások igényei alapján a Visual Studio-megoldást, keresse fel a [Data Factory egyéni tevékenység](https://go.microsoft.com/fwlink/?LinkId=717077) weblapon. 

Ez a munkafolyamat bemutatja a kötegelt feldolgozáshoz előkészített mintaadatok.

![A mintaadatok előkészítve a kötegelt feldolgozási munkafolyamat](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 


A folyamatot a Data Factory .NET egyéni tevékenység áll.

![PrepareSampleDataPipeline tevékenység](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

Miután a folyamat végrehajtása sikeresen megtörtént, és a RawCarEventsTable data set "Kész" van megjelölve, egy teljes év alatt az szimulált jármű jelek és diagnosztikai adatainak előállítása. A következő mappa és fájl létrehozásakor a storage-fiókban az connectedcar tároló alatt jelenik meg:

![PrepareSampleDataPipeline kimenet](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

## <a name="partition-the-data-set"></a>Az adatkészlet partíció
Az adat-előkészítési lépésként a nyers félig strukturált jármű jelek és diagnosztikai adatkészlet egy év és hónap formátumra particionálva. Ezt a particionálása elősegíti a hatékonyabb lekérdezés és a méretezhető, hosszú távú tárolás tartalék feladatátvételi engedélyezésével. Például az első blob fiók megtelik, ahogy azt hibákat keresztül a következő fiókhoz. 

>[!NOTE] 
>Ebben a lépésben a megoldás csak a kötegelt feldolgozás vonatkozik.

Bemeneti és kimeneti adatok kezelése:

* **Kimeneti adatok** (címkézett PartitionedCarEventsTable) egy hosszú ideig tartott a data lake a vásárlói adatok eligazodást / "rawest" képernyő. 
* **Bemeneti adatok** Ez a folyamat a rendszer általában vetve, mert a kimeneti adatokat rendelkezik teljes visszaadása a bemeneti. Tárolt (particionált) jobban későbbi használatra.

A partíció autó események munkafolyamat.

![Partíció autó események munkafolyamat](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)


A nyers adatok PartitionCarEventsPipeline, egy Azure HDInsight Hive-tevékenység használatával van particionálva, az alábbi képernyőképen látható módon. A mintaadatokat, egy egymást követő évben az adat-előkészítési lépésként a generált particionálva van, év és hónap szerint. A partíciók az egyes hónapok (összesen 12 partícióra) év jármű jelek és diagnosztikai adatok létrehozásához használt. 

![PartitionCarEventsPipeline tevékenység](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)


**PartitionConnectedCarEvents Hive-parancsfájl**

A Hive-parancsfájl partitioncarevents.hql particionálására szolgál. A letöltött zip-fájlban \demo\src\connectedcar\scripts mappában található. 
    
    SET hive.exec.dynamic.partition=true;
    SET hive.exec.dynamic.partition.mode = nonstrict;
    set hive.cli.print.header=true;

    DROP TABLE IF EXISTS RawCarEvents; 
    CREATE EXTERNAL TABLE RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string
    ) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

    DROP TABLE IF EXISTS Stage_RawCarEvents; 
    CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string,
                YearNo                             int,
                MonthNo                         int) 
    ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

    INSERT OVERWRITE TABLE Stage_RawCarEvents
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        Year(gendate),
        Month(gendate)

    FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

    INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        YearNo,
        MonthNo
    FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

A folyamat sikeres végrehajtása után a következő partíciókhoz létrehozott storage-fiókjában az connectedcar tároló alatt jelenik meg:

![A particionált kimenet](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

Az adatok most már optimalizálva, könnyebben kezelhető, és készen áll a batch részletes információkhoz juthat a további feldolgozás céljából. 

## <a name="data-analysis"></a>Adatelemzés
Ebben a szakaszban bemutatjuk, hogyan kombinálhatja a Stream Analytics, az Azure Machine Learning, a Data Factory és a HDInsight a gazdag bővített analitikához állapotát, és a vezetési szokásokat.

### <a name="machine-learning"></a>Gépi tanulás
A cél, hogy a karbantartás, illetve bizonyos heath statisztikák, a következő Előfeltevések alapján alapján visszaírási igénylő járművek előrejelzése:

* A következő három feltétel valamelyike teljesül, a járművek van szükség a karbantartáshoz karbantartási:
  
  * A kulcsszava nyomás értéke alacsony.
  * A motor olaj szint értéke alacsony.
  * A motor hőmérséklete túl magas.

* Ha a következő feltételek valamelyike teljesül, a járművek előfordulhat, hogy rendelkezik egy biztonsági kérdés és visszaírási megkövetelése:
  
  * A motor hőmérséklete túl magas, de a külső hőmérséklet alacsony.
  * A motor hőmérséklet alacsony, de a külső hőmérséklet túl magas.

Az előző követelmények alapján, két külön modell észlelje a rendellenességeket. Jármű karbantartási észlelési van egy modellt, és egy modell jármű visszaírási észleléséhez. Mindkét modellben a beépített egyszerű összetevő elemzés (PEM) algoritmus rendellenesség-észlelést szolgál. 

#### <a name="maintenance-detection-model"></a>**Modell karbantartása**

Ha egy három mutatók--kulcsszava nyomás, motor olaj vagy motor hőmérséklet--eleget tesz a megfelelő feltétel, a karbantartási modell egy rendellenességet jelenti. Ennek eredményeképpen csak ezeket a három változókat kell figyelembe kell venni a modellkészítés kell. A kísérletben a machine learning a **Select Columns in Dataset** modul segítségével csomagolja ki ezeket a változókat. Ezután a PCA-alapú feltételészlelési modul szolgál a modell létrehozásához a rendellenességek észlelését. 

PEM a egy meghatározott technika, a machine learning szolgáltatás kiválasztása, a besorolást és a rendellenességek észlelése alkalmazható. PEM esetekben valószínűleg korrelált változók be egy értékhalmazt nevű fő összetevőit tartalmazó készletét alakítja át. Modellezési PCA-alapú kulcs lényege, projekt adatokhoz egy alacsonyabb dimenziós területére könnyebben azonosíthatja a funkciók és a rendellenességeket.

Minden egyes új bevitel, a modell esetén az anomáliadetektálási detector használatával először kiszámítja a leképezés a eigenvectors a. Majd kiszámítja a normalizált reconstruction hiba. Ez a normalizált hiba az anomáliadetektálási pontszám: minél nagyobb a hiba, a több rendellenes a példányt. 

A karbantartási észlelési probléma minden rekord kulcsszava nyomás motor olaj és motor által meghatározott egy pont háromdimenziós szóközzel koordináták számít. Rögzítheti a rendellenességeket, PEM szolgál az háromdimenziós helyre az eredeti adatok projekt egy kétdimenziós területére. Összetevők PEM paraméter számát, a két értékre van állítva. Ez a paraméter a PCA-alapú anomáliadetektálás alkalmazása fontos szerepet játszik. Miután PEM projektadatokat, ezeket a rendellenességek azonosítása könnyebben.

#### <a name="recall-anomaly-detection-model"></a>**Idézze anomáliadetektálási modell**

A visszaírási rendellenességek észlelése modellben a **Select Columns in Dataset** és hasonló módon PCA-alapú rendellenességek észlelése modulok használják. Pontosabban, a három változók – motor hőmérséklete, kívül először használatával vonjuk hőmérséklet és sebesség--a **Select Columns in Dataset** modul. Sebesség változó a csomagban foglalt, mert a motor hőmérséklet általában ad eredményül, amelyek a sebesség. Ezután a PCA-alapú feltételészlelési modul szolgál az adatok a térbeli területről projekt egy kétdimenziós területére. A visszaírási feltételek teljesülnek. A vehicle visszaírási szükséges, ha a rendkívül negatív korrelált a motor hőmérséklet és külső hőmérséklet. PEM hajtja végre, miután a PCA alapú anomáliaészlelő létrehozására használható a rendellenességeket. 

Mindkét modell betanításakor normális adatokká szolgál a bemeneti adatokat a PCA-alapú modell betanításához. (Normál adatok nem igényel karbantartást vagy visszahívás.) A pontozó kísérletben az anomáliadetektálási betanított modell annak észlelésére használnak, a vehicle szükség van-e karbantartási vagy visszahívás. 

### <a name="real-time-analysis"></a>Valós idejű elemzése
A következő Stream Analytics SQL-lekérdezés beolvasni az összes fontos jármű paramétert átlaga szolgál. Ezeket a paramétereket például jármű sebességét, ösztönzésének szint, motor hőmérséklet, kilométer olvasó, kulcsszava nyomás, motor olaj szintje és mások. Az átlagokat észlelje a rendellenességeket, adja ki a riasztások és egy adott régióban lévő üzemeltetett járművek általános állapotának feltételek meghatározására szolgálnak. A átlagok, demográfiai majd közötti kapcsolatot. 

![Stream Analytics-lekérdezés valós idejű feldolgozás](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Az összes átlagokat keresztül egy három másodperces átfedésmentes ablak számítjuk ki. Átfedésmentes ablak szolgál, mert nem fedő és összefüggő időintervallumok szükség. 

A Stream Analytics ablakkezelési képességeivel kapcsolatos további tudnivalókért lásd: [Ablakkezelési (az Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

#### <a name="real-time-prediction"></a>**Valós idejű előrejelzése**

Egy alkalmazás a valós idejű gépi tanulási modell üzembe helyezése a megoldás része. Az alkalmazás RealTimeDashboardApp létrehozta és konfigurálta a megoldás központi telepítésének részeként. Az alkalmazás:

* Az event hubs-példánnyal, ahol Stream Analytics közzéteszi az események a mintázat folyamatosan figyeli.

    ![Az adatok közzététele a Stream Analytics-lekérdezés](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) 

* Fogadja az eseményeket. Minden eseményhez, amely megkapja ezt az alkalmazást: 
   
   * Az adatok feldolgozása a machine learning-kérés-válasz pontozási (RRS) végponthoz. Az RRS-végpontot automatikusan a telepítés részeként van közzétéve.
   * A Power BI-adatkészlet az RRS kimeneti a push API-k használatával van közzétéve.

Ez a minta akkor is, amelyben egy sor üzleti alkalmazás a valós idejű elemzési folyamatával integrálni kívánt alkalmazási helyzeteket alkalmazható. Ezek a forgatókönyvek közé tartozik a riasztások, értesítések és üzenetkezelés.

Megjegyzés:, hogy a RealtimeDashboardApp Visual Studio-megoldás az adatok már nem érhető el.

#### <a name="execute-the-real-time-dashboard-application"></a>**A valós idejű irányítópult alkalmazás végrehajtása**
1. Bontsa ki a RealtimeDashboardApp, és mentse helyileg.

    ![RealTimeDashboardApp folder](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) 

2. Hajtsa végre az alkalmazás RealtimeDashboardApp.exe.

3. Adja meg a Power BI érvényes hitelesítő adatait, és válassza ki **jelentkezzen be a**.  

    ![Valós idejű irányítópult app bejelentkezési ablak](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 
    
4. Válassza ki az **Elfogadás** lehetőséget.

    ![Valós idejű irányítópult app bejelentkezési ablaka](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

>[!NOTE] 
>Ha azt szeretné, a Power BI adatkészletében kiüríteni, hajtsa végre a RealtimeDashboardApp "flushdata" paraméterrel. 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>Batch-elemzés
Itt az célja, hogy hogyan Contoso Motors már használja az Azure-beli számítási funkcióit használja ki a big Data típusú adatok megjelenítése. Ezek az adatok tárja fel a részletes információk a környezetbarát minták, viselkedésébe és állapotát. Ez az információ lehetővé teszi:

* A felhasználói élmény javítása érdekében, és adja meg olcsóbb és a vezetési szokásokat és tíz a környezetbarát viselkedés alapján.
* Ismerje meg proaktív módon ügyfelek és a vezetési környezeteken szabályozzák az üzleti döntéseket hozhat, és adja meg a legjobb minőségű termékeket és szolgáltatásokat.

Ebben a megoldásban a következő metrikák célozzák meg:

* **Agresszív viselkedés vezetési**: azonosítja a trendjét a modelleket, helyek, vezetői feltételek és idő év következtetések levonásához agresszív környezetbarát mintáknak megfelelően. Contoso Motors ezek insights segítségével a marketingkampányok új személyre szabott szolgáltatások és a használat alapú biztosítási.
* **Tíz a környezetbarát viselkedés**: azonosítja a modelleket, helyek, vezetői feltételek és idő év tíz a környezetbarát minták elemzése trendjét. Contoso Motors ezeket a marketingkampányokban insights segítségével vezeti be új szolgáltatásokat és a proaktív jelentő illesztőprogramjait költséghatékony és a környezetbarát vezetési szokásokat.
* **Modellek visszahívása**: visszaírás által a rendellenességek észlelése a machine learning-kísérlet modellezést igénylő modelleken azonosítja.

Vizsgáljuk meg ezeket a metrikákat részleteit.

#### <a name="aggressive-driving-behavior-patterns"></a>**Agresszív környezetbarát viselkedési mintáinak**

A particionált jármű jelek és diagnosztikai adatok feldolgozása történik AggresiveDrivingPatternPipeline, ahogyan az az alábbi munkafolyamat. Hive a modellek, hely, jármű, üzemi és más paramétereket, amelyeket agresszív környezetbarát minták meghatározására szolgál.

![Agresszív környezetbarát minta munkafolyamat](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 

***Agresszív környezetbarát minta Hive-lekérdezés***

A Hive-parancsfájl aggresivedriving.hql agresszív környezetbarát feltétel mintáinak elemzése szolgál. A letöltött zip-fájlban \demo\src\connectedcar\scripts mappában található. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS CarEventsAggresive; 
    CREATE EXTERNAL TABLE CarEventsAggresive
    (
                   vin                         string, 
                model                        string,
                timestamp                    string,
                city                        string,
                speed                          string,
                transmission_gear_position    string,
                brake_pedal_status            string,
                Year                        string,
                Month                        string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



    INSERT OVERWRITE TABLE CarEventsAggresive
    select
    vin,
    model,
    timestamp,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'


A szkript egy jármű átviteli fogaskerék pozícióját, berendezés kerékpárok állapotát és sebesség kombinációja alapján nagy sebességű minták fékezés reckless/agresszív környezetbarát viselkedés észlelését. 

A folyamat sikeres végrehajtása után a következő partíciókhoz létrehozott storage-fiókjában az connectedcar tároló alatt jelenik meg:

![AggressiveDrivingPatternPipeline kimenet](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 


#### <a name="fuel-efficient-driving-behavior-patterns"></a>**Tíz a környezetbarát viselkedési mintáinak**

A particionált jármű jelek és diagnosztikai adatok feldolgozása történik FuelEfficientDrivingPatternPipeline, ahogyan az az alábbi munkafolyamat. Hive a modellek, hely, jármű, üzemi és egyéb tulajdonságok, amelyeket tíz a környezetbarát minták meghatározására szolgál.

![Tíz a környezetbarát minták](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

***Tíz környezetbarát minta Hive-lekérdezés***

Tíz a környezetbarát feltétel mintáinak elemzése a Hive-parancsfájl fuelefficientdriving.hql szolgál. A letöltött zip-fájlban \demo\src\connectedcar\scripts mappában található. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS FuelEfficientDriving; 
    CREATE EXTERNAL TABLE FuelEfficientDriving
    (
                   vin                         string, 
                model                        string,
                   city                        string,
                speed                          string,
                transmission_gear_position    string,                
                brake_pedal_status            string,            
                accelerator_pedal_position    string,                             
                Year                        string,
                Month                        string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



    INSERT OVERWRITE TABLE FuelEfficientDriving
    select
    vin,
    model,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    accelerator_pedal_position,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


A parancsfájl egy jármű átviteli fogaskerék pozíció kombinációját használja, a berendezés kerékpárok állapot, a sebesség és a gyorsító pedál pozíció alapján gyorsítás, fékezés, tíz környezetbarát viselkedés észlelését, és gyorsan mintákat. 

A folyamat sikeres végrehajtása után a következő partíciókhoz létrehozott storage-fiókjában az connectedcar tároló alatt jelenik meg:

![FuelEfficientDrivingPatternPipeline kimenet](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

**Modell-előrejelzéseket visszahívása**

A machine learning-kísérlet kiépített és közzététele webszolgáltatásként, amely a megoldás központi telepítésének részeként. A kötegelt pontozási végpontjához használatban van ebben a munkafolyamatban. A data factoryhoz társított szolgáltatásként regisztrált, és üzembe helyezte azt a data factory a kötegelt pontozási tevékenység használatával.

![Machine learning-végpont](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

A regisztrált kapcsolódó szolgáltatáshoz a rendellenességek észlelése modell segítségével az adatok pontozásához DetectAnomalyPipeline szerepel. 

![Machine learning kötegelt pontozási tevékenységek a data factoryban](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png)  

Néhány lépést, hogy azt is kell üzembe helyezte azt a kötegelt pontozási webszolgáltatás ebben az adatcsatornában az adat-előkészítési történik. 

![A visszaírási előrejelzési DetectAnomalyPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png)  

***Rendellenességek észlelése Hive-lekérdezés***

Ha a pontozás befejeződött, egy HDInsight-tevékenység feldolgozza, és összesíti az adatokat, amelyek a modell kategorizált megtalálható rendellenességek formájában. A modell egy valószínűségi pontszámának 0.60 vagy magasabb használ.

    DROP TABLE IF EXISTS CarEventsAnomaly; 
    CREATE EXTERNAL TABLE CarEventsAnomaly 
    (
                vin                            string,
                model                        string,
                gendate                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                fuel                        string,
                engineoil                    string,
                tirepressure                string,
                odometer                    string,
                city                        string,
                accelerator_pedal_position    string,
                parking_brake_status        string,
                headlamp_status                string,
                brake_pedal_status            string,
                transmission_gear_position    string,
                ignition_status                string,
                windshield_wiper_status        string,
                abs                          string,
                maintenanceLabel            string,
                maintenanceProbability        string,
                RecallLabel                    string,
                RecallProbability            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

    DROP TABLE IF EXISTS RecallModel; 
    CREATE EXTERNAL TABLE RecallModel 
    (

                vin                            string,
                model                        string,
                city                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                Year                        string,
                Month                        string                

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

    INSERT OVERWRITE TABLE RecallModel
    select
    vin,
    model,
    city,
    outsidetemperature,
    enginetemperature,
    speed,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from CarEventsAnomaly
    where RecallLabel = '1' AND RecallProbability >= '0.60'


A folyamat sikeres végrehajtása után a következő partíciókhoz létrehozott storage-fiókjában az connectedcar tároló alatt jelenik meg:

![DetectAnomalyPipeline kimenet](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

## <a name="publish"></a>Közzététel

### <a name="real-time-analysis"></a>Valós idejű elemzése
A lekérdezések a Stream Analytics-feladat az egyik tesz közzé az eseményeket egy kimeneti event hubs-példánnyal. 

![Stream Analytics-feladat közzétett egy kimeneti event hubs-példánnyal](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

A következő Stream Analytics-lekérdezés a kimeneti event hubs-példánnyal közzétételére szolgál:

![Stream Analytics-lekérdezés a kimeneti event hubs-példánnyal közzététele](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

Az események a stream a RealTimeDashboardApp a megoldás részét képező használja fel. Ez az alkalmazás a machine learning-webszolgáltatást kérés-válasz valós idejű pontozási használ. Az így kapott adatok a Power BI-adatkészletbe felhasználásra tesz közzé. 

### <a name="batch-analysis"></a>Batch-elemzés
A kötegelt és valós idejű feldolgozás eredményét az Azure SQL Database-táblák felhasználásra kerülnek közzétételre. Az SQL server, az adatbázis és a táblázatok jönnek létre automatikusan a telepítési parancsfájl részeként. 

A kötegelt feldolgozási eredmények az Adatközpont munkafolyamat lesz másolva.

![Kötegelt feldolgozás az Adatközpont munkafolyamat másolt eredmények](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

A Stream Analytics-feladat az adatpiac van közzétéve.

![Stream Analytics-feladat közzétett adatpiac](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

Az adatok adatközpont érték a Stream Analytics-feladatban.

![A Stream Analytics-feladatban Data adatközpont beállítása](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

## <a name="consume"></a>Felhasználás
A Power BI egy informatív irányítópulton biztosít a megoldás a valós idejű adatok és prediktív elemzési vizualizációt. 

Az utolsó irányítópult példához hasonlóan néz ki:

![Power BI-irányítópult](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

## <a name="summary"></a>Összegzés
Ez a dokumentum tartalmazza a Vehicle Telemetriai Analytics megoldás részletes részletezése. A lambda architektúra a minta használható valós idejű és kötegelt analytics, az előrejelzéseket és műveleteket. Gyakori elérésű útvonal (valós idejű) igénylő alkalmazási helyzetek széles skáláját vonatkozik ez a minta és a ritka elérésű útvonal (kötegelt) analytics. 

### <a name="references"></a>Referencia

* [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
* [Azure Data Factory](https://docs.microsoft.com/rest/api/datafactory/)
* [Streamek feldolgozása az Azure Event Hubs SDK](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Az Azure Data Factory adat adatátviteli szolgáltatás](../../data-factory/copy-activity-overview.md)
* [Az Azure Data Factory .NET-tevékenység](../../data-factory/transform-data-using-dotnet-custom-activity.md)
* [Azure Data Factory .NET-tevékenység a Visual Studio megoldásnak a mintaadatok létrehozása](https://go.microsoft.com/fwlink/?LinkId=717077) 

## <a name="next-steps"></a>További lépések

A Power BI-jelentések és irányítópultok a megoldás konfigurálása kapcsolatban lásd: [jármű Telemetriai Analytics megoldás sablon Power BI-irányítópult beállítási útmutatásai](cortana-analytics-playbook-vehicle-telemetry-powerbi.md).
