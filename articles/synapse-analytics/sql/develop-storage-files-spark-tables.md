---
title: Spark-táblák lekérdezése az IGÉNY szerinti SQL használatával (előzetes verzió)
description: A Spark-táblák igény szerinti SQL-alapú lekérdezésének áttekintése (előzetes verzió)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 41e31a322a3d771557474fdf5c318960822bcfe1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424047"
---
# <a name="query-spark-tables-with-azure-synapse-analytics-using-sql-on-demand-preview"></a>Spark-táblák lekérdezése az Azure Synapse Analytics használatával az SQL igény szerinti használatával (előzetes verzió)

Az SQL igény szerinti (előzetes verzió) automatikusan szinkronizálja a metaadatokat a Spark-készletekből a Synapse-munkaterületen belül (előzetes verzió). A Spark-készletekben (előzetes verzió) létező minden egyes adatbázishoz létrejön egy IGÉNY szerinti SQL-adatbázis. Minden Spark-tábla parokt vagy CSV-alapú, egy külső tábla jön létre az SQL igény szerinti adatbázisban. Így leállíthatja a Spark-készletek, és továbbra is lekérdezheti a Spark-táblák sql igény szerint.

Ha egy tábla particionált a Sparkban, a tárolóban lévő fájlok mappák szerint vannak rendezve. Az SQL igény szerinti használja a partíció metaadatait, és csak a megfelelő mappákat és fájlokat célozza meg a lekérdezéshez.

Metaadat-szinkronizálás automatikusan konfigurálva van az Azure Synapse munkaterületen kiépített minden spark-készlethez. A Spark-táblák lekérdezése azonnal megkezdheti.

Minden Spark-tábla egy külső táblával jelenik meg egy dbo-sémában, amely megfelel egy SQL igény szerinti adatbázisnak. A Spark-tábla lekérdezések, futtasson egy lekérdezést, amely egy külső [spark_table]. Az alábbi példa futtatása előtt győződjön meg arról, hogy megfelelő [hozzáféréssel rendelkezik a tárfiókhoz,](develop-storage-files-storage-access-control.md) ahol a fájlok találhatók.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="spark-data-types-to-sql-data-types-mapping"></a>Szikraadattípusok SQL-adattípusok leképezéséhez

| Szikra-adattípus | SQL-adattípus               |
| --------------- | --------------------------- |
| ByteType (Bájttípus)        | smallint                    |
| ShortType típusú       | smallint                    |
| IntegerType     | int                         |
| LongType típus        | bigint                      |
| FloatType (Lebegőtípus)       | real                        |
| DoubleType típus      | lebegőpontos                       |
| Decimális típus     | tizedes tört                     |
| Időbélyegtípusa   | datetime2                   |
| Dátumtípusa        | dátum                        |
| Karakterlánctípusa      | varchar(max)*               |
| Bináris típus      | varbinary között                   |
| Logikai típus     | bit                         |
| Tömbtípus       | varchar(max)* (A JSON-ba)** |
| MapType típus         | varchar(max)* (A JSON-ba)** |
| StructType típus      | varchar(max)* (A JSON-ba)** |

\*Az alkalmazott rendezés Latin1_General_100_BIN2_UTF8.

** Az ArrayType, a MapType és a StructType json-ként jelennek meg.



## <a name="next-steps"></a>További lépések

A [storage access-control](develop-storage-files-storage-access-control.md) cikkhez további információa a tárolási hozzáférés-vezérlésről.
