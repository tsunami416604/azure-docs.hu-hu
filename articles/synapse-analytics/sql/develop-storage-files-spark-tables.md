---
title: A Spark-táblázatok lekérdezése az SQL on-demand használatával (előzetes verzió)
description: A Spark-táblázatok lekérdezése az SQL on-demand (előzetes verzió) használatával – áttekintés
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 41e31a322a3d771557474fdf5c318960822bcfe1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424047"
---
# <a name="query-spark-tables-with-azure-synapse-analytics-using-sql-on-demand-preview"></a>Spark-táblázatok lekérdezése az Azure szinapszis Analytics használatával az SQL igény szerinti használata (előzetes verzió)

Az SQL on-demand (előzetes verzió) automatikusan képes szinkronizálni a-alapú Spark-készletekben található metaadatokat a szinapszis munkaterületen (előzetes verzió). A Spark-készletekben (előzetes verzió) létező összes adatbázishoz létre kell hozni egy SQL on demand-adatbázist. A Parquet vagy CSV-alapú összes Spark-táblához egy külső tábla jön létre az SQL igény szerinti adatbázisában. Így leállíthatja a Spark-készleteket, és továbbra is lekérdezheti a Spark-táblákat az SQL igény szerint.

Ha egy tábla a Sparkban van particionálva, a tárolóban lévő fájlok mappák szerint vannak rendezve. Az SQL on-demand a partíciós metaadatokat használja, és csak a lekérdezéshez kapcsolódó mappákat és fájlokat fogja használni.

A rendszer automatikusan beállítja a metaadatok szinkronizálását az Azure szinapszis munkaterületen kiépített összes Spark-készlethez. Azonnal megkezdheti a Spark-táblák lekérdezését.

Minden Spark-tábla egy olyan dbo-sémában található külső táblával van ábrázolva, amely egy SQL igény szerinti adatbázisnak felel meg. A Spark Table-lekérdezések esetében futtasson egy külső [spark_table] célt szolgáló lekérdezést. Az alábbi példa futtatása előtt győződjön meg arról, hogy megfelelő [hozzáférése van a Storage-fiókhoz](develop-storage-files-storage-access-control.md) , ahol a fájlok találhatók.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="spark-data-types-to-sql-data-types-mapping"></a>Spark-adattípusok az SQL-adattípusok leképezéséhez

| Spark adattípus | SQL-adattípus               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| ShortType       | smallint                    |
| IntegerType     | int                         |
| LongType        | bigint                      |
| FloatType       | valós szám                        |
| DoubleType      | lebegőpontos                       |
| DecimalType     | tizedes tört                     |
| TimestampType   | datetime2                   |
| DateType        | dátum                        |
| StringType      | varchar (max) *               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar (max) * (JSON-ba) * * |
| MapType         | varchar (max) * (JSON-ba) * * |
| StructType      | varchar (max) * (JSON-ba) * * |

\*A használt rendezés Latin1_General_100_BIN2_UTF8.

* * A ArrayType, a MapType és a StructType JSON-ként jelenik meg.



## <a name="next-steps"></a>További lépések

Folytassa a [storage Access Control](develop-storage-files-storage-access-control.md) cikkével, amely további információt biztosít a Storage hozzáférés-vezérléséről.
