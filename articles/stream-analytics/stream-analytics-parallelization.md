---
title: Azure Stream Analytics lekérdezési párhuzamos folyamatkezelést biztosítja, és a skála használata
description: Ez a cikk ismerteti a Stream Analytics-feladatok méretezése bemeneti partíciók konfigurálása, a lekérdezés definíciója hangolása és adatfolyam-egységek feladat beállítása.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: 949806379891dbf5a7c145a14cae532104f51497
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Használja ki az Azure Stream Analytics lekérdezési párhuzamos folyamatkezelést biztosítja
Ez a cikk bemutatja, hogyan Azure Stream Analytics párhuzamos folyamatkezelést biztosítja előnyeit. Megismerheti a Stream Analytics-feladatok méretezése bemeneti partíciók beállításával, és az elemzés Lekérdezésdefiníció hangolása.
Előfeltételként, amelyeknél felmerülhet a Streaming Unit ismertetett fogalmát ismernie kell a [ismertetése, és módosítsa a folyamatos átviteli egységet](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Mik azok a Stream Analytics-feladat azon részei?
A Stream Analytics-feladat definíciójának bemenet, a lekérdezés és a kimeneti tartalmazza. Bemenetek, ahol a feladat beolvassa az adatfolyamot a. A lekérdezés segítségével a bemeneti adatfolyam átalakítása, és a kimeneti, ahol a feladat küldése a feladat eredményét.  

Egy feladat az adatok adatfolyamként történő legalább egy bemeneti forrást igényel. Az adatfolyam bemeneti adatforrás is tárolhatók, egy Azure event hubs eseményközpontot, vagy az Azure blob Storage tárolóban. További információkért lásd: [Azure Stream Analytics bemutatása](stream-analytics-introduction.md) és [Azure Stream Analytics használatának első](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-sources-and-sinks"></a>Források és mosdók a partíciók
A Stream Analytics-feladat skálázás kihasználja a partíciók a bemeneti vagy kimeneti. A particionáló lehetővé teszi, hogy részekre adatok partíciós kulcs alapján. A folyamat, amely az adatokat (például egy Streaming Analytics-feladat) akkor is használnak, és különböző partíciók írási párhuzamosan, ami növeli az adatátviteli sebességet. 

### <a name="inputs"></a>Bemenetek
Minden Azure Stream Analytics-bevitel kihasználhatják a particionálás:
-   Az EventHub (kell megadni, a partíciós kulcs explicit módon)
-   Az IoT-központ (kell megadni, a partíciós kulcs explicit módon)
-   Blob Storage

### <a name="outputs"></a>Kimenetek

A Stream Analytics használata, kihasználhatja a kimenetek a particionálás:
-   Azure Data Lake Storage
-   Azure Functions
-   Azure-tábla
-   Blob Storage
-   CosmosDB (kell megadni, a partíciós kulcs explicit módon)
-   Az EventHub (kell megadni, a partíciós kulcs explicit módon)
-   Az IoT-központ (kell megadni, a partíciós kulcs explicit módon)
-   Service Bus

Power BI, az SQL és az SQL-adatraktár kimenetek nem támogatják a particionálást. Azonban Ön is továbbra is partícióazonosító a bemeneti leírtak [Ez a szakasz](#multi-step-query-with-different-partition-by-values) 

Partíciók kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Event Hubs szolgáltatások – áttekintés](../event-hubs/event-hubs-features.md#partitions)
* [Az adatok particionálása](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning#partitioning-azure-blob-storage)


## <a name="embarrassingly-parallel-jobs"></a>Embarrassingly párhuzamos feladat
Egy *embarrassingly párhuzamos* az Azure Stream Analytics tudunk legjobban méretezhető forgatókönyv. A lekérdezés egy példánya bemeneti egy partíciót csatlakozik a kimenet egy partíciót. A párhuzamos végrehajtás követelményei a következők:

1. Ha a lekérdezés logika attól függ, hogy a lekérdezés példányt által feldolgozott ugyanazzal a kulccsal, meg kell győződnie arról, hogy az események nyissa meg a bemeneti partícióra. Az event hubs számára ez azt jelenti, hogy az eseményadatok kell rendelkeznie a **PartitionKey** érték beállítása. Másik lehetőségként particionált feladók is használhatja. A blob-tároló Ez azt jelenti, hogy az események az azonos partíció mappába kerülnek. Ha a lekérdezés logika nem követeli meg a lekérdezés példányt általi feldolgozásának ugyanazzal a kulccsal, figyelmen kívül hagyhatja ezt a követelményt. A logikai erre példa lehet egy egyszerű válassza-projekt-szűrő lekérdezés.  

2. Ha az adatokat a bemeneti oldalon elrendezését, meg kell győződnie arról, hogy a lekérdezés particionálva van. Ehhez szükséges, hogy használja **PARTITION BY** az összes lépését. Több lépést használhat, de mindegyikük ugyanazzal a kulccsal kell particionálható. Jelenleg a particionálási key értékre kell állítani **PartitionId** ahhoz, hogy a teljes párhuzamos feladat.  

3. A kimenet a legtöbb kihasználhatják a particionálás, azonban egy kimeneti típus használata, amely nem támogatja a particionálás a feladat nem fogja teljesen párhuzamos. Tekintse meg a [szakasz kimeneti](#outputs) további részleteket.

4. A bemeneti partíciók számának egyenlőnek kell lennie a kimeneti partíciók számát. BLOB storage-kimenet jelenleg nem támogatja a partíciókat. Azonban ez nem probléma, mert a particionálási sémát a felsőbb rétegbeli lekérdezés örökli. Az alábbiakban néhány lehetséges, amelyek lehetővé teszik a teljes párhuzamos feladat partíció érték:  

   * 8 event hub bemeneti partíciók és 8 eseményközpont kimeneti partíciók
   * 8 event hub bemeneti partíciókat és a blob storage kimeneti  
   * bemeneti partíciók 8 blob storage és a blob storage kimeneti  
   * 8 blob-tároló bemeneti partíciók és 8 event hub kimeneti partíciók  

Az alábbi szakaszok néhány példát arra, hogy embarrassingly párhuzamos tárgyalják.

### <a name="simple-query"></a>Egyszerű lekérdezés

* Bemenet: Eseményközpont 8 partíciókkal rendelkező
* Kimeneti: Eseményközpont 8 partíciókkal rendelkező

Lekérdezés:

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

Ez a lekérdezés egy egyszerű szűrési. Emiatt nem kell foglalkoznia a bemeneti az event hubs elküldött particionálás. Figyelje meg, hogy a lekérdezés tartalmaz **partíció által PartitionId**, így a korábbi #2 követelmény teljesít. A kimeneti igazolnia kell konfigurálnia az event hub kimeneti a partíciós kulcs értékre van beállítva a feladat **PartitionId**. Egy legutóbbi ellenőrzés, győződjön meg arról, hogy a bemeneti partíciók számának megegyezik a kimeneti partíciók számát.

### <a name="query-with-a-grouping-key"></a>Csoportosítás kulccsal lekérdezése

* Bemenet: Eseményközpont 8 partíciókkal rendelkező
* A kimenetre: A Blob storage

Lekérdezés:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Ez a lekérdezés csoportosítási kulccsal rendelkezik. Ezért a csoportosított események Eseményközpont ugyanazon partíciójára kell küldeni. Mivel ebben a példában a Microsoft csoportosítási szempont TollBoothID, igazolnia kell, hogy az, hogy TollBoothID partíciókulcsnak az események küldhetők az Event Hubs. Majd ASA, a használhatjuk **partíció által PartitionId** örökli a partícióséma, és engedélyezze a teljes párhuzamos folyamatkezelést biztosítja. Mivel a kimeneti blob-tároló, nem kell aggódnia a partíciós kulcs értékre, követelmény #4 konfigurálása.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Példa olyan forgatókönyveket mutatnak, amelyek *nem* embarrassingly párhuzamos

Az előző szakaszban néhány embarrassingly párhuzamos forgatókönyv bemutatta azt. Ebben a szakaszban arról lesz szó, amelyek nem felelnek meg az összes követelmények embarrassingly párhuzamos forgatókönyvek. 

### <a name="mismatched-partition-count"></a>Nem egyező partíciók száma
* Bemenet: Eseményközpont 8 partíciókkal rendelkező
* Kimenet: Eseményközpont 32 partíciókkal rendelkező

Ebben az esetben nem számít, mi az a lekérdezés. Ha a bemeneti partíciók száma nem egyezik meg a kimeneti partíciók száma, a topológia nem embarrassingly párhuzamos. + azonban azt elérheti bizonyos szint vagy a párhuzamos folyamatkezelést biztosítja.

### <a name="query-using-non-partitioned-output"></a>Lekérdezés nem particionált kimeneti használatával
* Bemenet: Eseményközpont 8 partíciókkal rendelkező
* Kimenet: Power BI

Power bi-kimenet jelenleg nem támogatja a particionálást. Emiatt az ebben a forgatókönyvben nincs embarrassingly párhuzamos.

### <a name="multi-step-query-with-different-partition-by-values"></a>Több lekérdezés eltérő értékű PARTITION BY
* Bemenet: Eseményközpont 8 partíciókkal rendelkező
* Kimeneti: Eseményközpont 8 partíciókkal rendelkező

Lekérdezés:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Ahogy látja, a második lépésben használt **TollBoothId** particionálási kulcsként. Ez a lépés nem ugyanaz, mint az első lépés, és ezért igényel, hogy egy véletlen tegye. 

Az előző példák azt szemléltetik, néhány Stream Analytics-feladatok, amelyek egy embarrassingly párhuzamos topológia felel meg (vagy nem). Ha felelnek meg, lehetséges, hogy a maximális skálája rendelkeznek. Feladatok, amelyek nem felelnek meg ezeket a profilokat, útmutatást skálázás egyik le lesz elérhető a jövőben frissíti. Most használja az alábbi szakaszok az általános útmutatást.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Az adatfolyam-egységek a feladatok maximálisan kiszámítása
A Stream Analytics-feladat által használt adatfolyam-továbbítási egységek száma attól függ, hogy a lekérdezésben definiált a feladatot, és minden egyes partíciók száma lépések számát.

### <a name="steps-in-a-query"></a>A lekérdezésben lépései
A lekérdezés egy vagy több lépéseket rendelkezhet. Az egyes lépések által meghatározott segédlekérdezésben a **WITH** kulcsszó. A lekérdezés, amely kívül esik a **WITH** kulcsszó (csak egy lekérdezést) is számít egy lépést, mint például a **válasszon** utasítás a következő lekérdezésben:

Lekérdezés:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

Ez a lekérdezés két lépést tartalmaz.

> [!NOTE]
> Ez a lekérdezés a cikk későbbi részében részletesebben tárgyalja.
>  

### <a name="partition-a-step"></a>A lépés particionálása
Particionálás egy lépés a következő feltételeknek kell:

* A bemeneti forrás kell particionálni. 
* A **válasszon** utasítás a lekérdezés egy particionált bemeneti forrásból kell olvasni.
* A lekérdezés a lépés belül kell rendelkeznie a **PARTITION BY** kulcsszó.

A lekérdezés particionálva van, amikor a bemeneti események feldolgozott és az összesített külön partíciócsoportok, és kimenetek az események az egyes csoportok hozhatók létre. Ha azt szeretné, hogy egy kombinált összesítést, létre kell hoznia egy második nem particionált lépés összesítendő.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Az adatfolyam-egységek feladat maximális kiszámítása
Minden nem particionált lépést együtt is méretezhető Stream Analytics-feladat legfeljebb hat folyamatos átviteli egységeket (SUS-t). Továbbá a 6 SUS-t minden partíció esetében is hozzáadhat egy particionált lépésben.
Megtekintheti az egyes **példák** az alábbi táblázatban.

| Lekérdezés                                               | A feladat maximális SUS-t |
| --------------------------------------------------- | ------------------- |
| <ul><li>A lekérdezés tartalmaz egy lépésben.</li><li>A lépés nincs particionálva.</li></ul> | 6 |
| <ul><li>A bemeneti adatfolyam 16 szerint van particionálva.</li><li>A lekérdezés tartalmaz egy lépésben.</li><li>A lépés particionálva van.</li></ul> | 96 (6 * 16 partíciók) |
| <ul><li>A lekérdezés két lépést tartalmaz.</li><li>A lépések egyike sem particionálva van.</li></ul> | 6 |
| <ul><li>A bemeneti adatfolyam 3 particionálva van.</li><li>A lekérdezés két lépést tartalmaz. A bemeneti lépés particionálva van, és a második lépésben nincs.</li><li>A <strong>válasszon</strong> a particionált bemeneti olvassa be az utasítást.</li></ul> | 24 (18 particionált lépéseket + 6. lépéseket nem particionált |

### <a name="examples-of-scaling"></a>Példák a méretezés

A következő lekérdezés, amely rendelkezik a három tollbooths téren állomás áthaladás három perces ablak autók száma számítja ki. Ez a lekérdezés legfeljebb hat SUS-t is méretezhető.

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Több SUS-t használni a lekérdezéshez, mind a bemeneti adatfolyamban, és a lekérdezés kell particionálni. Mivel az adatok adatfolyam partíció 3 van állítva, a következő módosított lekérdezés legfeljebb 18 SUS-t is méretezhető:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

A lekérdezés particionálva van, amikor a bemeneti események feldolgozása, és külön partíciócsoportok összesíteni. A kimeneti eseményekben is vonatkozóan a csoportok jönnek létre. Particionálás néhány várt következményekhez vezethet amikor a **GROUP BY** mező nincs a partíciókulcs a bemeneti adatfolyamban. Például a **TollBoothId** partíciós kulcsa nem található az előző lekérdezés mező **Input1**. Az eredménye, hogy őrbódét #1 adatait több partíciót a átterjedhet.

Egyes a **Input1** partíciók dolgoz fel külön Stream Analytics. Ennek eredményeképpen az ugyanazon Átfedésmentes ablak azonos őrbódét a car száma több rekordnyi jön létre. A bemeneti partíciós kulcs nem módosítható, ha a probléma lehet meghatározni a partíción kívüli lépés felvételével az összesített értékek közötti partíciók, az alábbi példában látható módon:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Ez a lekérdezés 24 SUS-t is méretezhető.

> [!NOTE]
> Ha a két adatfolyam csatlakozik, győződjön meg arról, hogy az adatfolyamokat a partíciókulcs az illesztések létrehozásához használt oszlop van-e particionálni. Ellenőrizze azt is, hogy rendelkezik-e a partíciók azonos számú a mindkét adatfolyamokat.
> 
> 





## <a name="get-help"></a>Segítségkérés
Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

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

[microsoft.support]: http://support.microsoft.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

