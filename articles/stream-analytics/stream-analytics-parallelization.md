---
title: Lekérdezés párhuzamos feldolgozás és a skála használata az Azure Stream Analytics szolgáltatásban
description: Ez a cikk azt ismerteti, hogyan méretezhetők a Stream Analytics-feladatok a bemeneti partíció konfigurálásával, a lekérdezés definíciójának finomhangolásával és folyamatos átviteli egységek feladat beállítása.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 0b68819ba032d7655433aadd30fe2852941096ce
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000546"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Használja ki az Azure Stream Analytics lekérdezési ezerszer
Ez a cikk bemutatja, hogyan ezerszer kihasználásához az Azure Stream Analytics szolgáltatásban. Megismerheti a Stream Analytics-feladatok méretezése a bemeneti partíció konfigurálásával, valamint a elemzési lekérdezés definíciójának finomhangolásával.
Előfeltételként ismertetett folyamatos átviteli egység fogalma ismernie kell a érdemes [megismerés és módosítsa a folyamatos átviteli egységek](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Mik azok a Stream Analytics-feladat részeit?
A Stream Analytics-feladat definíciója bemenetei között, egy lekérdezés és a kimenet tartalmazza. Bemenetek, ahol a feladat beolvassa az adatokat a streamből. A lekérdezés szolgál átalakítja a bemeneti streamből, és a kimenete, ahol a feladat küld-e a feladat eredményeket.  

Egy feladathoz legalább egy bemeneti forrás a streamelési adatok. A stream bemeneti adatforrás tárolhatók az Azure event hub vagy az Azure blob storage-ban. További információkért lásd: [Azure Stream Analytics bemutatása](stream-analytics-introduction.md) és [első lépései az Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-sources-and-sinks"></a>A forrásként és fogadóként partíciók
Egy Stream Analytics-feladat skálázás kihasználja a bemeneti vagy kimeneti partíciók. A particionálás lehetővé teszi, hogy részekre adatok partíciókulcs alapján. Egy folyamat, amely felhasználja az adatokat (például Stream Analytics-feladat) is használják, és a különböző partíciók írási párhuzamosan, ami növeli az átviteli sebességet. 

### <a name="inputs"></a>Bemenetek
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
-   A cosmos DB (állítsa be a partíciós kulcs explicit módon kell)
-   Az Event Hubs (állítsa be a partíciós kulcs explicit módon kell)
-   Az IoT Hub (állítsa be a partíciós kulcs explicit módon kell)
-   Service Bus
- SQL és az SQL Data Warehouse a particionálás nem kötelező: a további információ a [kimeneti Azure SQL Database oldalára](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf).

A Power BI nem támogatja a particionálást. Azonban továbbra is particionáló a bemeneti leírtak szerint [ebben a szakaszban](#multi-step-query-with-different-partition-by-values) 

A partíciók kapcsolatos további információkért lásd a következő cikkeket:

* [Az Event Hubs-szolgáltatások áttekintése](../event-hubs/event-hubs-features.md#partitions)
* [Adatparticionálás](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Zavaróan párhuzamos feladatok
Egy *zavaróan párhuzamos* feladat az Azure Stream Analytics a leginkább méretezhető forgatókönyv. A kimenet egy partíciót egy partíciót a bemenet egyetlen példányban a lekérdezés csatlakozik. A párhuzamosság követelményei a következők:

1. Ha a lekérdezés logikája attól függ, hogy ugyanazzal a kulccsal ugyanazon lekérdezés által feldolgozott, győződjön meg arról, hogy az események nyissa meg a bemeneti ugyanazon a partíción. Az Event Hubs vagy IoT hubot, ez azt jelenti, hogy az eseményadatokat kell rendelkeznie a **PartitionKey** set érték. A particionált feladók is használhatja. A blob Storage Ez azt jelenti, hogy az események küldhetők partíció ugyanabban a mappában. Ha a lekérdezés logikája nem igényel ugyanazon lekérdezés által feldolgozandó ugyanazzal a kulccsal, figyelmen kívül hagyhatja ezt a követelményt. Egy példa a logikai lenne egy egyszerű válassza project-szűrő lekérdezés.  

2. Miután az adatokat a bemeneti oldalon van leírva, győződjön meg arról, hogy a lekérdezés particionálva van. Ez megköveteli, hogy használjon **PARTITION BY** az összes lépését. Több lépést engedélyezettek, de mindegyikük által ugyanazzal a kulccsal kell particionálni. Jelenleg a particionálókulcs értékre kell állítani **PartitionId** ahhoz, hogy a feladat teljes mértékben párhuzamosan.  

3. A kimenet a legtöbb kihasználhatják a particionálás, azonban egy kimeneti típus használata, amely nem támogatja a particionálást a feladat nem lesz teljes mértékben párhuzamos. Tekintse meg a [kimeneti szakasz](#outputs) további részletekért.

4. A bemeneti partíciók számának egyenlőnek kell lennie a kimeneti partíciók száma. A BLOB storage-kimenet is támogatja a partíciókat, és örökli a felsőbb rétegbeli lekérdezés particionálási sémát. Ha egy partíciókulcsot, a Blob storage meg van adva, adatok particionálása bemeneti partíciónként így ez továbbra is teljes mértékben párhuzamos. Az alábbiakban néhány lehetséges partíció érték, amely egy teljes körűen párhuzamos feladat engedélyezése:

   * 8 event hub bemeneti partíció, és 8 eseményközpont kimeneti partíciók
   * 8 event hub bemeneti partíció- és blob storage-kimenet
   * 8 event hub bemeneti partíciók és a egy egyéni mezőt dokumentumtárolási tetszőleges számossága blob storage-kimenet
   * a bemeneti partíció 8 blob storage és a blob storage-kimenet
   * 8 blob storage bemeneti partíciók és a 8 event hub kimeneti partíció

Az alábbi szakaszok tárgyalják a példákat megtekinteni, amelyek zavaróan párhuzamos.

### <a name="simple-query"></a>Egyszerű lekérdezés

* Bemenet: 8 partícióval rendelkező eseményközpontot
* Kimenet: 8 partícióval rendelkező eseményközpontot

Lekérdezés:

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

Ez a lekérdezés egy egyszerű szűrő. Ezért nem kell aggódnia a bemenet, amely küld az event hubs particionálási. Figyelje meg, hogy a lekérdezés tartalmaz **partíció által PartitionId**, így a #2. követelmény a korábban teljesít. A kimeneti kell konfigurálni az event hub kimeneti a feladat a partíciós kulcs értéke az **PartitionId**. Egy utolsó ellenőrzés, hogy ellenőrizze, hogy a bemeneti partíciók számának kimeneti partíciók száma egyenlő.

### <a name="query-with-a-grouping-key"></a>A csoportosítás kulccsal lekérdezése

* Bemenet: 8 partícióval rendelkező eseményközpontot
* Kimenet: Blob Storage

Lekérdezés:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Ez a lekérdezés csoportosítási kulccsal rendelkezik. Ezért az események csoportosítva el kell küldeni az Eseményközpont ugyanazon a partíción. Ebben a példában a Microsoft csoportosítási szempont TollBoothID, mivel azt kell róla, hogy TollBoothID használja partíciókulcsként, ha az események eseményközpontba érkeznek. Ezután az ASA, használhatjuk **partíció által PartitionId** a partícióséma öröklik, és a teljes ezerszer engedélyezéséhez. Mivel a kimeneti blob storage-ba, nem kell aggódnia a partíciókulcs-értékkel, #4. követelmény alapján konfigurálása.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Például olyan esetben *nem* zavaróan párhuzamos

Az előző szakaszban megmutattuk néhány zavaróan párhuzamos forgatókönyvet. Ebben a szakaszban bemutatjuk a forgatókönyvek, amelyek nem felelnek meg az összes követelmények zavaróan párhuzamos. 

### <a name="mismatched-partition-count"></a>Nem egyező partíciók száma
* Bemenet: 8 partícióval rendelkező eseményközpontot
* Kimenet: 32 partícióval rendelkező eseményközpontot

Ebben az esetben nem számít, mi az a lekérdezést. Ha a bemeneti partíciók száma nem egyezik meg a kimeneti partíciók száma, a topológia nem zavaróan párhuzamos. + azonban továbbra is kapunk bizonyos szintű vagy folyamatkezelést biztosítja.

### <a name="query-using-non-partitioned-output"></a>Lekérdezés kimenete nem particionált használatával
* Bemenet: 8 partícióval rendelkező eseményközpontot
* Kimenet: Power BI

A Power BI-kimenet jelenleg nem támogatja a particionálást. Ezért az ebben a forgatókönyvben nem zavaróan párhuzamos.

### <a name="multi-step-query-with-different-partition-by-values"></a>Többlépéses lekérdezés eltérő PARTITION BY értékek
* Bemenet: 8 partícióval rendelkező eseményközpontot
* Kimenet: 8 partícióval rendelkező eseményközpontot

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

Amint láthatja, a második lépésben használ **TollBoothId** particionálási kulcsként. Ez a lépés nem ugyanaz, mint az első lépés, és, ezért megköveteli tőlünk egy shuffle tennie. 

Az előző példák azt mutatják néhány Stream Analytics-feladatokat, amelyeket egy zavaróan párhuzamos topológia felel meg (vagy nem). Ha azok megfelelnek, a lehetséges maximális méretezési rendelkeznek. Frissíti a feladatok, amelyek nem egyeznek meg ezeket a profilokat, útmutatást skálázás egyikét a jövőben elérhető lesz. Most használja az alábbi szakaszok az általános útmutatást.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>A folyamatos átviteli egység egy feladat maximális kiszámítása
Egy Stream Analytics-feladat által használt streamelési egységek teljes száma attól függ, hogy a lekérdezésben, a feladat, illetve az egyes lépések a partíciók számának megadott lépéseket.

### <a name="steps-in-a-query"></a>A lekérdezés lépései
A lekérdezés egy vagy több lépést rendelkezhet. Az egyes lépések által meghatározott segédlekérdezésben a **WITH** kulcsszót. A lekérdezés, amely kívül esik a **WITH** kulcsszó (csak egy lekérdezés) is tranzakciónak számít egy lépést, mint például a **kiválasztása** utasítás a következő lekérdezést:

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
* A **kiválasztása** a lekérdezési utasítás egy particionált bemeneti forrás kell olvasni.
* A lekérdezés a lépés belül rendelkeznie kell a **PARTITION BY** kulcsszót.

A lekérdezés particionálva van, amikor a bemeneti események feldolgozása és az összesített külön partíciócsoportok, és kimenetek események az egyes csoportok jönnek létre. Ha azt szeretné, hogy egy kombinált összesítés, létre kell hoznia egy nem particionált második lépése az összesítést.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>A folyamatos átviteli feladat adattárházegységeinek maximális kiszámítása
Minden nem particionált lépést együtt méretezhető egy Stream Analytics-feladat legfeljebb hat a folyamatos átviteli egységek (su). Emellett adja hozzá mindegyik partíció 6 SUS-t egy particionált lépésben.
Megtekintheti az egyes **példák** az alábbi táblázatban.

| Lekérdezés                                               | Maximális SUS-t a projekthez |
| --------------------------------------------------- | ------------------- |
| <ul><li>A lekérdezés tartalmaz egy lépésben.</li><li>A lépés nincs particionálva.</li></ul> | 6 |
| <ul><li>A bemeneti streamből való 16 particionálva van.</li><li>A lekérdezés tartalmaz egy lépésben.</li><li>A lépés particionálva van.</li></ul> | 96 (6 * 16 partíciók) |
| <ul><li>A lekérdezés két lépést tartalmaz.</li><li>A lépések egyike sem particionálva van.</li></ul> | 6 |
| <ul><li>A bemeneti streamet 3 particionálva van.</li><li>A lekérdezés két lépést tartalmaz. A bemeneti lépés particionálva van, és a második lépésben nincs.</li><li>A <strong>kiválasztása</strong> utasítás a particionált bemeneti olvas.</li></ul> | a particionált lépéseket (18 + 6. lépéseket nem particionált 24 |

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

Ha a lekérdezés particionálva van, a bemeneti események feldolgozása, és összevonva jelenik meg különálló partíció csoportok. Kimeneti események is jönnek létre az egyes csoportok. Particionálás néhány várt következményekhez vezethet során a **GROUP BY** művelet nem a partíciókulcs a bemeneti streamből. Ha például a **TollBoothId** az előző lekérdezést a mező nem partíciókulcsa **Input1**. Ez több partíciót őrbódét 1 származó adatok lehetnek elosztva.

Minden egyes a **Input1** partíciók fogja feldolgozni, külön-külön Stream Analytics. Ennek eredményeképpen az ugyanazon őrbódét ugyanaz az Átfedésmentes ablak az autó számát, több rekordot hoz létre. A bemeneti partíciós kulcs nem módosítható, ha a probléma lehet meghatározni egy nem partíció lépés hozzáadásával az összesített értékek partíciókon, az alábbi példában látható módon:

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





## <a name="get-help"></a>Segítségkérés
További segítségre van szüksége, próbálja meg [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
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

