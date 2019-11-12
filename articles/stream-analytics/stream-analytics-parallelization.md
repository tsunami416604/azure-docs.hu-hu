---
title: Lekérdezési párhuzamos és méretezés használata Azure Stream Analytics
description: Ez a cikk azt ismerteti, hogyan méretezhetők Stream Analytics feladatok a bemeneti partíciók konfigurálásával, a lekérdezés definíciójának finomhangolásával és a feladat-átviteli egységek beállításával.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 985746989af39aa55d5d8af735edf62f4c4b77b7
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932290"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>A lekérdezési párhuzamos kihasználása Azure Stream Analytics
Ez a cikk bemutatja, hogyan veheti igénybe a párhuzamos előnyeit a Azure Stream Analyticsban. Megtudhatja, hogyan méretezheti Stream Analytics feladatokat a bemeneti partíciók konfigurálásával és az elemzési lekérdezés definíciójának finomhangolásával.
Előfeltételként érdemes megismerni a [folyamatos átviteli egységek ismertetése és módosítása](stream-analytics-streaming-unit-consumption.md)című témakörben ismertetett folyamatos átviteli egység fogalmát.

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Mik a Stream Analytics feladatok részei?
A Stream Analytics-feladatdefiníció bemeneteket, lekérdezéseket és kimeneteket tartalmaz. Bemenetek: a feladatból beolvassa az adatfolyamot a rendszerből. A lekérdezés az adatbeviteli adatfolyam átalakítására szolgál, és a kimenet, ahol a feladatot a rendszer a feladatnak küldi.

Egy feladathoz legalább egy bemeneti forrás szükséges az adatátvitelhez. Az adatfolyam bemeneti forrása az Azure Event hub-ban vagy az Azure Blob Storage-ban is tárolható. További információ: [a Azure stream Analytics bemutatása](stream-analytics-introduction.md) és a [Azure stream Analytics használatának első lépései](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-sources-and-sinks"></a>Források és nyelők partíciói
A Stream Analyticsi feladatok skálázása kihasználja a bemeneti vagy kimeneti partíciók előnyeit. A particionálás lehetővé teszi, hogy egy partíciós kulcs alapján csoportosítsa az adathalmazokat. Az adatmennyiséget (például egy streaming Analytics-feladatot) feldolgozó folyamat párhuzamosan képes használni a különböző partíciókat, ami növeli az átviteli sebességet. 

### <a name="inputs"></a>Bemenetek
Az összes Azure Stream Analytics-bemenet kihasználhatja a particionálás előnyeit:
-   EventHub (a particionálási kulcsot explicit módon kell beállítani a PARTITION BY kulcsszó alapján)
-   IoT Hub (a partíciós kulcsot explicit módon kell beállítani a PARTITION BY kulcsszó alapján)
-   Blob Storage

### <a name="outputs"></a>Kimenetek

Stream Analytics használata esetén kihasználhatja a particionálást a kimenetekben:
-   Azure Data Lake Storage
-   Azure Functions
-   Azure-tábla
-   BLOB Storage (a partíciós kulcs explicit módon állítható be)
-   Cosmos DB (explicit módon be kell állítania a partíció kulcsát)
-   Event Hubs (explicit módon be kell állítania a partíció kulcsát)
-   IoT Hub (explicit módon be kell állítania a partíció kulcsát)
-   Service Bus
- SQL és SQL Data Warehouse választható particionálással: További információ a [kimenetről Azure SQL Database lapra](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf).

A Power BI nem támogatja a particionálást. A bemenet azonban továbbra is particionálható a [jelen szakaszban](#multi-step-query-with-different-partition-by-values) leírtak szerint. 

A partíciókkal kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Az Event Hubs-szolgáltatások áttekintése](../event-hubs/event-hubs-features.md#partitions)
* [Adatparticionálás](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Zavaróan párhuzamos feladatok
Egy *kínosan párhuzamos* feladatokkal a leginkább méretezhető forgatókönyv van Azure stream Analyticsban. A bemenet egy partícióját a lekérdezés egy példányához kapcsolja a kimenet egyik partíciója számára. Ez a párhuzamosság az alábbi követelményekkel rendelkezik:

1. Ha a lekérdezési logikája ugyanazon lekérdezési példány által feldolgozott kulcstól függ, meg kell győződnie arról, hogy az események a bemenet azonos partícióján mennek. Event Hubs vagy IoT Hub esetén ez azt jelenti, hogy az események **PartitionKey** értékének meg kell egyeznie. Azt is megteheti, hogy particionált küldőket is használhat. A blob Storage esetében ez azt jelenti, hogy az eseményeket ugyanarra a partíciós mappába küldik. Ha a lekérdezési logikának nincs szüksége ugyanazon kulcs feldolgozására ugyanazzal a lekérdezési példánnyal, figyelmen kívül hagyhatja ezt a követelményt. Ilyen logika például egy egyszerű Select-Project-Filter lekérdezés lehet.  

2. Ha az adatok bekerülnek a bemeneti oldalon, meg kell győződnie arról, hogy a lekérdezés particionálva van-e. Ehhez a **partíciókat** kell használnia az összes lépésben. Több lépés is engedélyezett, de mindegyiket ugyanazzal a kulccsal kell particionálni. A 1,0 és a 1,1 kompatibilitási szint alatt a particionálási kulcsot a **PartitionID** értékre kell beállítani ahhoz, hogy a feladatnak teljesen párhuzamosnak kell lennie. A 1,2-es és újabb compatility-szintű feladatokhoz egyéni oszlop adható meg partíciós kulcsként a bemeneti beállításokban, a feladat pedig paralellized automatikusan, a PARTITION BY záradék nélkül is. Az Event hub kimenete esetében a "Partition Key Column" tulajdonságot "PartitionId" értékre kell beállítani.

3. A legtöbb kimenet kihasználhatja a particionálás előnyeit, azonban ha olyan kimeneti típust használ, amely nem támogatja a particionálást, nem lesz teljesen párhuzamos. További részletekért tekintse meg a [kimenet szakaszt](#outputs) .

4. A bemeneti partíciók számának meg kell egyeznie a kimeneti partíciók számával. A blob Storage-kimenet képes támogatni a partíciókat, és örökli a felsőbb rétegbeli lekérdezés particionálási sémáját. Ha meg van adva a blob Storage-hoz tartozó partíciós kulcs, a rendszer bemeneti partíción particionálja az adatokat, így az eredmény továbbra is teljesen párhuzamos. Íme néhány példa olyan partíciós értékekre, amelyek lehetővé teszik egy teljesen párhuzamos feladatot:

   * 8 Event hub bemeneti partíció és 8 Event hub kimeneti partíció
   * 8 Event hub bemeneti partíció és blob Storage-kimenet
   * 8 Event hub bemeneti partíció és blob Storage-kimenet particionálva egy egyéni mezővel, tetszőleges időponttal
   * 8 blob Storage bemeneti partíció és blob Storage kimenet
   * 8 blob Storage bemeneti partíció és 8 Event hub kimeneti partíció

Az alábbi részekben olyan példákat ismertetünk, amelyek zavaróan párhuzamosak.

### <a name="simple-query"></a>Egyszerű lekérdezés

* Bemenet: Event hub 8 partícióval
* Kimenet: az Event hub 8 partícióval rendelkezik (a partíciós kulcs oszlopát a "PartitionId" érték használatára kell beállítani)

Lekérdezés

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

Ez a lekérdezés egy egyszerű szűrő. Ezért nem kell aggódnia az Event hub-ba küldött bemenet particionálásakor. Figyelje meg, hogy az 1,2 előtt a kompatibilitási szinttel rendelkező feladatok között szerepelnie kell a **Partition by PartitionID** záradéknak, így a korábbitól #2 követelménynek eleget tesz. A kimenethez konfigurálnia kell az Event hub kimenetét a feladatokban, hogy a partíciós kulcs **PartitionID**legyen beállítva. Egy utolsó ellenőrzéssel győződjön meg arról, hogy a bemeneti partíciók száma egyenlő a kimeneti partíciók számával.

### <a name="query-with-a-grouping-key"></a>Lekérdezés csoportosítási kulccsal

* Bemenet: Event hub 8 partícióval
* Kimenet: blob Storage

Lekérdezés

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

A lekérdezésnek van egy csoportosítási kulcsa. Ezért az együtt csoportosított eseményeket ugyanarra az Event hub-partícióra kell elküldeni. Mivel ebben a példában a TollBoothID csoportba tartozunk, a TollBoothID az Event hub eseményeinek elküldésekor partíciós kulcsként kell használni. Ebben az ASA-ben a **Partition by PartitionID** használatával örökölheti a partíciós sémát, és engedélyezheti a teljes párhuzamos. Mivel a kimenet blob Storage, nem kell aggódnia a partíciós kulcs értékének konfigurálásával kapcsolatban, mint követelmény #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Példa olyan forgatókönyvekre, amelyek *nem* zavaróan párhuzamosak

Az előző szakaszban néhány kínosan párhuzamos forgatókönyvet mutatunk be. Ebben a szakaszban olyan forgatókönyveket ismertetünk, amelyek nem felelnek meg az összes követelménynek. 

### <a name="mismatched-partition-count"></a>Nem egyező partíciók száma
* Bemenet: Event hub 8 partícióval
* Kimenet: az Event hub 32 partícióval rendelkezik

Ebben az esetben nem számít, hogy mi a lekérdezés. Ha a bemeneti partíciók száma nem egyezik meg a kimeneti partíciók számával, a topológia nem zavaróan párhuzamos. + azonban továbbra is elérheti a szintet vagy a párhuzamos.

### <a name="query-using-non-partitioned-output"></a>Lekérdezés nem particionált kimenet használatával
* Bemenet: Event hub 8 partícióval
* Kimenet: Power BI

Power BI a kimenet jelenleg nem támogatja a particionálást. Ezért ez a forgatókönyv nem zavaró módon párhuzamos.

### <a name="multi-step-query-with-different-partition-by-values"></a>Többlépéses lekérdezés különböző PARTÍCIÓkkal értékek alapján
* Bemenet: Event hub 8 partícióval
* Kimenet: Event hub 8 partícióval

Lekérdezés

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Ahogy láthatja, a második lépés a **TollBoothId** használja a particionálási kulcsként. Ez a lépés nem egyezik meg az első lépéssel, ezért a Shuffle-t kell elvégeznie. 

Az előző példák olyan Stream Analytics feladatokat mutatnak be, amelyek megfelelnek a (vagy nem) kínos párhuzamos topológiának. Ha ezek megfelelnek a megfelelőségnek, akkor a maximális skálázás lehetséges. Azon feladatok esetében, amelyek nem felelnek meg ezen profilok egyikének, a skálázási útmutató a jövőbeli frissítésekben lesz elérhető. Egyelőre a következő részben ismertetett általános útmutatást használhatja.

### <a name="compatibility-level-12---multi-step-query-with-different-partition-by-values"></a>Kompatibilitási szint 1,2 – többlépéses lekérdezés eltérő PARTÍCIÓval értékek alapján 
* Bemenet: Event hub 8 partícióval
* Kimenet: az Event hub 8 partícióval rendelkezik (a partíciós kulcs oszlopát a "TollBoothId" érték használatára kell beállítani)

Lekérdezés

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

A 1,2 kompatibilitási szint alapértelmezés szerint lehetővé teszi a párhuzamos lekérdezés-végrehajtást. Az előző szakasz lekérdezése például akkor parttioned, ha a "TollBoothId" oszlop bemeneti partíciós kulcsként van beállítva. A PARTITION BY ParttionId záradék használata nem kötelező.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Egy adott feladatokhoz tartozó maximális átviteli egységek kiszámítása
A Stream Analytics-feladatok által felhasználható folyamatos átviteli egységek teljes száma függ a feladatokhoz definiált lekérdezés és az egyes lépések partícióinak számától.

### <a name="steps-in-a-query"></a>A lekérdezés lépései
Egy lekérdezésnek egy vagy több lépése lehet. Az egyes lépések a **with** kulcsszó által definiált allekérdezések. **A (z) kulcsszón** kívüli lekérdezés (csak egy lekérdezés) szintén lépésként számít, például a **Select** utasítás a következő lekérdezésben:

Lekérdezés

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId
```

A lekérdezés két lépésből áll.

> [!NOTE]
> Ezt a lekérdezést a cikk későbbi részében részletesebben tárgyaljuk.
>  

### <a name="partition-a-step"></a>Lépés particionálása
Egy lépés particionálásához a következő feltételek szükségesek:

* A bemeneti forrást particionálni kell. 
* A lekérdezés **Select** utasítását particionált bemeneti forrásból kell olvasni.
* A lépésen belüli lekérdezésnek a **partíció kulcsszó alapján** kell szerepelnie.

Ha egy lekérdezés particionálva van, a rendszer feldolgozza és összesíti a bemeneti eseményeket külön partíciós csoportokba, és az egyes csoportokhoz generálja a kimenetek eseményeit. Ha összetett összesítést szeretne, hozzon létre egy második, nem particionált lépést az összesítéshez.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Egy adott feladatokhoz tartozó maximális átviteli egységek kiszámítása
A nem particionált lépések együttesen akár hat folyamatos átviteli egység (SUs) is méretezhetők egy Stream Analytics feladatokhoz. Emellett 6 SUs-t is hozzáadhat a particionált lépésekben található minden egyes partícióhoz.
Az alábbi táblázatban néhány **példát** láthat.

| Lekérdezés                                               | Maximális SUs a feladatokhoz |
| --------------------------------------------------- | ------------------- |
| <ul><li>A lekérdezés egy lépést tartalmaz.</li><li>A lépés nincs particionálva.</li></ul> | 6 |
| <ul><li>A bemeneti adatfolyamot a rendszer 16-ra particionálja.</li><li>A lekérdezés egy lépést tartalmaz.</li><li>A lépés particionálva van.</li></ul> | 96 (6 * 16 partíció) |
| <ul><li>A lekérdezés két lépést tartalmaz.</li><li>A lépések egyike sincs particionálva.</li></ul> | 6 |
| <ul><li>A bemeneti adatfolyamot 3-ra particionálja a rendszer.</li><li>A lekérdezés két lépést tartalmaz. A bemeneti lépés particionálva van, és a második lépés nem.</li><li>A <strong>Select</strong> utasítás beolvassa a particionált bemenetből.</li></ul> | 24 (18 – particionált lépések + 6 a nem particionált lépésekhez |

### <a name="examples-of-scaling"></a>Példák a skálázásra

A következő lekérdezés kiszámítja az autók számát egy három perces időszakon belül egy olyan autópályadíj-állomáson, amely három tollbooths rendelkezik. A lekérdezés legfeljebb hat SUs méretű lehet.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Ha több SUs-t szeretne használni a lekérdezéshez, a bemeneti adatfolyamot és a lekérdezést is particionálni kell. Mivel az adatfolyam-partíció 3 értékre van állítva, a következő módosított lekérdezés akár 18 SUs-ra is méretezhető:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Ha egy lekérdezés particionálva van, a bemeneti események feldolgozása és összesítése külön partíciós csoportokba történik. A rendszer az egyes csoportok kimeneti eseményeit is létrehozta. A particionálás bizonyos váratlan eredményekhez vezethet, ha a **Group By** mező nem a bemeneti adatfolyamban található partíciós kulcs. Például az előző lekérdezés **TollBoothId** mezője nem a **Input1**partíciós kulcsa. Ennek eredményeképpen a TollBooth #1 származó adatok több partíción is elterjedhetnek.

Az egyes **Input1** -partíciókat stream Analytics külön dolgozza fel a rendszer. Ennek eredményeképpen a rendszer az azonos Tollbooth tartozó számlálók több rekordját is létrehozhatja ugyanabban a kiesési ablakban. Ha a bemeneti partíció kulcsa nem módosítható, ez a probléma kijavítható úgy, hogy egy nem partíciós lépést ad hozzá a partíciók között, az alábbi példában látható módon:

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Ezt a lekérdezést 24 SUs-re lehet méretezni.

> [!NOTE]
> Ha két streamet csatlakoztat, győződjön meg arról, hogy a streamek particionálva lesznek az illesztések létrehozásához használt oszlop partíciós kulcsával. Győződjön meg arról is, hogy azonos számú partíció található mindkét adatfolyamban.
> 
> 

## <a name="achieving-higher-throughputs-at-scale"></a>Nagyobb átviteli sebességek elérése a skálán

Egy [zavaróan párhuzamos](#embarrassingly-parallel-jobs) feladatokra van szükség, de nem elegendő a nagyobb átviteli sebesség fenntartásához a skálán. Minden tárolási rendszer és a hozzá tartozó Stream Analytics kimenete a lehető legjobb írási sebesség eléréséhez szükséges változatokkal rendelkezik. A méretezési forgatókönyvekhez hasonlóan bizonyos kihívások is megoldhatók a megfelelő konfigurációk használatával. Ez a szakasz néhány gyakori kimenet konfigurációját ismerteti, és mintákat biztosít a másodpercenkénti 1K, 5K és 10K események betöltési arányának fenntartásához.

A következő megjegyzések egy állapot nélküli (átadó) lekérdezéssel rendelkező Stream Analytics feladatot használnak, amely egy alapszintű JavaScript UDF, amely az Event hub, az Azure SQL DB vagy a Cosmos DBba ír.

#### <a name="event-hub"></a>Eseményközpont

|Betöltési arány (események másodpercenként) | Folyamatos átviteli egységek | Kimeneti erőforrások  |
|--------|---------|---------|
| 1000     |    1    |  2 TU   |
| 5 kb     |    6    |  6 TU   |
| 10 ezer    |    12   |  10 TU  |

Az [Event hub](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs) -megoldás lineárisan méretezhető a folyamatos átviteli egységek (su) és az átviteli sebesség tekintetében, így a leghatékonyabb és a teljesítménybeli módszer a stream Analyticsból származó adatok elemzéséhez és továbbításához. A feladatok akár 192 SU-re is felméretezhetők, ami nagyjából 200 MB/s vagy napi 19 000 000 000 000 eseményt dolgoz fel.

#### <a name="azure-sql"></a>Azure SQL
|Betöltési arány (események másodpercenként) | Folyamatos átviteli egységek | Kimeneti erőforrások  |
|---------|------|-------|
|    1000   |   3  |  S3   |
|    5 kb   |   18 |  P4   |
|    10 ezer  |   36 |  P6   |

Az [Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql) támogatja az írást párhuzamosan, a particionálás öröklése néven, de alapértelmezés szerint nincs engedélyezve. A particionálás öröklésének engedélyezése azonban egy teljesen párhuzamos lekérdezéssel együtt nem elegendő a nagyobb átviteli sebesség eléréséhez. Az SQL írási átviteli sebessége jelentősen függ a SQL Azure adatbázis-konfigurációtól és a tábla sémájától. Az [SQL kimeneti teljesítményről](./stream-analytics-sql-output-perf.md) szóló cikk részletesebben ismerteti azokat a paramétereket, amelyek segítségével maximalizálható az írási sebesség. Ahogy az a [Azure stream Analytics kimenet Azure SQL Database](./stream-analytics-sql-output-perf.md#azure-stream-analytics) cikkben látható, ez a megoldás nem méretezhető lineárisan, mint a 8 partíción túli teljes párhuzamos feldolgozási folyamat, és szükség lehet az SQL-kimenet (lásd: [into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) újraparticionálására. A prémium SKU-kat a magas i/o-díjak fenntartásához, valamint a naplók biztonsági mentésével járó terheléshez kell elkészíteni néhány percenként.

#### <a name="cosmos-db"></a>Cosmos DB
|Betöltési arány (események másodpercenként) | Folyamatos átviteli egységek | Kimeneti erőforrások  |
|-------|-------|---------|
|  1000   |  3    | 20000 RU  |
|  5 kb   |  24   | 60K RU  |
|  10 ezer  |  48   | 120K RU |

A Stream Analytics [Cosmos db](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) kimenete frissítve lett, hogy natív integrációt használjon a [1,2 kompatibilitási szint](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12)alatt. A 1,2-es kompatibilitási szint jelentősen nagyobb átviteli sebességet tesz lehetővé, és csökkenti a 1,1-hoz képesti RU-felhasználást, amely az új feladatok alapértelmezett kompatibilitási szintje. A megoldás a/deviceId particionált CosmosDB-tárolókat használ, a többi megoldás pedig azonos módon van konfigurálva.

Az [Azure skálán lévő összes adatfolyam-továbbítási](https://github.com/Azure-Samples/streaming-at-scale) szolgáltatás a tesztelési ügyfeleket bemenetként szimuláló betöltéssel egy Event hub-t használ. Minden bemeneti esemény egy 1KB JSON-dokumentum, amely a konfigurált betöltési sebességet az átviteli sebesség (1MB/s, 5MB/s és 10 MB/s) alapján könnyedén lefordítja. Az események szimulálnak egy IoT-eszközt, amely a következő JSON-adatokat (rövidített formában) küldi el akár 1K-eszközökre:

```
{
    "eventId": "b81d241f-5187-40b0-ab2a-940faf9757c0",
    "complexData": {
        "moreData0": 51.3068118685458,
        "moreData22": 45.34076957651598
    },
    "value": 49.02278128887753,
    "deviceId": "contoso://device-id-1554",
    "type": "CO2",
    "createdAt": "2019-05-16T17:16:40.000003Z"
}
```

> [!NOTE]
> A konfigurációk a megoldásban használt különböző összetevők miatt változhatnak. A pontosabb becslés érdekében szabja testre a mintákat úgy, hogy illeszkedjenek a forgatókönyvhöz.

### <a name="identifying-bottlenecks"></a>Szűk keresztmetszetek azonosítása

A Azure Stream Analytics feladatok mérőszámok paneljén azonosíthatja a folyamat szűk keresztmetszeteit. Tekintse át a **bemeneti/kimeneti eseményeket** az átviteli sebesség és a "küszöbértékek [késleltetése"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) vagy a **várakozó események** között, és ellenőrizze, hogy a feladatban szerepel-e a bemeneti sebesség. Az Event hub mérőszámai esetében keresse meg a **szabályozott kérelmeket** , és ennek megfelelően módosítsa a küszöbértékeket. Cosmos DB metrikák esetében tekintse át a **maximálisan felhasznált ru/s** értékeit az átviteli sebesség alatt, hogy a partíciós kulcsok tartománya egységesen legyen felhasználva. Az Azure SQL DB esetében figyelje a **log IO** és a **CPU**-t.

## <a name="get-help"></a>Segítségkérés

További segítségért próbálja ki a [Azure stream Analytics fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

