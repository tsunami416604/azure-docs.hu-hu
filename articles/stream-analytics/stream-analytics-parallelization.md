---
title: Lekérdezéspárhuzamosítás és méretezés használata az Azure Stream Analytics szolgáltatásban
description: Ez a cikk ismerteti, hogyan skálázható stream analytics-feladatok a bemeneti partíciók konfigurálásával, a lekérdezésdefiníció finomhangolásával és a feladat streamelési egységek beállításával.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 31ac43ec796d305b8a8f4b62ea09481e262b6b3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256980"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>A lekérdezések párhuzamosításának kihasználása az Azure Stream Analytics szolgáltatásban
Ez a cikk bemutatja, hogyan használhatja ki a párhuzamosság előnyeit az Azure Stream Analytics-ben. Megtudhatja, hogyan skálázhatja a Stream Analytics-feladatokat a bemeneti partíciók konfigurálásával és az analytics-lekérdezésdefiníció finomhangolásával.
Előfeltételként érdemes lehet ismernie a Streaming Unit fogalmát, amelyet a [Streamelési egységek megértése és beállítása](stream-analytics-streaming-unit-consumption.md)ismertet.

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Melyek a Stream Analytics-feladat részei?
A Stream Analytics feladatdefiníciója bemeneteket, lekérdezéseket és kimeneteket tartalmaz. Bemenetek, ahol a feladat beolvassa az adatfolyamot. A lekérdezés az adatbeviteli adatfolyam átalakítására szolgál, és a kimenet az, amerre a feladat elküldi a feladat eredményeit.

Egy feladathoz legalább egy bemeneti forrás szükséges az adatfolyam-továbbításhoz. Az adatfolyam-bemeneti forrás tárolható egy Azure-eseményközpontban vagy az Azure blob storage-ban. További információ: [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md) és [az Azure Stream Analytics használatának első lépései című témakörben.](stream-analytics-real-time-fraud-detection.md)

## <a name="partitions-in-sources-and-sinks"></a>Partíciók forrásokban és fogadókban
A Stream Analytics-feladat méretezése kihasználja a bemeneti vagy kimeneti partíciók előnyeit. A particionálás lehetővé teszi az adatok felosztását partíciókulcs alapján részhalmazokra. Az adatokat felhasználó folyamat (például egy Streamanalytics-feladat) különböző partíciókat használhat fel és írhat párhuzamosan, ami növeli az átviteli forgalmat. 

### <a name="inputs"></a>Bemenetek
Minden Azure Stream Analytics-bemenet kihasználhatja a particionálás előnyeit:
-   EventHub (a partíciókulcsot explicit módon kell beállítani a PARTITION BY kulcsszósegítségével)
-   IoT Hub (a partíciókulcsot explicit módon kell beállítani a PARTITION BY kulcsszóhasználatával)
-   Blob Storage

### <a name="outputs"></a>Kimenetek

Amikor a Stream Analytics szolgáltatással dolgozik, kihasználhatja a kimenetek particionálásának előnyeit:
-   Azure Data Lake Storage
-   Azure Functions
-   Azure-tábla
-   Blob storage (explicit módon beállíthatja a partíciókulcsot)
-   Cosmos DB (explicit módon kell beállítani a partíciókulcsot)
-   Eseményközpontok (a partíciókulcsot explicit módon kell beállítani)
-   IoT Hub (explicit módon kell beállítani a partíciókulcsot)
-   Service Bus
- SQL és SQL Data Warehouse opcionális particionálással: további információ a [Kimenet az Azure SQL Database-be lapon.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf)

A Power BI nem támogatja a particionálást. Azonban továbbra is particionálhatja a bemenetet az ebben a [szakaszban](#multi-step-query-with-different-partition-by-values) leírtak szerint 

A partíciókról további információt az alábbi cikkekben talál:

* [Az Event Hubs-szolgáltatások áttekintése](../event-hubs/event-hubs-features.md#partitions)
* [Adatparticionálás](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Kínosan párhuzamos munkák
A *kínosan párhuzamos* feladat a leginkább skálázható forgatókönyv, amely az Azure Stream Analytics-ben van. A bemenet egy partícióját a lekérdezés egy példányához csatlakoztatja a kimenet egy partíciójával. Ez a párhuzamosság a következő követelményekkel rendelkezik:

1. Ha a lekérdezési logika attól függ, hogy ugyanazt a kulcsot dolgozza fel ugyanaz a lekérdezési példány, meg kell győződnie arról, hogy az események megy az azonos partícióra a bemeneti. Az Event Hubs vagy az IoT Hub esetén ez azt jelenti, hogy az eseményadatoknak meg kell határozniuk a **PartitionKey-értéket.** Másik lehetőségként particionált küldőket is használhat. Blob storage esetén ez azt jelenti, hogy az események ugyanabba a partíciómappába kerülnek. Ha a lekérdezési logika nem igényli ugyanazt a kulcsot, hogy ugyanazt a lekérdezési példány, figyelmen kívül hagyhatja ezt a követelményt. Erre a logikára példa egy egyszerű select-project-filter lekérdezés.  

2. Miután az adatok a bemeneti oldalon vannak elhelyezve, meg kell győződnie arról, hogy a lekérdezés particionálva van. Ehhez a **PARTITION BY-t** kell használnia az összes lépésben. Több lépés megengedett, de mindegyiknek ugyanazzal a kulccsal kell particionálnia őket. Az 1.0-s és 1.1-es kompatibilitási szint alatt a particionálási kulcsot **PartitionId-ra** kell állítani ahhoz, hogy a feladat teljesen párhuzamos legyen. Az 1.2-es vagy újabb kompatibilitási szintű feladatok esetén az egyéni oszlop partíciókulcsként adható meg a bemeneti beállításokban, és a feladat automatikusan paralellizálódik a PARTITION BY záradék nélkül is. Az eseményközpont kimeneténa a "Partíciókulcs oszlop" tulajdonságot a "PartitionId" használatára kell beállítani.

3. A legtöbb kimenetünk kihasználhatja a particionálás előnyeit, azonban ha olyan kimeneti típust használ, amely nem támogatja a feladat particionálását, nem lesz teljesen párhuzamos. Az Event Hub kimenetek esetén győződjön meg arról, hogy a **partíciókulcs oszlopa** megegyezik a lekérdezési partíciókulcséval. További részletekért tekintse meg a [kimeneti szakaszt.](#outputs)

4. A bemeneti partíciók számának meg kell egyeznie a kimeneti partíciók számával. Blob storage-kimenet támogatja a partíciókat, és örökli a particionálási sémát a felsőstream lekérdezés. Ha meg van adva egy partíciókulcs a Blob storage-hoz, az adatok bemeneti partíciónként particionálnak, így az eredmény továbbra is teljesen párhuzamos. Íme néhány példa partícióértékekre, amelyek teljesen párhuzamos feladatot engedélyeznek:

   * 8 eseményközpont-bemeneti partíció és 8 eseményközpont kimeneti partíciója
   * 8 eseményközpont bemeneti partíciója és blobstorage-kimenete
   * 8 eseményközpont-bemeneti partíció és blobstorage-kimenet, amelyet tetszőleges számossággal particionált egy egyéni mező
   * 8 blob storage bemeneti partíciók és blob storage kimenet
   * 8 blob storage bemeneti partíciók és 8 eseményközpont kimeneti partíciók

A következő szakaszok néhány példaforgatókönyveket vitatnak meg, amelyek kínosan párhuzamosak.

### <a name="simple-query"></a>Egyszerű lekérdezés

* Bemenet: 8 partícióval rendelkező eseményközpont
* Kimenet: 8 partícióval rendelkező eseményközpont ("Partíciós kulcsoszlop" a "PartitionId" használatára kell beállítani)

Lekérdezés:

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

Ez a lekérdezés egy egyszerű szűrő. Ezért nem kell aggódnia az eseményközpontba küldött bemenet particionálása miatt. Figyelje meg, hogy az 1.2 előtti kompatibilitási szinttel rendelkező feladatoknak tartalmazniuk kell **a PARTITION BY PartitionId záradékot,** így #2 a korábbi követelménynek. A kimenet, meg kell konfigurálni az eseményközpont kimenetét a feladatban, hogy a partíciókulcs **partitionid**. Az utolsó ellenőrzés annak ellenőrzése, hogy a bemeneti partíciók száma megegyezik-e a kimeneti partíciók számával.

### <a name="query-with-a-grouping-key"></a>Lekérdezés csoportosítási kulccsal

* Bemenet: 8 partícióval rendelkező eseményközpont
* Kimenet: Blob storage

Lekérdezés:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Ez a lekérdezés csoportosítási kulccsal rendelkezik. Ezért az együttesen csoportosított eseményeket ugyanahhoz az Event Hub-partícióhoz kell küldeni. Mivel ebben a példában a TollBoothID szerint csoportosítjuk, biztosnak kell lennünk abban, hogy a TollBoothID partíciókulcsként van-e használva, amikor az eseményeket az Event Hubra küldi. Ezután az ASA-ban használhatjuk **a PARTITION BY PartitionId-ot,** hogy örököljük ezt a partíciósémát, és engedélyezzük a teljes párhuzamosítást. Mivel a kimenet blob storage, nem kell aggódnia a partíciókulcs-érték konfigurálása miatt, a #4 követelménynek megfelelően.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Példa olyan forgatókönyvekre, amelyek *nem* kínosan párhuzamosak

Az előző részben néhány kínosan párhuzamos forgatókönyvet mutattunk be. Ebben a szakaszban olyan forgatókönyveket vitatünk meg, amelyek nem felelnek meg az összes követelménynek, hogy kínosan párhuzamosak legyenek. 

### <a name="mismatched-partition-count"></a>Nem egyező partíciók száma
* Bemenet: 8 partícióval rendelkező eseményközpont
* Kimenet: 32 partícióval rendelkező eseményközpont

Ebben az esetben nem számít, mi a lekérdezés. Ha a bemeneti partíciók száma nem felel meg a kimeneti partíciók száma, a topológia nem kínosan párhuzamos.+ Azonban továbbra is kaphatunk valamilyen szintet vagy párhuzamosítást.

### <a name="query-using-non-partitioned-output"></a>Nem particionált kimenettel rendelkező lekérdezés
* Bemenet: 8 partícióval rendelkező eseményközpont
* Kimenet: Power BI

A Power BI-kimenet jelenleg nem támogatja a particionálást. Ezért ez a forgatókönyv nem kínosan párhuzamos.

### <a name="multi-step-query-with-different-partition-by-values"></a>Többlépéses lekérdezés különböző PARTITION BY értékekkel
* Bemenet: 8 partícióval rendelkező eseményközpont
* Kimenet: 8 partícióval rendelkező eseményközpont

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

Mint látható, a második lépés a **TollBoothId** a particionálási kulcs. Ez a lépés nem ugyanaz, mint az első lépés, és ezért megköveteli tőlünk, hogy nem shuffle. 

Az előző példák néhány Stream Analytics-feladatot mutatnak be, amelyek megfelelnek (vagy nem) egy kínosan párhuzamos topológiának. Ha megfelelnek, akkor megvan a lehetőség a maximális skála. A feladatok, amelyek nem felelnek meg az egyik ilyen profilok, méretezési útmutatást lesz elérhető a jövőbeli frissítések. Egyelőre használja az általános útmutatást a következő szakaszokban.

### <a name="compatibility-level-12---multi-step-query-with-different-partition-by-values"></a>Kompatibilitási szint 1.2 - Többlépéses lekérdezés különböző PARTITION BY értékekkel 
* Bemenet: 8 partícióval rendelkező eseményközpont
* Kimenet: 8 partícióval rendelkező eseményközpont ("A partíciókulcs oszlopát" a "TollBoothId" használatára kell beállítani)

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

Az 1.2-es kompatibilitási szint alapértelmezés szerint lehetővé teszi a párhuzamos lekérdezések végrehajtását. Például az előző szakasz lekérdezése particionálódik, amíg a "TollBoothId" oszlop bemeneti partíciókulcsként van beállítva. Partition by PartitionId záradék nem szükséges.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Egy feladat maximális streamelési egységének kiszámítása
A Stream Analytics-feladat által használható streamelési egységek teljes száma a feladathoz definiált lekérdezésben megadott lépések számától és az egyes lépésekhez szükséges partíciók számától függ.

### <a name="steps-in-a-query"></a>A lekérdezés lépései
A lekérdezésnek lehet egy vagy több lépése. Minden lépés a **WITH** kulcsszó által meghatározott segédlekérdezés. A **WITH** kulcsszón kívül lévő lekérdezés (csak egy lekérdezés) szintén lépésnek számít, például a KÖVETKEZŐ lekérdezés **SELECT** utasítása:

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
> Ezt a lekérdezést a cikk későbbi részében részletesebben tárgyaljuk.
>  

### <a name="partition-a-step"></a>Lépés particionálása
Egy lépés particionálásához a következő feltételek szükségesek:

* A bemeneti forrást particionálni kell. 
* A lekérdezés **SELECT** utasításának particionált bemeneti forrásból kell olvasnia.
* A lépésen belüli lekérdezésnek rendelkeznie kell a **PARTITION BY** kulcsszóval.

A lekérdezés particionálásakor a bemeneti események feldolgozása és összesítése külön partíciócsoportokban történik, és a kimeneti események az egyes csoportokhoz jönnek létre. Ha kombinált összesítést szeretne, létre kell hoznia egy második, nem particionált lépést az összesítéshez.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>A feladat maximális streamelési egységeinek kiszámítása
Az összes nem particionált lépés együttesen legfeljebb hat streamelési egység (SUs) skálázható egy Stream Analytics-feladathoz. Ezen kívül 6 SUs-t adhat hozzá minden partícióhoz egy particionált lépésben.
Néhány **példát** az alábbi táblázatban láthat.

| Lekérdezés                                               | Max SUs a feladathoz |
| --------------------------------------------------- | ------------------- |
| <ul><li>A lekérdezés egy lépést tartalmaz.</li><li>A lépés nincs particionálva.</li></ul> | 6 |
| <ul><li>A bemeneti adatfolyam particionált 16.</li><li>A lekérdezés egy lépést tartalmaz.</li><li>A lépés particionálva van.</li></ul> | 96 (6 * 16 partíció) |
| <ul><li>A lekérdezés két lépést tartalmaz.</li><li>Egyik lépés sincs particionálva.</li></ul> | 6 |
| <ul><li>A bemeneti adatfolyamot 3 particionálja.</li><li>A lekérdezés két lépést tartalmaz. A bemeneti lépés particionált, a második lépés pedig nem.</li><li>A <strong>SELECT</strong> utasítás a particionált bemenetről olvas.</li></ul> | 24 (18 particionált lépések + 6 a nem particionált lépések |

### <a name="examples-of-scaling"></a>Példák méretezésre

A következő lekérdezés kiszámítja az autók számát egy három perces ablakon belül, amely három fizetőállomással rendelkezik. Ez a lekérdezés legfeljebb hat SUs skálázható.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Ha több SUs-t szeretne használni a lekérdezéshez, mind a bemeneti adatfolyamot, mind a lekérdezést particionálni kell. Mivel az adatfolyam-partíció 3-ra van állítva, a következő módosított lekérdezés legfeljebb 18 SUs-ra skálázható:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

A lekérdezés particionálásakor a bemeneti események feldolgozása és összesítése külön partíciócsoportokban történik. Kimeneti események is jönnek létre az egyes csoportok. A particionálás váratlan eredményeket okozhat, ha a **GROUP BY** mező nem a bemeneti adatfolyam partíciókulcsa. Például az előző lekérdezés **TollBoothId mezője** nem az **Input1**partíciókulcsa. Az eredmény az, hogy a TollBooth #1 adatai több partícióra is terjeszthetők.

Az **Input1** partíciók mindegyikét a Stream Analytics külön dolgozza fel. Ennek eredményeképpen az ugyanazon a tumbling ablakban az azonos fizetőkapu hoz létre több rekordot az autószámról. Ha a bemeneti partíciókulcs nem módosítható, ez a probléma megoldható egy nem partíciós lépéssel a partíciók közötti értékek összesítéséhez, mint például a következő példában:

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

Ez a lekérdezés 24 SUs-ra méretezhető.

> [!NOTE]
> Ha két adatfolyamhoz csatlakozik, győződjön meg arról, hogy az adatfolyamokat az illesztések létrehozásához használt oszlop partíciókulcsa particionálja. Győződjön meg arról is, hogy mindkét adatfolyamban azonos számú partícióvan.
> 
> 

## <a name="achieving-higher-throughputs-at-scale"></a>Nagyobb átmenők elérése nagy léptékben

Egy [kínosan párhuzamos](#embarrassingly-parallel-jobs) feladat szükséges, de nem elegendő a nagyobb átviteli nagy léptékű fenntartani. Minden tárolórendszer és a hozzá tartozó Stream Analytics-kimenet a lehető legjobb írási átviteli teljesítmény elérésének módját is eltérően adja meg. Mint minden skálázási forgatókönyv, vannak olyan kihívások, amelyek a megfelelő konfigurációk használatával megoldhatók. Ez a szakasz néhány gyakori kimenet konfigurációját ismerteti, és mintákat biztosít az 1K, 5K és 10K események másodpercenkénti lenyelési sebességének fenntartásához.

A következő megfigyelések egy Stream Analytics-feladatot használnak állapotnélküli (áteresztő) lekérdezéssel, egy alapvető JavaScript UDF-et, amely az Event Hub, az Azure SQL DB vagy a Cosmos DB rendszerbe ír.

#### <a name="event-hub"></a>Eseményközpont

|Benyelési arány (események másodpercenként) | Streamelési egységek | Kimeneti erőforrások  |
|--------|---------|---------|
| 1E     |    1    |  2 TU   |
| 5E     |    6    |  6 TU   |
| 10 E    |    12   |  10 TU  |

Az [Event Hub-megoldás](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs) lineárisan skálázható a streamelési egységek (SU) és az átviteli teljesítmény tekintetében, így ez a leghatékonyabb és legeredményesebb módja az adatok elemzésének és a Stream Analytics-ből való streamelésének. A feladatok akár 192 SU-ra is méretezhetők, ami nagyjából 200 MB/s vagy napi 19 billió esemény feldolgozását jelenti.

#### <a name="azure-sql"></a>Azure SQL
|Benyelési arány (események másodpercenként) | Streamelési egységek | Kimeneti erőforrások  |
|---------|------|-------|
|    1E   |   3  |  S3   |
|    5E   |   18 |  P4   |
|    10 E  |   36 |  P6   |

[Az Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql) támogatja a párhuzamos írást, amelyet particionálás öröklésnek neveznek, de alapértelmezés szerint nincs engedélyezve. Azonban a particionálás öröklésének engedélyezése egy teljesen párhuzamos lekérdezéssel együtt nem feltétlenül elegendő a nagyobb átviteli eredmények eléréséhez. Az SQL-írási átviteli műveletek jelentősen függnek az SQL Azure-adatbázis konfigurációjától és a táblasémától. Az [SQL output teljesítmény](./stream-analytics-sql-output-perf.md) cikk részletesebben ismerteti a paramétereket, amelyek maximalizálják az írási átviteli. Amint azt az [Azure Stream Analytics kimenet](./stream-analytics-sql-output-perf.md#azure-stream-analytics) az Azure SQL Database cikkében, ez a megoldás nem skálázható lineárisan, mint egy teljesen párhuzamos folyamat túl 8 partíciót, és szükség lehet újraparticionálás előtt SQL kimenet (lásd [INTO).](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) Prémium szintű sk-ek szükségesek a magas I/O-díjak fenntartásához, valamint a naplóbiztonsági mentések néhány percenként történő terhelésének fenntartásához.

#### <a name="cosmos-db"></a>Cosmos DB
|Benyelési arány (események másodpercenként) | Streamelési egységek | Kimeneti erőforrások  |
|-------|-------|---------|
|  1E   |  3    | 20K RU  |
|  5E   |  24   | 60k RU  |
|  10 E  |  48   | 120ezer RU |

A Stream Analytics [Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) kimenete frissítve lett, hogy az [1.2-es kompatibilitási szinten](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12)natív integrációt használjon. Az 1.2-es kompatibilitási szint jelentősen nagyobb átviteli értéket tesz lehetővé, és csökkenti a vt.-fogyasztást az 1.1-hez képest, ami az új feladatok alapértelmezett kompatibilitási szintje. A megoldás a /deviceId partíciós CosmosDB-tárolókat használja, és a megoldás többi része azonos módon van konfigurálva.

Minden [streamelés scale azure-minták](https://github.com/Azure-Samples/streaming-at-scale) egy Event Hub adagolással a tesztügyfelek bemeneti szimulálásával. Minden bemeneti esemény egy 1 KB-os JSON-dokumentum, amely a konfigurált betöltési sebességet könnyen átviteli sebességre (1 MB/s, 5MB/s és 10MB/s) fordítja le. Az események a következő JSON-adatokat küldő IoT-eszközt szimulálják (rövidített formában) legfeljebb 1K-eszközökön:

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
> A konfigurációk változhatnak a megoldásban használt különböző összetevők miatt. A pontosabb becslésérdekében testre szabhatja a mintákat a forgatókönyvnek megfelelően.

### <a name="identifying-bottlenecks"></a>A szűk keresztmetszetek azonosítása

Az Azure Stream Analytics-feladat Metrikák ablaktáblája segítségével azonosíthatja a folyamat szűk keresztmetszeteit. Tekintse át **a bemeneti/kimeneti eseményeket** az átviteli sebességhez és a ["Vízjel késleltetése"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) vagy **az eldugott eseményeket,** és nézze meg, hogy a feladat lépést tart-e a bemeneti sebességgel. Az Event Hub-metrikák esetében keresse **meg a szabályozott kérelmeket,** és ennek megfelelően módosítsa a küszöbértékegységeket. A Cosmos DB metrikák, tekintse át **a maximális felhasznált RU/s partíciókulcs-tartományban** az átviteli sebesség, győződjön meg arról, hogy a partíciókulcs-tartományok egyenletesen fogyasztva. Az Azure SQL DB esetén figyelheti **a Log IO** és a CPU **monitorát.**

## <a name="get-help"></a>Segítségkérés

További segítségért próbálja ki [az Azure Stream Analytics fórumunkat.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
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

