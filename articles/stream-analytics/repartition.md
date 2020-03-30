---
title: Az Átparticionálás használata az Azure Stream Analytics-feladatok optimalizálásához
description: Ez a cikk ismerteti, hogyan kell használni az újraparticionálás optimalizálásához Az Azure Stream Analytics-feladatok, amelyek nem párhuzamosítható.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c70cfb6c1626908a2ba4e707a890f6dc7481c51a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732382"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Az Újraparticionálás használata az Azure Stream Analytics használatával a feldolgozás optimalizálásához

Ez a cikk bemutatja, hogyan használhatja az újraparticionálást az Azure Stream Analytics-lekérdezés méretezéséhez olyan forgatókönyvekhez, amelyek nem teljes [mértékben párhuzamosíthatók.](stream-analytics-scale-jobs.md)

Előfordulhat, hogy nem tudja használni a [párhuzamosítást,](stream-analytics-parallelization.md) ha:

* A bemeneti adatfolyam partíciókulcsát nem vezérelheti.
* A forrás "spray" bemenet több partíción, amely et később egyesíteni kell.

Újraparticionálás, vagy átrendeződés szükséges, ha az adatok feldolgozása egy adatfolyamon, amely nem egy természetes bemeneti séma szerint, például **PartitionId** az Event Hubs. Újraparticionáláskor minden szegmens ek től függetlenül feldolgozható, amely lehetővé teszi, hogy lineárisan skálázhatja ki a streamelési folyamat.

## <a name="how-to-repartition"></a>Újraparticionálás

A particionáláshoz használja az **INTO** kulcsszót **a lekérdezésben egy PARTITION BY** utasítás után. A következő példa a **DeviceID-adatokat** 10 partíciószámba particionálja. Az **Eszközazonosító** kivonatolása annak meghatározására szolgál, hogy melyik partíció melyik részáramat fogadja el. Az adatok egymástól függetlenül kiürítve minden particionált stream, feltételezve, hogy a kimenet támogatja a particionált írási műveleteket, és 10 partícióval rendelkezik.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

A következő példalekérdezés két újraparticionált adatfolyamot egyesít. Két újraparticionált adatfolyam okának összekötésekének esetén az adatfolyamoknak ugyanazzal a partíciós kulccsal és számmal kell rendelkezniük. Az eredmény egy adatfolyam, amely ugyanazt a partíciósémát rendelkezik.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

A kimeneti sémának meg kell egyeznie az adatfolyam-séma kulcsának, és meg kell számolnia, hogy minden részfolyam egymástól függetlenül kiüríthető legyen. Az adatfolyam is egyesíthető, és újraparticionálva egy másik séma kiürítés előtt, de kerülje el ezt a módszert, mert növeli a feldolgozás általános késése, és növeli az erőforrás-kihasználtságot.

## <a name="streaming-units-for-repartitions"></a>Újraparticionálások streamelési egységei

Kísérletezzen és figyelje meg a feladat erőforrás-használatát a szükséges partíciók pontos számának meghatározásához. A [streamelési egységek (SU)](stream-analytics-streaming-unit-consumption.md) számát az egyes partíciókhoz szükséges fizikai erőforrásoknak megfelelően kell beállítani. Általában hat SUs van szükség minden partícióhoz. Ha nincs elegendő erőforrás a feladathoz rendelve, a rendszer csak akkor alkalmazza az újraparticionálást, ha az a feladat számára előnyös.

## <a name="repartitions-for-sql-output"></a>Újraparticionálások SQL kimenethez

Ha a feladat SQL-adatbázist használ a kimenethez, explicit újraparticionálás használatával feleljen meg az optimális partíciószámnak az átviteli teljesítmény maximalizálása érdekében. Mivel az SQL működik a legjobban nyolc író, átparticionálása a folyamat nyolc öblítés előtt, vagy valahol feljebb, előnyös lehet a munka teljesítményét. 

Ha több mint 8 bemeneti partíciók, a bemeneti particionálási séma öröklése nem megfelelő választás. Fontolja meg [az INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) használatát a lekérdezésben a kimeneti írók számának kifejezett megadásához. 

A következő példa beolvassa a bemenetet, függetlenül attól, hogy természetes módon particionált, és újraparticionálja az adatfolyam tízszeresét a DeviceID dimenziónak megfelelően, és kiüríti az adatokat kimenetre. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

További információ: [Azure Stream Analytics-kimenet az Azure SQL Database számára.](stream-analytics-sql-output-perf.md)


## <a name="next-steps"></a>További lépések

* [Ismerkedés az Azure Stream Analytics szolgáltatással](stream-analytics-introduction.md)
* [A lekérdezések párhuzamosításának kihasználása az Azure Stream Analytics szolgáltatásban](stream-analytics-parallelization.md)
