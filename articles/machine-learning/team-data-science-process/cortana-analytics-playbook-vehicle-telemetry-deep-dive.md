---
title: "Részletes bemutatója az előre jelezni a vehicle állapotát, és ki irányítja az szokásait - Azure |} Microsoft Docs"
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
ms.openlocfilehash: 4050fdc2056df395bbcc37e3783f61eebd90f80a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>A járműtelemetria-elemzési megoldás forgatókönyve: a megoldás részletes ismertetése
Ez **menü** Ez a forgatókönyv szakaszait mutató hivatkozásokat: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Ez a szakasz részletezi az utasításokat, és a Testreszabás mutatók a megoldásarchitektúra írja le szakaszok azokat. 

## <a name="data-sources"></a>Adatforrások
A megoldás két különböző adatforrásból használ:

* **Szimulált vehicle jelek és diagnosztikai adatkészlet** és 
* **vehicle katalógus**

A vehicle telematika szimulátor Ez a megoldás részét. Diagnosztikai adatok bocsát ki, és jelzi a vehicle állapotát, és adott vezetői típusnak megfelelő időben. Kattintson a [Vehicle telematika szimulátor](http://go.microsoft.com/fwlink/?LinkId=717075) letölteni a **Vehicle telematika szimulátor Visual Studio megoldás** testreszabni a követelmények alapján. A vehicle katalógus egy referencia-adatkészletnek a VIN az modell-lel rendelkező tartalmazza.

![Vehicle telematika szimulátor](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)

*1. ábra – Vehicle telematika szimulátor*

Ez a JSON-formátumú adatkészletre mutató a következő sémát tartalmaz.

| Oszlop | Leírás | Értékek |
| --- | --- | --- |
| VIN |Véletlenszerűen létrehozott Vehicle azonosító szám |10 000 véletlenszerűen létrehozott vehicle azonosító számok fő listájának származik. |
| Külső hőmérséklet |A külső hőmérséklet, ahol a vehicle befolyásoló tényezők |Véletlenszerűen létrehozott szám 0 – 100 |
| Motor |A vehicle motor hőmérséklete |Véletlenszerűen létrehozott száma 0-500 |
| Gyorsaság |A motor sebesség, amellyel a vehicle befolyásoló tényezők |Véletlenszerűen létrehozott szám 0 – 100 |
| Üzemanyag |A vehicle üzemanyag szintje |Véletlenszerűen létrehozott szám 0 – 100 (üzemanyag százalékos értéke jelzi) |
| EngineOil |A vehicle motor olaj szintje |Véletlenszerűen létrehozott szám 0 – 100 (motor olaj százalékos értéke jelzi) |
| Kulcsszava nyomás |A vehicle kulcsszava nyomás |Véletlenszerűen előállított száma 0-50 (kulcsszava nyomás százalékos értéke jelzi) |
| Kilométer |A vehicle kilométer olvasása |Véletlenszerűen létrehozott szám 0-200000 |
| Accelerator_pedal_position |A vehicle gyorsító tartásához lehetőleg keveset pozíciója |Véletlenszerűen létrehozott szám 0 – 100 (gyorsító százalékos értéke jelzi) |
| Parking_brake_status |Azt jelzi, hogy a vehicle kell itt tartózkodnia vagy sem |IGAZ vagy hamis |
| Headlamp_status |Azt jelzi, ha a fényszóró megtalálható-e |IGAZ vagy hamis |
| Brake_pedal_status |Azt jelzi, hogy a fékpedál nélkül, vagy sem |IGAZ vagy hamis |
| Transmission_gear_position |A vehicle átviteli fogaskerék pozíciója |Állapotok: először, a második, harmadik, negyedik, ötödik, hatodik, hetedik, nyolcadik |
| Ignition_status |Azt jelzi, hogy a vehicle fut vagy leállt |IGAZ vagy hamis |
| Windshield_wiper_status |Azt jelzi, hogy a szélvédőkeret ablaktörlő be van kapcsolva vagy nem |IGAZ vagy hamis |
| ABS |Azt jelzi, hogy ABS részt vevő vagy sem |IGAZ vagy hamis |
| időbélyeg |Az időbélyeg az adatpont létrehozásakor |Dátum |
| Város |A vehicle helye |Ebben a megoldásban 4 városokat: Bellevue, Redmond, Sammamish, Seattle |

A vehicle modell referencia-adatkészletnek VIN tartalmaz az a modell-lel. 

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

### <a name="references"></a>Referencia
[Vehicle telematika szimulátor Visual Studio megoldás](http://go.microsoft.com/fwlink/?LinkId=717075) 

[Az Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)

[Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)

## <a name="ingestion"></a>Adatfeldolgozást
Azure Event Hubs, a Stream Analytics és a Data Factory kombinációi használja tölti be a vehicle jeleket, a diagnosztikai események és valós idejű és kötegelt elemzés. Ezek az összetevők létrehozni és konfigurálni a megoldás központi telepítésének részeként. 

### <a name="real-time-analysis"></a>Valós idejű elemzés
A Vehicle telematika szimulátor által generált események kerülnek közzétételre az Event hubs Event Hub SDK használatával. A Stream Analytics-feladat ingests ezeket az eseményeket az eseményközpontból, és feldolgozza a valós idejű elemzése a vehicle egészségügyi adatokat. 

![Event hub irányítópult](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

*4. ábra - Eseményközpont irányítópult*

![A Stream Analytics-feladat adatainak feldolgozása](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 

*5. ábra - adatfeldolgozás Stream Analytics-feladat*

A Stream Analytics-feladat;

* az Event Hubs adatait ingests 
* a vehicle VIN hozzárendelése a megfelelő modell referenciaadatokkal illesztést végez 
* az Azure blob-tároló gazdag kötegelt elemzés fenntartása őket. 

A következő Stream Analytics-lekérdezés segítségével megőrizni az adatokat az Azure blob Storage tárolóban. 

![Stream Analytics feladat lekérdezés adatfeldolgozást](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 

*6. ábra – a Stream Analytics-feladat adatfeldolgozást lekérdezése*

### <a name="batch-analysis"></a>Kötegelt elemzés
Azt is generál egy szimulált vehicle jelek és diagnosztikai adatkészlet gazdagabb kötegelt elemzés további mennyiségét. Ez elengedhetetlen ahhoz, hogy a helyes reprezentatív adatmennyiség kötegelt feldolgozásra. Erre a célra egy "PrepareSampleDataPipeline" nevű folyamatot használunk az Azure Data Factory-munkafolyamat egy év alatt érkezett szimulált vehicle jelek és diagnosztikai adatkészlet létrehozásához. Kattintson a [adat-előállító egyéni tevékenység](http://go.microsoft.com/fwlink/?LinkId=717077) letölteni a Data Factory egyéni DotNet tevékenység igényei szerint testre szabott elem Visual Studio-megoldáshoz. 

![Kötegfeldolgozási munkafolyamat mintaadatok előkészítése](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 

*7. ábra - mintaadatok előkészítése kötegelt feldolgozásra munkafolyamat*

A feldolgozási sor tartalmaz egy egyéni ADF .net tevékenység, itt megjelenítése:

![PrepareSampleDataPipeline tevékenység](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

*8. ábra - PrepareSampleDataPipeline*

Ha a folyamat sikeresen lefut, és "RawCarEventsTable" adatkészlet "Kész" van megjelölve egyéves érdemes szimulált vehicle jelek és diagnosztikai adatainak előállítása. A következő mappa és fájl létrehozása a "connectedcar" tárolóban a tárfiókban lévő jelenik meg:

![PrepareSampleDataPipeline kimeneti](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

*9. ábra - PrepareSampleDataPipeline kimeneti*

### <a name="references"></a>Referencia
[Az Azure Event Hub SDK a streameket](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

[A mozgás képességek az Azure Data Factory adatok](../../data-factory/v1/data-factory-data-movement-activities.md)
[Azure Data Factory DotNet tevékenység](../../data-factory/v1/data-factory-use-custom-activities.md)

[Az Azure Data Factory DotNet tevékenység visual studio megoldás a mintaadatok előkészítése](http://go.microsoft.com/fwlink/?LinkId=717077) 

## <a name="partition-the-dataset"></a>A dataset partíció
A nyers félig strukturált vehicle jelek és diagnosztikai adatkészlet particionáltak, az adatok előkészítése lépésben egy év/hónap formátumú fájlba. A particionálás elősegíthető hatékonyabb kérdez le, és a méretezhető, hosszú távú tárolás, az első fiók megtelik, mivel lehetővé teszi, hogy tartalék át egy blob-fiókból a Tovább gombra. 

>[!NOTE] 
>Ez a lépés a megoldásban is alkalmazható, csak a kötegelt feldolgozásra.

Bemeneti és kimeneti adatok adatkezelés:

* A **kimeneti adatai** (feliratú *PartitionedCarEventsTable*) az adatok a "Data Lake" az ügyfél eligazodást / "rawest" képernyő egy hosszú ideig tartják. 
* A **bemeneti adatai** a folyamat volna általában elveti a rendszer, a kimeneti adatok rendelkezik a bemeneti teljes visszaadása – csak tárolt (particionált) jobban későbbi használatra.

![Partíció car események munkafolyamat](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)

*10. ábra – partíció Car események munkafolyamat*

A nyers adatok particionálása használata a Hive HDInsight tevékenység "PartitionCarEventsPipeline". A mintaadatok egy évig az 1. lépésben létrehozott év/hónap szerint van particionálva. A partíciók az év havonta (összesen 12 partíciók) a vehicle jelek és diagnosztikai adatok létrehozásához használt. 

![PartitionCarEventsPipeline tevékenység](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)

*11. ábra - PartitionCarEventsPipeline*

***PartitionConnectedCarEvents Hive-parancsfájl***

A következő Hive-parancsfájl, a "partitioncarevents.hql" nevű particionálására használja, és a letöltött zip "\demo\src\connectedcar\scripts" mappájában található. 
    
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

A folyamat sikeres végrehajtása után megjelenik a következő partíciókat hozott létre a tárfiók a "connectedcar" tárolóban.

![A particionált kimeneti](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

*12. ábra - particionált kimeneti*

Az adatok most már megfelelően lett optimalizálva, kezelhető, és készen áll a gazdag kötegelt dcu további feldolgozásra. 

## <a name="data-analysis"></a>Adatok elemzése
Ebben a szakaszban látható Azure Stream Analytics, az Azure Machine Learning, az Azure Data Factory és az Azure HDInsight egyesítése a gazdag, sokoldalú analitikai vehicle egészségügyi, és ki irányítja a szokásokat. Itt három alszakaszokat van:

1. **Gépi tanulás**: a jelen alszakasz ismerteti a anomáliadetektálási észlelési kísérlet karbantartási karbantartási igénylő járművekről gyűjtött és a biztonsági problémák miatt visszahívások igénylő járművekről gyűjtött becsülhető ebben a megoldásban használt rendelkezik.
2. **Valós idejű elemzési**: a jelen alszakasz a valós idejű elemzés, használja a Stream Analytics lekérdezési nyelv és a gépi tanulási kísérlet valós idejű használ egy egyéni alkalmazást végrehajtott vonatkozó információkat tartalmazza.
3. **Kötegelt elemzés**: a jelen alszakasz ismerteti a átalakítása vonatkozó és az Azure HDInsight és az Azure Machine Learning Azure Data Factory által operationalized kötegelt adatok feldolgozása.

### <a name="machine-learning"></a>Machine Learning
Itt célunk a járművekről gyűjtött vagy a karbantartási és a visszaírási bizonyos heath statisztika alapján előre jelezni. A következő feltételek biztosítjuk

* Egyet az alábbi három feltétel teljesülése esetén a járművekről gyűjtött szükséges **karbantartási karbantartási**:
  
  * Kulcsszava nyomás értéke alacsony
  * Motor olaj szintje alacsony
  * Motor túl magas
* Előfordulhat, ha a következő feltételek valamelyike teljesül, a járművekről gyűjtött egy **biztonsági kérdés** és a szükséges **visszaírási**:
  
  * Motor magas, de külső hőmérséklet alacsony
  * Motor alacsony, de külső hőmérséklet magas

Az előző követelmények alapján létrehoztunk két külön modellek rendellenességek észlelését, egy a vehicle karbantartási észleléséhez és egy a vehicle visszaírási észleléséhez észleléséhez. A beépített egyszerű összetevő elemzés (PEM) algoritmus mindkét modellek használják a anomáliadetektálás. 

**Karbantartási modell**

-Kulcsszava nyomás, motor olaj vagy motor hőmérséklet - három mutatók egyikét megfelel az megfelelő állapotba, ha a karbantartási modell jelenti az anomáliadetektálási. Ennek eredményeképpen csak kell figyelembe venni a modell fejlesztése során három változókhoz. A kísérlet az Azure Machine Learning, először használjuk a **Select Columns in Dataset** modul három változókhoz kibontásához. A PCA alapú feltételészlelési modul mellett az anomáliadetektálási modell összeállításához használjuk. 

Egyszerű összetevő elemzésre (PEM) egy meglévő technika, a machine learning szolgáltatás kiválasztása, a besorolást és a anomáliadetektálás alkalmazható. PEM alakít egy készletét tartalmazó esetlegesen kapcsolódó változók neve a fő összetevőit értékek esetében. A kulcs a PEM-alapú modellezési lényege projekt adatokhoz egy alacsonyabb dimenziós területére, hogy a szolgáltatások és rendellenességeket könnyebben azonosítható legyen.

A modell minden új bemenet az anomáliadetektálási érzékelő először kiszámítja a leképezés a eigenvectors a, és majd kiszámítja a normalizált újjáépítése hiba. Ez a normalizált hiba a anomáliadetektálási pontszámot. Minél nagyobb a hiba, a rendellenes több példány. 

A karbantartási észlelési probléma rekordokban tekinthető kulcsszava nyomás motor olaj és motor által definiált egy pont a 3-dimenziós térben koordinátái. Rögzítheti a rendellenességek észlelését, a 2-dimenziós területére PEM használatával az eredeti adatok a 3-dimenziós területen kivetíthetik azt. Ebből kifolyólag hivatott a paraméter összetevők PEM 2 száma. Ez a paraméter a PEM-alapú anomáliadetektálás alkalmazása fontos szerepet játszik. PEM kiálló adatokat, miután azt azonosíthatja ezeket a rendellenességeket könnyebben.

**Visszaírási anomáliadetektálási modell** visszaírási anomáliadetektálási észlelési modell használjuk a Select Columns in Dataset és PCA alapú észlelési modulok hasonló módon. Pontosabban, azt először nyerje ki három változók - kívül a hőmérséklet és sebességét, a motor hőmérséklet - használatával a **Select Columns in Dataset** modul. Is magában foglalja az a sebesség változó, mert a motor hőmérséklet általában visszamenőleges korrelációban állnak a teljesítmény. PCA alapú feltételészlelési modul mellett az adatokat a 3-dimenziós területről projekt 2-dimenziós területére használjuk. A visszaírási feltételek teljesülnek, és ehhez a vehicle kell-e visszaírási amikor motor és a külső hőmérséklet magas negatívan közötti kapcsolatot. PCA alapú észlelési algoritmus használ, azt rögzíteni tudja a rendellenességeket PEM végrehajtása után. 

Vagy a modell betanításakor kell használni a megszokott adatforgalmi, amely nem igényli a karbantartás vagy visszaírási a PCA alapú modell betanítása bemeneti adatként. A pontozási kísérlet az anomáliadetektálási betanított modell használjuk-e a vehicle szükséges karbantartás vagy visszaírási észleléséhez. 

### <a name="real-time-analysis"></a>Valós idejű elemzés
A következő Stream Analytics SQL-lekérdezés például vehicle sebessége, üzemanyag szint, motor, kilométer olvasási, kulcsszava nyomás, motor olaj szint és más fontos vehicle paraméterek átlaga eléréséhez használatos. A átlagok rendellenességek észlelését, ki riasztást, és adott régióban üzemeltetett járművekről gyűjtött általános állapotának feltételeinek meghatározása és majd összefüggéseket azt demográfiai szolgálnak. 

![A valós idejű feldolgozással Stream Analytics-lekérdezés](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

*13. ábra: valós idejű feldolgozással Stream Analytics-lekérdezés*

A átlagok kiszámítása a 3-második TumblingWindow keresztül. Használjuk TubmlingWindow ebben az esetben óta mozaikként, átfedés nélkül és összefüggő időközök szükségesek. 

Azure Stream Analytics a "Ablakozó" képességeivel kapcsolatos további tudnivalókért kattintson [Ablakozó (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

**Valós idejű előrejelzés**

Egy alkalmazás tartalmazzák a megoldás a valós idejű gépi tanulási modell azok a rendszer. A "RealTimeDashboardApp" nevű alkalmazás létrehozásáról és a megoldás központi telepítésének részeként. Az alkalmazás a következőket végzi el:

1. Egy Eseményközpont-példányon, ahol Stream Analytics közzéteszi az eseményeket a mintában folyamatosan figyeli. ![Stream Analytics lekérdezési adatok közzétételére vonatkozó](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) *14. ábra: a Stream Analytics lekérdezési való közzétételéhez az adatok egy kimeneti esemény Hub-példány* 
2. Minden eseményhez, amely megkapja ezt az alkalmazást: 
   
   * A Machine Learning kérés-válasz pontozási (RR-EKET) végpont használatával adatokat dolgozza fel. Az RRS-végpontot automatikusan bekerül a telepítés részeként.
   * Az RRS kimeneti közzé van téve a leküldéses API-k használata a Power BI DataSet.

Ezt a mintát, amely szintén alkalmazható helyzetekben, amelyben egy üzletági (LoB) alkalmazások integrálása a valós idejű elemzési folyamata, például a riasztások, értesítések és üzenetkezelési forgatókönyvek szeretné.

Kattintson a [RealtimeDashboardApp letöltési](http://go.microsoft.com/fwlink/?LinkId=717078) a testreszabások RealtimeDashboardApp Visual Studio megoldás letöltése. 

**A valós idejű irányítópulton alkalmazás végrehajtása**
1. Bontsa ki és mentse helyileg ![RealtimeDashboardApp mappa](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) *16. ábra – RealtimeDashboardApp mappa*  
2. Az alkalmazás RealtimeDashboardApp.exe végrehajtása
3. Adjon meg érvényes Power BI hitelesítő adatokat, jelentkezzen be, és kattintson az elfogadás ![Valós idejű irányítópulton app bejelentkezés a Power bi-hoz](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) ![Bejelentkezés a Power bi-bA Befejezés valós idejű irányítópult-alkalmazás](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

*17. ábra – RealtimeDashboardApp:-Bejelentkezés a Power bi-hoz*

>[!NOTE] 
>Ha azt szeretné, a Power BI dataset kiüríteni, hajtható végre a "flushdata" paraméterrel RealtimeDashboardApp: 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>Kötegelt elemzés
Itt az célja, hogy hogyan Contoso motorok használja az Azure számítási képességekre hasznosítására gazdag dcu mintát, a használati működésének és a vehicle állapotát befolyásoló tényezők a big Data típusú adatok megjelenítése. Ez lehetővé teszi:

* A felhasználói élmény javításához, és teszi olcsóbb szokásait és üzemanyag hatékony vezetői viselkedések vezetői szóló insights megadásával
* Proaktív információ az ügyfelek és a vezetői patters az üzleti döntések szabályozására, és adja meg a legjobb osztály termékek és szolgáltatások

Ebben a megoldásban azt céloz meg a következő metrikákat:

1. **Agresszív viselkedését befolyásoló tényezők**: a modellek, helyek, vezetői feltételek és idő az év agresszív vezetői minták a dcu trendjét azonosítja. Contoso motor ezeket insights segítségével marketingkampányok, új személyre szabott szolgáltatásokat és a használat alapú biztosítási vezetői.
2. **Hatékony vezetői viselkedés üzemanyag-**: a modellek, helyek, vezetői feltételek és idő az év üzemanyag hatékony vezetői minták a dcu trendjét azonosítja. Contoso motorok ezek insights marketingkampányok, ki irányítja az új funkciók és a hatályos és a környezet rövid vezetői szokásait proaktív reporting az illesztőprogramokhoz a költségeket. 
3. **Modellek visszahívása**: visszahívások igénylő által végrehajtott az anomáliadetektálási észlelési gépi tanulási kísérlet modelljeit azonosítja

Nézzük meg az egyes ezeket a mérési részleteinek

**Agresszív vezetői minta**

A particionált vehicle jelek és diagnosztikai adatok feldolgozása a feldolgozási nevű "AggresiveDrivingPatternPipeline" Hive segítségével határozza meg, a modellek, a helyet, a vehicle, a üzemi és a más paramétereket, amelyek agresszív vezetői mintát mutat.

![Agresszív vezetői minta munkafolyamat](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 
*. ábra 18 – agresszív vezetői minta munkafolyamat*


***Agresszív vezetői minta Hive-lekérdezések***

A Hive-parancsfájl agresszív vezetői feltétel mintát elemzéséhez használt "aggresivedriving.hql" nevű, "\demo\src\connectedcar\scripts" mappában található a letöltött zip helyezkedik el. 

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


Vehicle tartozó átviteli fogaskerék pozícióját, berendezés tartásához lehetőleg keveset állapotát és sebességét, kombinációját reckless/agresszív hajtott viselkedés alapján fékezés nagy sebességű mintát használja azt. 

A folyamat sikeres végrehajtása után megjelenik a következő partíciókat hozott létre a tárfiók a "connectedcar" tárolóban.

![AggressiveDrivingPatternPipeline kimeneti](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 

*19. ábra – AggressiveDrivingPatternPipeline kimeneti*

**Üzemanyag hatékony vezetői minta**

A particionált vehicle jelek és diagnosztikai adatokat az adatcsatorna "FuelEfficientDrivingPatternPipeline" nevű dolgoznak fel. Hive a modellek, a helyet, a vehicle, a üzemi és a más tulajdonságokat, amelyeket üzemanyag hatékony vezetői mintát meghatározására szolgál.

![Tíz vezetői minta](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

*20. ábra – tíz vezetői minta munkafolyamat*

***Üzemanyag hatékony vezetői minta Hive lekérdezés***

A Hive-parancsfájl agresszív vezetői feltétel mintát elemzéséhez használt "fuelefficientdriving.hql" nevű, "\demo\src\connectedcar\scripts" mappában található a letöltött zip helyezkedik el. 

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


A vehicle tartozó átviteli fogaskerék pozíció kombinációját használja, berendezés tartásához lehetőleg keveset állapot, a sebesség és a gyorsító pedál üzemanyag hatékony vezetői viselkedését fékezés, a gyorsítás alapján észlelésére és minták sebessége. 

A folyamat sikeres végrehajtása után megjelenik a következő partíciókat hozott létre a tárfiók a "connectedcar" tárolóban.

![FuelEfficientDrivingPatternPipeline kimeneti](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

*21. ábra – FuelEfficientDrivingPatternPipeline kimeneti*

**Előrejelzés visszahívása**

A gépi tanulási kísérlet kiosztásakor és a megoldás központi telepítésének részeként webszolgáltatásként közzé. A kötegelt pontozás végpont a rendszer a munkafolyamatban, a data factory kapcsolt szolgáltatásként regisztrált és operationalized használatával a data factory kötegelt pontozási tevékenység elkészítéséhez használja.

![Machine Learning-végpont](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

*A data factory kapcsolt szolgáltatásként regisztrált 22. ábra – gépi tanulási végpont*

A regisztrált kapcsolódó szolgáltatás szerepel a DetectAnomalyPipeline az adatokat az anomáliadetektálási modell pontozása céljából. 

![Számítógép-tanulás kötegelt pontozási tevékenység adat](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png) 

*23. ábra – a data factory Azure Machine Learning kötegelt pontozási tevékenység* 

Az adatok előkészítése az adatcsatorna végre, hogy a kötegelt pontozás webszolgáltatás is operationalized néhány lépésből áll. 

![Járművekről gyűjtött visszahívások igénylő előrejelzésére DetectAnomalyPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png) 

*24. ábra – járművekről gyűjtött visszahívások igénylő előrejelzésére DetectAnomalyPipeline* 

***Az anomáliadetektálási észlelési Hive-lekérdezések***

A pontozási befejezése után egy HDInsight tevékenységgel feldolgozza és összesíti az adatokat, amelyek a 0,60 a valószínűség pontszám modell által rendellenességek észlelését, kategorizált vagy magasabb.

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


A folyamat sikeres végrehajtása után megjelenik a következő partíciókat hozott létre a tárfiók a "connectedcar" tárolóban.

![DetectAnomalyPipeline kimeneti](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

*25. ábra – DetectAnomalyPipeline kimeneti*

## <a name="publish"></a>Közzététel

### <a name="real-time-analysis"></a>Valós idejű elemzés
Egy, a Stream Analytics-feladat lekérdezése közzéteszi az eseményeket egy kimeneti esemény Hub-példány. 

![A Stream Analytics-feladat közzéteszi a kimenetnek Event Hub-példány](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

*26. ábra – a Stream Analytics-feladat közzéteszi, a kimenetnek Event Hub-példány*

![Stream Analytics lekérdezési a kimenet közzététele az Event Hubs-példány](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

*Ábra 27 – a kimeneti közzétételére Stream Analytics lekérdezési Event Hub-példány*

Ez az események streamjét a megoldásban RealTimeDashboardApp fel. Ez az alkalmazás kihasználja a Machine Learning kérés-válasz webszolgáltatás valós idejű pontozó, és az eredményül kapott adatokat közzéteszi a Power BI dataset felhasználásra. 

### <a name="batch-analysis"></a>Kötegelt elemzés
A kötegelt és a valós idejű feldolgozással eredményeit az Azure SQL Database táblákhoz felhasználásra kerülnek közzétételre. Az Azure SQL Server adatbázis és a táblázatok jönnek létre automatikusan a telepítési parancsfájl részeként. 

![Kötegfeldolgozási eredmények másolás adatok adatközpont munkafolyamat](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

*28. ábra – kötegfeldolgozási eredmények másolás adatok adatközpont munkafolyamat*

![A Stream Analytics-feladat közzéteszi az Adatközpont](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

*29. ábra – a Stream Analytics-feladat közzéteszi az Adatközpont*

![A Stream Analytics-feladatban Data adatközpont beállítása](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

*30. ábra – a Stream Analytics-feladat beállítása adatpiac*

## <a name="consume"></a>Felhasználás
A Power BI ad a megoldás részletes irányítópult valós idejű adatok és a prediktív elemzés képi megjelenítések. 

Kattintson ide a Power BI-jelentéseket és az irányítópult beállításával kapcsolatos részletes információkra van szüksége. A végső irányítópult így néz ki:

![A Power BI-irányítópulton](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

*Ábra 31 - Power BI-irányítópulton*

## <a name="summary"></a>Összefoglalás
Ez a dokumentum a Vehicle Telemetriai Analytics megoldás részletes Lehatolás tartalmaz. Ez egy lambda architektúra mintát bővíthető valós idejű és kötegelt elemzés előrejelzéseket és műveletek. Ebben a mintában azokat a használati esetek gyakran használt adatok elérési útja (valós időben) igénylő vonatkozik és az elemzések cold elérési útja (kötegelt). 

