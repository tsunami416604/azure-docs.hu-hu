---
title: Újraparticionálás használata Azure Stream Analytics feladatok optimalizálása érdekében
description: Ez a cikk azt ismerteti, hogyan lehet az újraparticionálással optimalizálni Azure Stream Analytics feladatokat, amelyeket nem lehet párhuzamosan használni.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c70cfb6c1626908a2ba4e707a890f6dc7481c51a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75732382"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Az újraparticionálással optimalizálja a feldolgozást Azure Stream Analytics

Ebből a cikkből megtudhatja, hogy az újraparticionálással hogyan méretezheti a Azure Stream Analytics-lekérdezést olyan forgatókönyvek esetében, amelyek nem lehetnek teljesen [párhuzamosak](stream-analytics-scale-jobs.md).

Előfordulhat, hogy nem tudja használni a [párhuzamos](stream-analytics-parallelization.md) , ha:

* A bemeneti adatfolyamhoz tartozó partíciós kulcs nem szabályozható.
* A forrás "spray" bemenete több partíción keresztül történik, amelyeket később egyesíteni kell.

Az újraparticionálás vagy az újrakeverés szükséges, ha olyan adatfolyamon dolgoz fel adatokat, amely nem egy természetes bemeneti séma alapján van felosztva, például Event Hubs **PartitionID** . Az újraparticionáláskor az egyes szegmensek egymástól függetlenül dolgozhatók fel, ami lehetővé teszi a folyamatos átviteli folyamat lineáris felskálázását.

## <a name="how-to-repartition"></a>Újraparticionálás

Az újraparticionáláshoz használja a kulcsszót egy **Partition by** **utasítás után a** lekérdezésben. Az alábbi példa az adatmennyiséget az **DeviceID** értékre particionálja egy 10. számú partíción. A **DeviceID** kivonatolása annak meghatározására szolgál, hogy melyik partíciónak kell elfogadnia az alstreamet. Az adatokat az egyes particionált adatfolyamok egymástól függetlenül ürítik, feltéve, hogy a kimenet támogatja a particionált írásokat, és 10 partíciót tartalmaz.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

A következő példa két újraparticionált adatstreamet egyesít. Két újraparticionált adatstream csatlakoztatásakor a streameknek ugyanazzal a partíciós kulccsal és számmal kell rendelkezniük. Az eredmény egy olyan adatfolyam, amely ugyanazzal a partíciós sémával rendelkezik.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

A kimeneti sémának meg kell egyeznie az adatfolyam-séma kulcsával és számával, hogy az egyes alstreamek egymástól függetlenül is kiüríthető legyen. Az adatfolyamot egy másik séma is egyesítheti és újraparticionálhatja a kiürítés előtt, de el kell kerülnie ezt a metódust, mivel az felveszi a feldolgozás általános késését, és növeli az erőforrás-kihasználtságot.

## <a name="streaming-units-for-repartitions"></a>Újraparticionálások folyamatos átviteli egységei

Kísérletezzen és figyelje meg a feladatok erőforrás-használatát a szükséges partíciók pontos számának megállapításához. A [folyamatos átviteli egységek (su)](stream-analytics-streaming-unit-consumption.md) számát az egyes partíciók számára szükséges fizikai erőforrások alapján kell beállítani. Általánosságban elmondható, hogy az egyes partíciók esetében hat SUs szükséges. Ha nincs elegendő erőforrás rendelve a feladathoz, a rendszer csak akkor alkalmazza az újraparticionálást, ha a feladat előnyeit biztosítja.

## <a name="repartitions-for-sql-output"></a>SQL-kimenet újraparticionálása

Ha a feladata SQL Database-t használ a kimenethez, az átviteli sebesség maximalizálása érdekében használjon explicit újraparticionálást az optimális partíciók számának megfelelően. Mivel az SQL nyolc író használatával működik a legjobban, a folyamat átparticionálása a kiürítés előtt nyolcra, vagy valahol további upstream, a feladatok teljesítményére is felhasználható. 

Ha több mint 8 bemeneti partíció van, akkor előfordulhat, hogy a bemeneti particionálási séma öröklése nem megfelelő választás. Vegye fontolóra [a-ben való](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) használatát a lekérdezésben, hogy explicit módon adja meg a kimeneti írók számát. 

A következő példa beolvassa a bevitelt, függetlenül attól, hogy a rendszer természetesen particionálja, és újraparticionálja a stream tízszeresére a DeviceID dimenzió alapján, és kiüríti az adatokat a kimenetre. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

További információ: [Azure stream Analytics kimenet Azure SQL Database](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>További lépések

* [Ismerkedés a Azure Stream Analytics](stream-analytics-introduction.md)
* [A lekérdezési párhuzamos kihasználása Azure Stream Analytics](stream-analytics-parallelization.md)
