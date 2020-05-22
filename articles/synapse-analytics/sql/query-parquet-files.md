---
title: A Parquet-fájlok lekérdezése az SQL on-demand használatával (előzetes verzió)
description: Ebből a cikkből megtudhatja, hogyan kérdezheti le a Parquet-fájlokat az SQL on-demand (előzetes verzió) használatával.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: e9731b869b20c7d8cfc3b1e234711c818a2b7422
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744253"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>A Parquet-fájlok lekérdezése az SQL on-demand (előzetes verzió) használatával az Azure szinapszis Analyticsben

Ebből a cikkből megtudhatja, hogyan írhat egy lekérdezést az SQL on-demand (előzetes verzió) használatával, amely beolvassa a parketta-fájlokat.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként létre kell **hoznia egy adatbázist** egy olyan adatforrással, amely a [New York Yellow taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) Storage-fiókra hivatkozik. Ezután inicializálja az objektumokat a [telepítési parancsfájl](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) végrehajtásával az adatbázison. Ez a telepítési parancsfájl létrehozza az adatforrásokat, az adatbázis-hatókörrel rendelkező hitelesítő adatokat, valamint az ezekben a mintákban használt külső fájlformátumokat.

## <a name="dataset"></a>Adatkészlet

Ebben a példában a [New York Yellow taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) -adatkészletet használjuk. A parketta-fájlok lekérdezése ugyanúgy végezhető el, mint a [CSV-fájlok olvasása](query-parquet-files.md). Az egyetlen különbség, hogy a `FILEFORMAT` paramétert a értékre kell beállítani `PARQUET` . A jelen cikkben szereplő példák a Parquet-fájlok olvasásának sajátosságait mutatják be.

## <a name="query-set-of-parquet-files"></a>A Parquet-fájlok lekérdezési készlete

A Parquet-fájlok lekérdezése során csak a fontos oszlopokat adhatja meg.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>Automatikus séma-következtetés

A Parquet-fájlok olvasásakor nem szükséges a OPENROWSET WITH záradék használata. Az oszlopnevek és az adattípusok automatikusan beolvashatók a Parquet-fájlokból.

Az alábbi minta a Parquet-fájlok automatikus sémájának következtetéseit mutatja be. A sorok számát a 2017 szeptemberében adja vissza a séma meghatározása nélkül.

> [!NOTE]
> A Parquet-fájlok olvasásakor nem szükséges oszlopokat megadni a OPENROWSET WITH záradékban. Ebben az esetben az SQL igény szerinti lekérdezési szolgáltatása metaadatokat használ a Parquet fájlban, és az oszlopokat név alapján köti össze.

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>Particionált adatlekérdezés

Az ebben a mintában megadott adathalmaz külön almappákba van osztva (particionálva). A filepath függvény használatával konkrét partíciókat is megcélozhat. Ez a példa a viteldíjak összegét mutatja év, hónap és payment_type szerint az 2017-as első három hónapban.

> [!NOTE]
> Az igény szerinti SQL-lekérdezés a kaptár/Hadoop particionálási sémával kompatibilis.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="type-mapping"></a>Típus leképezése

A Parquet-fájlok minden oszlop típusának leírását tartalmazzák. A következő táblázat leírja, hogyan vannak leképezve a parketta típusai az SQL natív típusaira.

| Parketta típusa | Parketta logikai típusa (jegyzet) | SQL-adattípus |
| --- | --- | --- |
| LOGIKAI | | bit |
| BINÁRIS/BYTE_ARRAY | | varbinary |
| DUPLÁN | | lebegőpontos |
| FLOAT | | valós szám |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINÁRIS |UTF8 |varchar \* (UTF8-rendezés) |
| BINÁRIS |KARAKTERLÁNC |varchar \* (UTF8-rendezés) |
| BINÁRIS |ENUM|varchar \* (UTF8-rendezés) |
| BINÁRIS |UUID |uniqueidentifier |
| BINÁRIS |DECIMÁLIS |tizedes tört |
| BINÁRIS |JSON |varchar (max) \* (UTF8-rendezés) |
| BINÁRIS |BSON |varbinary (max.) |
| FIXED_LEN_BYTE_ARRAY |DECIMÁLIS |tizedes tört |
| BYTE_ARRAY |IDŐKÖZ |varchar (max), szabványosított formátumba szerializálva |
| INT32 |INT (8, igaz) |smallint |
| INT32 |INT (16, igaz) |smallint |
| INT32 |INT (32, true) |int |
| INT32 |INT (8, hamis) |tinyint |
| INT32 |INT (16, hamis) |int |
| INT32 |INT (32, hamis) |bigint |
| INT32 |DATE |date |
| INT32 |DECIMÁLIS |tizedes tört |
| INT32 |IDŐ (MILLIS)|time |
| INT64 |INT (64, true) |bigint |
| INT64 |INT (64, hamis) |decimális (20, 0) |
| INT64 |DECIMÁLIS |tizedes tört |
| INT64 |IDŐ (MICROS/NANOS) |time |
|INT64 |IDŐBÉLYEG (MILLIS/MICROES/NANOS) |datetime2 |
|[Összetett típus](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |LISTÁJÁT |varchar (max), JSON-ba szerializálva |
|[Összetett típus](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|Térkép|varchar (max), JSON-ba szerializálva |

## <a name="next-steps"></a>További lépések

A következő cikkből megtudhatja, hogyan lehet [lekérdezni a parketta beágyazott típusait](query-parquet-nested-types.md).
