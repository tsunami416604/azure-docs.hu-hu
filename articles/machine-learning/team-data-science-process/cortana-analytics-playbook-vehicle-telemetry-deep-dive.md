---
title: "Ismerteti, hogyan lehet vehicle állapotát, és ki irányítja az előrejelzése részletes bemutatója a szokásokat - Azure |} Microsoft Docs"
description: "A Cortana Intelligence szolgáltatásai segítségével a vehicle állapotát, és ki irányítja a valós idejű és prediktív dcu szokásokat."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d8866fa6-aba6-40e5-b3b3-33057393c1a8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: cdde0c8dc2fd1189970c0782769a609ca8142372
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Vehicle Telemetriai elemzési megoldások forgatókönyv: mély alaposabban tanulmányozhatja a megoldás
Ez a forgatókönyv szakaszait menü hivatkozásokat: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Ez a dokumentum részletezi az a megoldásarchitektúra írja le szakaszok azokat. Útmutatás és testreszabási mutatók tartoznak. 

## <a name="data-sources"></a>Adatforrások
A megoldás két különböző adatforrásból használ:

* Szimulált vehicle jelek és diagnosztikai adatkészlet
* Vehicle katalógus

A vehicle telematika szimulátor Ez a megoldás részét, az alábbi képernyőfelvételen látható módon. Általa kibocsátott diagnosztikai adatokat, és azt jelzi, hogy felel meg a vehicle állapotát, és a vezetői típusnak egy időben. A Vehicle telematika szimulátor Visual Studio megoldás a követelmények alapján testreszabni letöltéséhez keresse fel a [Vehicle telematika szimulátor](http://go.microsoft.com/fwlink/?LinkId=717075) weblap. A vehicle katalógus, amely leképezhető vehicle azonosítószámok (VINs) modellek hivatkozás adatkészlet tartalmazza.

![Vehicle telematika szimulátor](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)


A JSON-formátumú adatkészlet a következő sémát tartalmaz.

| Oszlop | Leírás | Értékek |
| --- | --- | --- |
| VIN |Véletlenszerűen létrehozott VIN |10 000 véletlenszerűen létrehozott VINs fő listájának nyert |
| Külső hőmérséklet |A külső hőmérséklet, ahol a vehicle befolyásoló tényezők |0 és 100 véletlenszerűen létrehozott szám |
| Motor |A vehicle motor hőmérséklete |0 és 500 véletlenszerűen létrehozott szám |
| Gyorsaság |A motor sebesség, amellyel a vehicle befolyásoló tényezők |0 és 100 véletlenszerűen létrehozott szám |
| Üzemanyag |A vehicle üzemanyag szintje |Véletlenszerűen létrehozott szám 0 és 100 (üzemanyag százalékos értéke jelzi) |
| EngineOil |A vehicle motor olaj szintje |Véletlenszerűen létrehozott szám 0 és 100 (motor olaj százalékos értéke jelzi) |
| Kulcsszava nyomás |A vehicle kulcsszava nyomás |Véletlenszerűen létrehozott szám 0 és 50 (kulcsszava nyomás százalékos értéke jelzi) |
| Kilométer |A vehicle kilométer olvasása |0 és 200 000 véletlenszerűen létrehozott szám |
| Accelerator_pedal_position |A vehicle gyorsító tartásához lehetőleg keveset pozíciója |Véletlenszerűen létrehozott szám 0 és 100 (gyorsító százalékos értéke jelzi) |
| Parking_brake_status |Azt jelzi, hogy a vehicle kell itt tartózkodnia vagy sem |IGAZ vagy hamis |
| Headlamp_status |Azt jelzi, hogy a fényszóró meg vagy nem |IGAZ vagy hamis |
| Brake_pedal_status |Azt jelzi, hogy a fékpedál nélkül, vagy sem |IGAZ vagy hamis |
| Transmission_gear_position |A vehicle átviteli fogaskerék pozíciója |Állapotok: először, a második, harmadik, negyedik, ötödik, hatodik, hetedik, nyolcadik |
| Ignition_status |Azt jelzi, hogy a vehicle fut vagy leállt |IGAZ vagy hamis |
| Windshield_wiper_status |Azt jelzi, hogy a szélvédőkeret ablaktörlő be van kapcsolva vagy nem |IGAZ vagy hamis |
| ABS |Azt jelzi, hogy ABS részt vevő vagy sem |IGAZ vagy hamis |
| Időbélyeg |Az adatpont létrehozásakor időbélyege |Dátum |
| Város |A vehicle helye |Ebben a megoldásban négy városokat: Bellevue, Redmond, Sammamish, Seattle |

A vehicle modell hivatkozás adatkészlet VINs modellek rendeli hozzá. 

| VIN | Modell |
| --- | --- |
| FHL3O1SA4IEHB4WU1 |Sedan |
| 8J0U8XCPRGW4Z3NQE |Hibrid |
| WORG68Z2PLTNZDBI7 |Családbiztonsági limuzin |
| JTHMYHQTEPP4WBMRN |Sedan |
| W9FTHG27LZN1YWO0Y |Hibrid |
| MHTP9N792PHK08WJM |Családbiztonsági limuzin |
| EI4QXI2AXVQQING4I |Sedan |
| 5KKR2VB4WHQH97PF8 |Hibrid |
| W9NSZ423XZHAONYXB |Családbiztonsági limuzin |
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
| VHRA1Y2TGTA84F00H |Családbiztonsági limuzin |
| R0JAUHT1L1R3BIKI0 |Sedan |
| 9230C202Z60XX84AU |Hibrid |
| T8DNDN5UDCWL7M72H |Családbiztonsági limuzin |
| 4WPYRUZII5YV7YA42 |Sedan |
| D1ZVY26UV2BFGHZNO |Hibrid |
| XUF99EW9OIQOMV7Q7 |Családbiztonsági limuzin |
| 8OMCL3LGI7XNCC21U |Váltható |
| ……. | |

## <a name="ingestion"></a>Adatfeldolgozást
Azure Event Hubs, az Azure Stream Analytics és az Azure Data Factory kombinációi használt tölti be a vehicle jeleket, a diagnosztikai események és valós idejű és kötegelt elemzés. Ezek az összetevők létrehozni és konfigurálni a megoldás központi telepítésének részeként. 

### <a name="real-time-analysis"></a>Valós idejű elemzés
Az event hubs SDK használatával a vehicle telematika szimulátor által előállított eseményeket az eseményközpontba lettek közzétéve.  

![Event hub irányítópult](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

A Stream Analytics-feladat ingests ezeket az eseményeket az eseményközpontból, és feldolgozza a valós idejű elemzése a vehicle egészségügyi adatokat.

![A Stream Analytics-feladat adatainak feldolgozása](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 


A Stream Analytics-feladatot:

* Az event hubs adatait ingests.
* A vehicle VIN hozzárendelése a megfelelő modell referenciaadatokkal illesztést hajt végre. 
* Az Azure Blob-tároló gazdag kötegelt elemzés fenntartása őket. 

A következő Stream Analytics-lekérdezés segítségével az adatok megőrzéséhez a Blob-tároló: 

![Stream Analytics feladat lekérdezés adatfeldolgozást](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 


### <a name="batch-analysis"></a>Kötegelt elemzés
Gazdagabb kötegelt elemzés is létrejön egy új kötetet szimulált vehicle jelek és diagnosztikai adatokat. A további kötet annak érdekében szükség egy jó reprezentatív adatmennyiség kötegelt feldolgozásra. Erre a célra PrepareSampleDataPipeline használatos a Data Factory-munkafolyamat egy év alatt érkezett szimulált vehicle jelek és diagnosztikai adatkészlet létrehozásához. Töltse le a Data Factory egyéni .NET tevékenység igényei szerint testre szabott elem Visual Studio-megoldást, keresse fel a [adat-előállító egyéni tevékenység](http://go.microsoft.com/fwlink/?LinkId=717077) weblap. 

Ez a munkafolyamat bemutatja a mintaadatok készített kötegelt feldolgozásra.

![A mintaadatok előkészítve a kötegelt feldolgozásra munkafolyamat](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 


A folyamat egy egyéni Data Factory .NET tevékenység áll.

![PrepareSampleDataPipeline tevékenység](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

Miután a feldolgozási sor végrehajtása sikeres, és a RawCarEventsTable adatkészlet "Kész" van megjelölve, egy év alatt érkezett szimulált vehicle jelek és diagnosztikai adatainak előállítása. A következő mappához és fájlhoz a tárfiók a connectedcar tárolóban létrehozott jelenik meg:

![PrepareSampleDataPipeline kimeneti](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

## <a name="partition-the-data-set"></a>Az adatkészlet partíció
Az előkészítési lépést a nyers félig strukturált vehicle jelek és diagnosztikai adatkészlet olyan év/hónap formátumra particionálva. A particionálás elősegíthető hatékonyabb kérdez le, és a méretezhető, hosszú távú tárolás tartalék átvevő engedélyezésével. Például, az első blob fiók megtelik, az hibákat keresztül a következő fiókhoz. 

>[!NOTE] 
>Ez a lépés a megoldás csak kötegfeldolgozási vonatkozik.

Bemeneti és kimeneti adatok kezelése:

* **A kimeneti adatok** (címkézett PartitionedCarEventsTable), az ügyfél data Lake data eligazodást / "rawest" formájában hosszú időn maradnak. 
* **Adja meg adatokat** a folyamat van általában elvetve, mert a kimeneti adatok rendelkezik a bemeneti teljes visszaadása. Tárolja (particionált) jobban későbbi használatra.

A partíció car események munkafolyamat.

![Partíció car események munkafolyamat](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)


A nyers adatok particionálása PartitionCarEventsPipeline, egy Hive Azure HDInsight tevékenység segítségével az alábbi képernyőfelvételen látható módon. A mintaadatok az adatok előkészítési lépés év előállított év/hónap szerint van particionálva. A partíciók az év havonta (összesen 12 partíciók) a vehicle jelek és diagnosztikai adatok létrehozásához használt. 

![PartitionCarEventsPipeline tevékenység](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)


**PartitionConnectedCarEvents Hive-parancsfájl**

A Hive parancsfájl partitioncarevents.hql particionálás szolgál. A letöltött zip-fájl, a \demo\src\connectedcar\scripts mappában található. 
    
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

Miután a folyamat sikeres végrehajtása során, lásd: a tárfiók a connectedcar tárolóban hozott létre a következő partíciókat:

![A particionált kimeneti](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

Az adatok mostantól optimalizálása, könnyebben kezelhető, és készen állnak a gazdag kötegelt dcu további feldolgozásra. 

## <a name="data-analysis"></a>Adatok elemzése
Ebben a szakaszban látható egyesítése a Stream Analytics, az Azure Machine Learning, a Data Factory és a HDInsight a gazdag, sokoldalú analitikai vehicle egészségügyi, és ki irányítja a szokásokat.

### <a name="machine-learning"></a>Gépi tanulás
A cél a karbantartás vagy bizonyos heath statisztika, a következő feltételek alapján alapján visszaírási igénylő járművekről gyűjtött előrejelzése céljából:

* Ha valamelyik az alábbi három feltétel teljesül, a járművekről gyűjtött igényelnek, karbantartási karbantartás:
  
  * A kulcsszava nyomás értéke alacsony.
  * A motor olaj szintje alacsony.
  * A motor hőmérséklet túl magas.

* Ha a következő feltételek valamelyike teljesül, a járművekről gyűjtött előfordulhat, hogy rendelkezik egy biztonsági kérdés és visszaírási megkövetelése:
  
  * A motor hőmérséklet magas, de a külső hőmérséklet alacsony.
  * A motor hőmérséklet alacsony, de a külső hőmérséklet magas.

Az előző követelmények alapján, két külön modellek rendellenességek észlelését. Egy modellt a vehicle karbantartási észleléséhez, pedig egy modellt a vehicle visszaírási észleléséhez. A beépített egyszerű összetevő elemzés (PEM) algoritmus mindkét modellt használják a anomáliadetektálás. 

#### <a name="maintenance-detection-model"></a>**Karbantartási modell**

Ha egy három mutatók--kulcsszava nyomás, motor olaj vagy motor hőmérséklet--eleget tesz a megfelelő feltétel, a karbantartási modell egy anomáliabiztonsági jelentéseket. Ennek eredményeképpen csak három változókhoz kell kell figyelembe kell venni a modell létrehozása. A kísérletben a machine learning szolgáltatásban a **Select Columns in Dataset** a modul kibontása három változókhoz szolgál. Ezután a PCA alapú feltételészlelési modul segítségével hozhatók létre az anomáliadetektálási modell. 

PEM a egy meghatározott technika, a machine learning szolgáltatás kiválasztása, a besorolást és a anomáliadetektálás alkalmazható. PEM esetekben az értékek nevükön összetevők egyszerű esetlegesen kapcsolódó változók tartalmazó készlete alakítja át. PEM-alapú modellezési kulcs lényege, projekt adatokhoz egy alacsonyabb dimenziós területére könnyebben azonosíthatja a szolgáltatások és rendellenességeket.

Minden egyes új bemeneti az észlelési modellre az anomáliadetektálási érzékelő először kiszámítja a leképezés a eigenvectors a. Ezután a normalizált újjáépítése hiba számítja ki. Ez a normalizált hiba a anomáliadetektálási pontszám: minél nagyobb a hiba, a több rendellenes a példányt. 

A karbantartási észlelési probléma rekordokban tekinthető kulcsszava nyomás motor olaj és motor által definiált egy pontot háromdimenziós terület koordinátái. Rögzítheti a rendellenességek észlelését, az eredeti adatok a háromdimenziós területen található projektbe kétdimenziós területére PEM szolgál ki. Ebből kifolyólag a paraméterrel összetevők PEM-ban történő használatáról értékre van állítva két. Ez a paraméter a PEM-alapú anomáliadetektálás alkalmazása fontos szerepet játszik. PEM projektadatokat használja, ezek rendellenességeket könnyebben azonosítják.

#### <a name="recall-anomaly-detection-model"></a>**Az anomáliadetektálási modell visszahívása**

A visszaírási anomáliadetektálási észlelési modellben a **Select Columns in Dataset** és hasonló módon PCA alapú észlelési modulok használják. Pontosabban, a három változók--motor hőmérséklete, külső először használatával kibontása hőmérséklet és a sebességét – a **Select Columns in Dataset** modul. A sebesség változó is megtalálható, mert a motor hőmérséklet általában gyorsasága ad eredményül. Ezután a PCA alapú feltételészlelési modul szolgál az adatok a háromdimenziós területről projekt kétdimenziós területére. A visszaírási feltételek teljesülnek. A vehicle visszaírási szükséges, ha a motor és a külső hőmérséklet magas negatívan közötti kapcsolatot. PEM hajtja végre, a PCA alapú észlelési algoritmus segítségével rögzítheti a rendellenességeket. 

Vagy a modell betanításakor megszokott adatforgalmi szolgál a bemeneti adatokként a PCA alapú modell betanításához. (Normál adatok nem igényel karbantartás vagy visszahívás.) A pontozási kísérletben a betanított anomáliadetektálási modell annak észlelésére használnak, a vehicle szükség van-e karbantartás vagy visszahívás. 

### <a name="real-time-analysis"></a>Valós idejű elemzés
A következő Stream Analytics SQL-lekérdezés segítségével átlagának kiszámítása a fontos vehicle paraméterek beolvasása. Ezek a paraméterek például a vehicle sebességét, üzemanyag szint, motor, kilométer olvasási, kulcsszava nyomás, motor olaj szint és mások. A átlagok rendellenességek észlelését, riasztások ki, illetve egy adott régióban üzemeltetett járművekről gyűjtött általános állapotának feltételeinek meghatározása szolgálnak. A átlagok majd a demográfiai adatoknak megfelelő elérési közötti kapcsolatot. 

![A valós idejű feldolgozással Stream Analytics-lekérdezés](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

A átlagok kiszámítása a három másodperces átfedésmentes ablak keresztül. Egy átfedésmentes ablak használata mozaikként, átfedés nélkül és összefüggő időközök szükség. 

A Stream Analytics Ablakozó képességeivel kapcsolatos további tudnivalókért lásd: [Ablakozó (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

#### <a name="real-time-prediction"></a>**Valós idejű előrejelzés**

Egy alkalmazás tartalmazzák a megoldás a valós idejű gépi tanulási modell azok a rendszer. Az alkalmazás RealTimeDashboardApp létrehozásáról és a megoldás központi telepítésének részeként. Az alkalmazás:

* Egy esemény hub-példányon, ahol Stream Analytics közzéteszi az eseményeket a mintában folyamatosan figyeli.

    ![Az adatok közzétételére vonatkozó Stream Analytics-lekérdezés](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) 

* Események fogadása. Minden eseményhez, amely megkapja ezt az alkalmazást: 
   
   * Az adatok feldolgozása a machine learning-kérés-válasz pontozási (RR-EKET) végpont. Az RRS-végpontot automatikusan bekerül a telepítés részeként.
   * A Power BI adatkészletet az RRS kimeneti a leküldéses API-k használatával van közzétéve.

Ezt a mintát, amely szintén alkalmazható helyzetekben, amelyben a sor üzleti alkalmazás integrálható a valós idejű elemzési folyamat szeretné. Ilyen például, riasztások, értesítések és üzenetkezelés.

A testreszabások RealtimeDashboardApp Visual Studio megoldás letöltéséhez lásd: a [RealtimeDashboardApp letöltési](http://go.microsoft.com/fwlink/?LinkId=717078) weblap. 

#### <a name="execute-the-real-time-dashboard-application"></a>**A valós idejű irányítópulton alkalmazás végrehajtása**
1. Bontsa ki a RealtimeDashboardApp, és mentse helyileg.

    ![RealTimeDashboardApp mappa](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) 

2. Az alkalmazás RealtimeDashboardApp.exe végrehajtása.

3. Érvényes Power BI hitelesítő adatait, és válassza ki **bejelentkezés**.  

    ![Valós idejű irányítópulton app bejelentkezési ablak](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 
    
4. Válassza ki **elfogadása**.

    ![Valós idejű irányítópulton app végső bejelentkezési ablak](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

>[!NOTE] 
>Ha azt szeretné, a Power BI adatkészlet kiüríteni, hajtható végre a "flushdata" paraméterrel RealtimeDashboardApp. 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>Kötegelt elemzés
Itt az célja, hogy hogyan Contoso motorok használja az Azure számítási képességekre hasznosítására big Data típusú adatok megjelenítése. Ezek az adatok tárja fel a vezetői minták, a használati viselkedését és a vehicle egészségügyi részletes információkat. Ezek az információk lehetővé teszi:

* A felhasználói élmény javításához, és teszi olcsóbb szokásait és tíz vezetői viselkedések vezetői szóló insights megadásával.
* Ügyfelek, valamint a vezetői környezeteken üzleti döntéseket tartását és a legjobb minőségű termékek és szolgáltatások további proaktív módon.

Ebben a megoldásban a következő mérőszámokat célozzák meg:

* **Agresszív viselkedését befolyásoló tényezők**: a modellek, helyek, vezetői feltételek és agresszív vezetői minták a dcu év idő trendjét azonosítja. Contoso motorok használhatja ezeket insights marketingkampányok új személyre szabott szolgáltatások és a használat alapú biztosítási.
* **Tíz vezetői viselkedés**: azonosítja a trend a modellek, helyek, vezetői feltételeket, és idő év dcu tíz vezetői mintáról olvashat. Contoso motor ezeket a marketingkampányok insights segítségével bevezetni új funkciókat és költséghatékony és egyszerűsített-környezet vezetői szokásait illesztőprogramjait proaktív jelentése.
* **Modellek visszahívása**: által végrehajtott az anomáliadetektálási észlelési gépi tanulási kísérlet visszahívások igénylő modelljeit azonosítja.

Vizsgáljuk meg a fenti metrikák részleteit.

#### <a name="aggressive-driving-behavior-patterns"></a>**Agresszív vezetői viselkedés minták**

A particionált vehicle jelek és diagnosztikai adatokat AggresiveDrivingPatternPipeline, ahogy az az alábbi munkafolyamat dolgoznak. Hive a modelleket, hely, vehicle, üzemi és más paramétereket, amelyeket agresszív vezetői minták meghatározására szolgál.

![Agresszív vezetői minta munkafolyamat](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 

***Agresszív vezetői minta Hive-lekérdezések***

A Hive parancsfájl aggresivedriving.hql agresszív vezetői feltétel minták elemzésére használatos. A letöltött zip-fájl, a \demo\src\connectedcar\scripts mappában található. 

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


A parancsfájl fékezés nagy sebességű minták alapján reckless/agresszív vezetői viselkedés a vehicle átviteli fogaskerék pozícióját, berendezés tartásához lehetőleg keveset állapotát és sebesség kombinációját használja. 

Miután a folyamat sikeres végrehajtása során, lásd: a tárfiók a connectedcar tárolóban hozott létre a következő partíciókat:

![AggressiveDrivingPatternPipeline kimeneti](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 


#### <a name="fuel-efficient-driving-behavior-patterns"></a>**Tíz vezetői viselkedés minták**

A particionált vehicle jelek és diagnosztikai adatokat FuelEfficientDrivingPatternPipeline, ahogy az az alábbi munkafolyamat dolgoznak. Hive a modellek, a helyet, a vehicle, a üzemi és a más tulajdonságokat, amelyeket tíz vezetői minták meghatározására szolgál.

![Tíz vezetői minták](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

***Tíz vezetői minta Hive-lekérdezések***

A Hive parancsfájl fuelefficientdriving.hql tíz vezetői feltétel minták elemzésére használatos. A letöltött zip-fájl, a \demo\src\connectedcar\scripts mappában található. 

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


A parancsfájl a vehicle átviteli fogaskerék pozíció kombinációját használja, berendezés tartásához lehetőleg keveset állapot, a sebesség és a gyorsító pedál fékezés, a gyorsítás tíz vezetői viselkedését észlelésére és minták sebessége. 

Miután a folyamat sikeres végrehajtása során, lásd: a tárfiók a connectedcar tárolóban hozott létre a következő partíciókat:

![FuelEfficientDrivingPatternPipeline kimeneti](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

**Modell előrejelzéseket visszahívása**

A gépi tanulási kísérlet kiosztásakor és a megoldás központi telepítésének részeként webszolgáltatásként közzé. A kötegelt pontozás végpont szolgál a munkafolyamatban. A data factory kapcsolt szolgáltatásként regisztrálva van, a data factory kötegelt pontozási tevékenység segítségével operationalized.

![Machine learning-végpont](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

A regisztrált kapcsolódó szolgáltatás DetectAnomalyPipeline szerepel az anomáliadetektálási észlelési modell segítségével az adatok pontozása céljából. 

![Machine learning kötegelt pontozási tevékenység adat](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png)  

Néhány lépést, hogy a kötegelt pontozás webszolgáltatás is operationalized történik az adatok előkészítése az adatcsatorna. 

![DetectAnomalyPipeline visszaírási előrejelzés](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png)  

***Az anomáliadetektálási észlelési Hive-lekérdezések***

A pontozási befejezése után egy HDInsight tevékenység feldolgozza és összesíti az adatokat, amelyek a modell kategóriába sorolt rendellenességek észlelését. A modell 0.60 vagy magasabb valószínűségét jelző pontszámot használ.

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


Miután a folyamat sikeres végrehajtása során, lásd: a tárfiók a connectedcar tárolóban hozott létre a következő partíciókat:

![DetectAnomalyPipeline kimeneti](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

## <a name="publish"></a>Közzététel

### <a name="real-time-analysis"></a>Valós idejű elemzés
Egy kimeneti esemény hub-példány egy Stream Analytics-feladat lekérdezése közzéteszi az eseményeket. 

![A Stream Analytics-feladat közzétett egy kimeneti esemény hub-példány](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

A következő Stream Analytics-lekérdezés a kimeneti event hub példányhoz közzétételére szolgál:

![Stream Analytics lekérdezési közzétételére a kimeneti esemény hub-példány](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

Ez az események streamjét a RealTimeDashboardApp, amely megtalálható a megoldás által felhasznált. Ez az alkalmazás gépi tanulási kérés-válasz webes szolgáltatás valós idejű pontozó használja. Az eredményül kapott adatokat a Power BI adatkészletben felhasználásra teszi közzé. 

### <a name="batch-analysis"></a>Kötegelt elemzés
A kötegelt és a valós idejű feldolgozással eredményeit a rendszer közzéteszi az Azure SQL Database táblák felhasználásra. Az SQL server, az adatbázis és a táblázatok jönnek létre automatikusan a telepítési parancsfájl részeként. 

A kötegelt feldolgozásra eredmények az adatok adatközpont munkafolyamat lesz másolva.

![Kötegfeldolgozási adatok adatközpont munkafolyamat másolva eredmények](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

A Stream Analytics-feladat az adatközponthoz van közzétéve.

![A Stream Analytics-feladat közzéteszi az Adatközpont](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

Az adatok adatközpont beállítás a Stream Analytics-feladat van.

![A Stream Analytics-feladatban Data adatközpont beállítása](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

## <a name="consume"></a>Felhasználás
A Power BI ad a megoldás részletes irányítópult valós idejű adatok és a prediktív elemzés képi megjelenítések. 

A végső irányítópult ebben a példában néz ki:

![A Power BI-irányítópulton](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

## <a name="summary"></a>Összefoglalás
Ez a dokumentum a Vehicle Telemetriai Analytics megoldás részletes Lehatolás tartalmaz. A lambda architektúra mintát használja a valós idejű és kötegelt elemzés előrejelzéseket és műveletek. Ebben a mintában azokat a használati esetek gyakran használt adatok elérési útja (valós időben) igénylő vonatkozik és az elemzések cold elérési útja (kötegelt). 

### <a name="references"></a>Referencia

* [Vehicle telematika szimulátor Visual Studio megoldás](http://go.microsoft.com/fwlink/?LinkId=717075) 
* [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
* [Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)
* [Az Azure Event Hubs SDK a streameket](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [A mozgás képességek az Azure Data Factory adatok](../../data-factory/v1/data-factory-data-movement-activities.md)
* [Az Azure Data Factory .NET tevékenység](../../data-factory/v1/data-factory-use-custom-activities.md)
* [Az Azure Data Factory .NET tevékenység használt mintaadatok létrehozása a Visual Studio megoldás](http://go.microsoft.com/fwlink/?LinkId=717077) 
