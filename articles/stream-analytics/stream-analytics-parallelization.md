---
title: Lekérdezés párhuzamos feldolgozás és a skála használata az Azure Stream Analytics szolgáltatásban
description: Ez a cikk azt ismerteti, hogyan méretezhetők a Stream Analytics-feladatok a bemeneti partíció konfigurálásával, a lekérdezés definíciójának finomhangolásával és folyamatos átviteli egységek feladat beállítása.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: d1afb6037b5fc290de93faba405982ebd1fb68ea
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364562"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Használja ki az Azure Stream Analytics lekérdezési ezerszer
Ez a cikk bemutatja, hogyan ezerszer kihasználásához az Azure Stream Analytics szolgáltatásban. Megismerheti a Stream Analytics-feladatok méretezése a bemeneti partíció konfigurálásával, valamint a elemzési lekérdezés definíciójának finomhangolásával.
Előfeltételként érdemes megismerni a [folyamatos átviteli egységek ismertetése és módosítása](stream-analytics-streaming-unit-consumption.md)című témakörben ismertetett folyamatos átviteli egység fogalmát.

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Mik azok a Stream Analytics-feladat részeit?
A Stream Analytics-feladat definíciója bemenetei között, egy lekérdezés és a kimenet tartalmazza. Bemenetek, ahol a feladat beolvassa az adatokat a streamből. A lekérdezés szolgál átalakítja a bemeneti streamből, és a kimenete, ahol a feladat küld-e a feladat eredményeket.

Egy feladathoz legalább egy bemeneti forrás a streamelési adatok. A stream bemeneti adatforrás tárolhatók az Azure event hub vagy az Azure blob storage-ban. További információ: [a Azure stream Analytics bemutatása](stream-analytics-introduction.md) és a [Azure stream Analytics használatának első lépései](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-sources-and-sinks"></a>A forrásként és fogadóként partíciók
Egy Stream Analytics-feladat skálázás kihasználja a bemeneti vagy kimeneti partíciók. A particionálás lehetővé teszi, hogy részekre adatok partíciókulcs alapján. Egy folyamat, amely felhasználja az adatokat (például Stream Analytics-feladat) is használják, és a különböző partíciók írási párhuzamosan, ami növeli az átviteli sebességet. 

### <a name="inputs"></a>Bevitelek
Az összes Azure Stream Analytics beviteli kihasználhatják a particionálás:
-   Az EventHub (kell beállítani a partíciós kulcs explicit módon a PARTITION BY kulcsszóra)
-   Az IoT Hub (kell beállítani a partíciós kulcs explicit módon a PARTITION BY kulcsszóra)
-   Blob Storage

### <a name="outputs"></a>Kimenetek

Használatakor a Stream Analytics-szel, igénybe veheti a kimenetekben particionálás:
-   Azure Data Lake Storage
-   Azure Functions
-   Azure-tábla
-   A BLOB storage (állíthatja a partíciós kulcs explicit módon)
-   Cosmos DB (explicit módon be kell állítania a partíció kulcsát)
-   Event Hubs (explicit módon be kell állítania a partíció kulcsát)
-   Az IoT Hub (állítsa be a partíciós kulcs explicit módon kell)
-   Service Bus
- SQL és SQL Data Warehouse választható particionálással: További információ a [kimenetről Azure SQL Database lapra](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf).

A Power BI nem támogatja a particionálást. A bemenet azonban továbbra is particionálható a [jelen szakaszban](#multi-step-query-with-different-partition-by-values) leírtak szerint. 

A partíciók kapcsolatos további információkért lásd a következő cikkeket:

* [Az Event Hubs-szolgáltatások áttekintése](../event-hubs/event-hubs-features.md#partitions)
* [Adatparticionálás](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Zavaróan párhuzamos feladatok
Egy *kínosan párhuzamos* feladatokkal a leginkább méretezhető forgatókönyv van Azure stream Analyticsban. A kimenet egy partíciót egy partíciót a bemenet egyetlen példányban a lekérdezés csatlakozik. A párhuzamosság követelményei a következők:

1. Ha a lekérdezés logikája attól függ, hogy ugyanazzal a kulccsal ugyanazon lekérdezés által feldolgozott, győződjön meg arról, hogy az események nyissa meg a bemeneti ugyanazon a partíción. Event Hubs vagy IoT Hub esetén ez azt jelenti, hogy az események **PartitionKey** értékének meg kell egyeznie. A particionált feladók is használhatja. A blob Storage Ez azt jelenti, hogy az események küldhetők partíció ugyanabban a mappában. Ha a lekérdezés logikája nem igényel ugyanazon lekérdezés által feldolgozandó ugyanazzal a kulccsal, figyelmen kívül hagyhatja ezt a követelményt. Egy példa a logikai lenne egy egyszerű válassza project-szűrő lekérdezés.  

2. Miután az adatokat a bemeneti oldalon van leírva, győződjön meg arról, hogy a lekérdezés particionálva van. Ehhez a **partíciókat** kell használnia az összes lépésben. Több lépést engedélyezettek, de mindegyikük által ugyanazzal a kulccsal kell particionálni. A 1,0 és a 1,1 kompatibilitási szint alatt a particionálási kulcsot a **PartitionID** értékre kell beállítani ahhoz, hogy a feladatnak teljesen párhuzamosnak kell lennie. A 1,2-es és újabb compatility-szintű feladatokhoz egyéni oszlop adható meg partíciós kulcsként a bemeneti beállításokban, a feladat pedig paralellized automatikusan, a PARTITION BY záradék nélkül is. Az Event hub kimenete esetében a "Partition Key Column" tulajdonságot "PartitionId" értékre kell beállítani.

3. A kimenet a legtöbb kihasználhatják a particionálás, azonban egy kimeneti típus használata, amely nem támogatja a particionálást a feladat nem lesz teljes mértékben párhuzamos. További részletekért tekintse meg a [kimenet szakaszt](#outputs) .

4. A bemeneti partíciók számának egyenlőnek kell lennie a kimeneti partíciók száma. A BLOB storage-kimenet is támogatja a partíciókat, és örökli a felsőbb rétegbeli lekérdezés particionálási sémát. Ha egy partíciókulcsot, a Blob storage meg van adva, adatok particionálása bemeneti partíciónként így ez továbbra is teljes mértékben párhuzamos. Az alábbiakban néhány lehetséges partíció érték, amely egy teljes körűen párhuzamos feladat engedélyezése:

   * 8 event hub bemeneti partíció, és 8 eseményközpont kimeneti partíciók
   * 8 event hub bemeneti partíció- és blob storage-kimenet
   * 8 event hub bemeneti partíciók és a egy egyéni mezőt dokumentumtárolási tetszőleges számossága blob storage-kimenet
   * a bemeneti partíció 8 blob storage és a blob storage-kimenet
   * 8 blob storage bemeneti partíciók és a 8 event hub kimeneti partíció

Az alábbi szakaszok tárgyalják a példákat megtekinteni, amelyek zavaróan párhuzamos.

### <a name="simple-query"></a>Egyszerű lekérdezés

* Bemenet: Eseményközpont 8 partícióval rendelkező
* Kimenet: az Event hub 8 partícióval rendelkezik (a partíciós kulcs oszlopát a "PartitionId" érték használatára kell beállítani)

Lekérdezés:

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

Ez a lekérdezés egy egyszerű szűrő. Ezért nem kell aggódnia a bemenet, amely küld az event hubs particionálási. Figyelje meg, hogy az 1,2 előtt a kompatibilitási szinttel rendelkező feladatok között szerepelnie kell a **Partition by PartitionID** záradéknak, így a korábbitól #2 követelménynek eleget tesz. A kimenethez konfigurálnia kell az Event hub kimenetét a feladatokban, hogy a partíciós kulcs **PartitionID**legyen beállítva. Egy utolsó ellenőrzés, hogy ellenőrizze, hogy a bemeneti partíciók számának kimeneti partíciók száma egyenlő.

### <a name="query-with-a-grouping-key"></a>A csoportosítás kulccsal lekérdezése

* Bemenet: Eseményközpont 8 partícióval rendelkező
* A kimenetre: A Blob storage

Lekérdezés:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Ez a lekérdezés csoportosítási kulccsal rendelkezik. Ezért az események csoportosítva el kell küldeni az Eseményközpont ugyanazon a partíción. Ebben a példában a Microsoft csoportosítási szempont TollBoothID, mivel azt kell róla, hogy TollBoothID használja partíciókulcsként, ha az események eseményközpontba érkeznek. Ebben az ASA-ben a **Partition by PartitionID** használatával örökölheti a partíciós sémát, és engedélyezheti a teljes párhuzamos. Mivel a kimeneti blob storage-ba, nem kell aggódnia a partíciókulcs-értékkel, #4. követelmény alapján konfigurálása.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Példa olyan forgatókönyvekre, amelyek *nem* zavaróan párhuzamosak

Az előző szakaszban megmutattuk néhány zavaróan párhuzamos forgatókönyvet. Ebben a szakaszban bemutatjuk a forgatókönyvek, amelyek nem felelnek meg az összes követelmények zavaróan párhuzamos. 

### <a name="mismatched-partition-count"></a>Nem egyező partíciók száma
* Bemenet: Eseményközpont 8 partícióval rendelkező
* Kimenete: Eseményközpont 32 partícióval rendelkező

Ebben az esetben nem számít, mi az a lekérdezést. Ha a bemeneti partíciók száma nem egyezik meg a kimeneti partíciók száma, a topológia nem zavaróan párhuzamos. + azonban továbbra is kapunk bizonyos szintű vagy folyamatkezelést biztosítja.

### <a name="query-using-non-partitioned-output"></a>Lekérdezés kimenete nem particionált használatával
* Bemenet: Eseményközpont 8 partícióval rendelkező
* Kimenet: Power BI

Power BI a kimenet jelenleg nem támogatja a particionálást. Ezért az ebben a forgatókönyvben nem zavaróan párhuzamos.

### <a name="multi-step-query-with-different-partition-by-values"></a>Többlépéses lekérdezés eltérő PARTITION BY értékek
* Bemenet: Eseményközpont 8 partícióval rendelkező
* Kimenete: Eseményközpont 8 partícióval rendelkező

Lekérdezés:

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

Ahogy láthatja, a második lépés a **TollBoothId** használja a particionálási kulcsként. Ez a lépés nem ugyanaz, mint az első lépés, és, ezért megköveteli tőlünk egy shuffle tennie. 

Az előző példák azt mutatják néhány Stream Analytics-feladatokat, amelyeket egy zavaróan párhuzamos topológia felel meg (vagy nem). Ha azok megfelelnek, a lehetséges maximális méretezési rendelkeznek. Frissíti a feladatok, amelyek nem egyeznek meg ezeket a profilokat, útmutatást skálázás egyikét a jövőben elérhető lesz. Most használja az alábbi szakaszok az általános útmutatást.

### <a name="compatibility-level-12---multi-step-query-with-different-partition-by-values"></a>Kompatibilitási szint 1,2 – többlépéses lekérdezés eltérő PARTÍCIÓval értékek alapján 
* Bemenet: Eseményközpont 8 partícióval rendelkező
* Kimenet: az Event hub 8 partícióval rendelkezik (a partíciós kulcs oszlopát a "TollBoothId" érték használatára kell beállítani)

Lekérdezés:

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

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>A folyamatos átviteli egység egy feladat maximális kiszámítása
Egy Stream Analytics-feladat által használt streamelési egységek teljes száma attól függ, hogy a lekérdezésben, a feladat, illetve az egyes lépések a partíciók számának megadott lépéseket.

### <a name="steps-in-a-query"></a>A lekérdezés lépései
A lekérdezés egy vagy több lépést rendelkezhet. Az egyes lépések a **with** kulcsszó által definiált allekérdezések. **A (z) kulcsszón** kívüli lekérdezés (csak egy lekérdezés) szintén lépésként számít, például a **Select** utasítás a következő lekérdezésben:

Lekérdezés:

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

Ez a lekérdezés két lépésből áll.

> [!NOTE]
> Ez a lekérdezés a cikk későbbi részében részletesebben tárgyalja.
>  

### <a name="partition-a-step"></a>A partíció egy lépés
Particionálás egy lépés szükséges a következő feltételeknek:

* A bemeneti forrás kell particionálni. 
* A lekérdezés **Select** utasítását particionált bemeneti forrásból kell olvasni.
* A lépésen belüli lekérdezésnek a **partíció kulcsszó alapján** kell szerepelnie.

A lekérdezés particionálva van, amikor a bemeneti események feldolgozása és az összesített külön partíciócsoportok, és kimenetek események az egyes csoportok jönnek létre. Ha azt szeretné, hogy egy kombinált összesítés, létre kell hoznia egy nem particionált második lépése az összesítést.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>A folyamatos átviteli feladat adattárházegységeinek maximális kiszámítása
Minden nem particionált lépést együtt méretezhető egy Stream Analytics-feladat legfeljebb hat a folyamatos átviteli egységek (su). Emellett adja hozzá mindegyik partíció 6 SUS-t egy particionált lépésben.
Az alábbi táblázatban néhány **példát** láthat.

| Lekérdezés                                               | Maximális SUS-t a projekthez |
| --------------------------------------------------- | ------------------- |
| <ul><li>A lekérdezés tartalmaz egy lépésben.</li><li>A lépés nincs particionálva.</li></ul> | 6 |
| <ul><li>A bemeneti streamből való 16 particionálva van.</li><li>A lekérdezés tartalmaz egy lépésben.</li><li>A lépés particionálva van.</li></ul> | 96 (6 * 16 partíciók) |
| <ul><li>A lekérdezés két lépést tartalmaz.</li><li>A lépések egyike sem particionálva van.</li></ul> | 6 |
| <ul><li>A bemeneti streamet 3 particionálva van.</li><li>A lekérdezés két lépést tartalmaz. A bemeneti lépés particionálva van, és a második lépésben nincs.</li><li>A <strong>Select</strong> utasítás beolvassa a particionált bemenetből.</li></ul> | a particionált lépéseket (18 + 6. lépéseket nem particionált 24 |

### <a name="examples-of-scaling"></a>Példák a skálázás

A következő lekérdezést a autók alatt áll, amely rendelkezik három tollbooths díjmentesen állomás három perces időtartamon belül számítja ki. Ez a lekérdezés legfeljebb hat SUs skálázhatók.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

A lekérdezés több SUS-t használja, hogy mind a bemeneti streamet, és a lekérdezés kell particionálni. Mivel az adatokat a stream partíció 3 értékre van állítva, a következő módosított lekérdezés legfeljebb 18 SUs skálázhatók:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Ha a lekérdezés particionálva van, a bemeneti események feldolgozása, és összevonva jelenik meg különálló partíció csoportok. Kimeneti események is jönnek létre az egyes csoportok. A particionálás bizonyos váratlan eredményekhez vezethet, ha a **Group By** mező nem a bemeneti adatfolyamban található partíciós kulcs. Például az előző lekérdezés **TollBoothId** mezője nem a **Input1**partíciós kulcsa. Ez több partíciót őrbódét 1 származó adatok lehetnek elosztva.

Az egyes **Input1** -partíciókat stream Analytics külön dolgozza fel a rendszer. Ennek eredményeképpen az ugyanazon őrbódét ugyanaz az Átfedésmentes ablak az autó számát, több rekordot hoz létre. A bemeneti partíciós kulcs nem módosítható, ha a probléma lehet meghatározni egy nem partíció lépés hozzáadásával az összesített értékek partíciókon, az alábbi példában látható módon:

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

Ez a lekérdezés 24 SUS-t is méretezhető.

> [!NOTE]
> Ha két adatfolyamot csatlakozik, ügyeljen arra, hogy az adatfolyamok a partíciókulcsot, amellyel a kimásolhat oszlop szerint vannak particionálva. Győződjön meg arról, hogy rendelkezik-e azonos számú partíciót a mindkét streameket is.
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
| 10E    |    12   |  10 TU  |

Az [Event hub](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs) -megoldás lineárisan méretezhető a folyamatos átviteli egységek (su) és az átviteli sebesség tekintetében, így a leghatékonyabb és a teljesítménybeli módszer a stream Analyticsból származó adatok elemzéséhez és továbbításához. A feladatok akár 192 SU-re is felméretezhetők, ami nagyjából 200 MB/s vagy napi 19 000 000 000 000 eseményt dolgoz fel.

#### <a name="azure-sql"></a>Azure SQL
|Betöltési arány (események másodpercenként) | Folyamatos átviteli egységek | Kimeneti erőforrások  |
|---------|------|-------|
|    1000   |   3  |  S3   |
|    5 kb   |   18 |  P4   |
|    10E  |   36 |  P6   |

Az [Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql) támogatja az írást párhuzamosan, a particionálás öröklése néven, de alapértelmezés szerint nincs engedélyezve. A particionálás öröklésének engedélyezése azonban egy teljesen párhuzamos lekérdezéssel együtt nem elegendő a nagyobb átviteli sebesség eléréséhez. Az SQL írási átviteli sebessége jelentősen függ a SQL Azure adatbázis-konfigurációtól és a tábla sémájától. Az [SQL kimeneti teljesítményről](./stream-analytics-sql-output-perf.md) szóló cikk részletesebben ismerteti azokat a paramétereket, amelyek segítségével maximalizálható az írási sebesség. Ahogy az a [Azure stream Analytics kimenet Azure SQL Database](./stream-analytics-sql-output-perf.md#azure-stream-analytics) cikkben látható, ez a megoldás nem méretezhető lineárisan, mint a 8 partíción túli teljes párhuzamos feldolgozási folyamat, és szükség lehet az SQL-kimenet (lásd: [into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) újraparticionálására. A prémium SKU-kat a magas i/o-díjak fenntartásához, valamint a naplók biztonsági mentésével járó terheléshez kell elkészíteni néhány percenként.

#### <a name="cosmos-db"></a>Cosmos DB
|Betöltési arány (események másodpercenként) | Folyamatos átviteli egységek | Kimeneti erőforrások  |
|-------|-------|---------|
|  1000   |  3    | 20000 RU  |
|  5 kb   |  24   | 60K RU  |
|  10E  |  48   | 120K RU |

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

