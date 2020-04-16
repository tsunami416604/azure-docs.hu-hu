---
title: Parquet-fájlok lekérdezése az IGÉNY szerinti SQL használatával (előzetes verzió)
description: Ebből a cikkből megtudhatja, hogyan lehet lekérdezni a parkettafájlokat az SQL igény szerinti (előzetes verzió) használatával.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431695"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Parquet-fájlok lekérdezése az SQL igény szerinti (előzetes verzió) használatával az Azure Synapse Analytics szolgáltatásban

Ebből a cikkből megtudhatja, hogyan írhat lekérdezést az SQL igény szerinti (előzetes verzió) használatával, amely a Parquet-fájlokat olvassa.

## <a name="prerequisites"></a>Előfeltételek

A cikk többi részének elolvasása előtt tekintse át a következő cikkeket:

- [Első beállítás](query-data-storage.md#first-time-setup)
- [Előfeltételek](query-data-storage.md#prerequisites)

## <a name="dataset"></a>Adatkészlet

A Parquet fájlokat ugyanúgy lekérdezheti, mint a CSV-fájlokat. Az egyetlen különbség az, hogy a FILEFORMAT paramétert PARQUET értékre kell állítani. Ebben a cikkben példák a Parkettafájlok olvasásának sajátosságait mutatják be.

> [!NOTE]
> Parkettafájlok olvasásakor nem kell oszlopokat megadnia az OPENROWSET WITH záradékban. Az SQL igény szerinti metaadatait használja a Parkettafájlban, és név szerint kötésoszlopokat.

A mintalekérdezésekhez a mappa *parkettát/taxit* fogja használni. Ez tartalmazza NYC Taxi - Yellow Taxi Trip Records adatok július 2016. 2018 júniusáig.

Az adatok at év és hónap szerint particionálják, és a mappastruktúra a következő:

- év=2016
  - hónap=6
  - ...
  - hónap=12
- év=2017
  - hónap=1
  - ...
  - hónap=12
- év=2018
  - hónap=1
  - ...
  - hónap=6

## <a name="query-set-of-parquet-files"></a>Parkettafájlok lekérdezéskészlete

A Parkettafájlok lekérdezésekénél csak az érdeklődésre számot tartó oszlopokat adhatja meg.

```sql
SELECT
        YEAR(pickup_datetime),
        passenger_count,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) WITH (
        pickup_datetime DATETIME2,
        passenger_count INT
    ) AS nyc
GROUP BY
    passenger_count,
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime),
    passenger_count;
```

## <a name="automatic-schema-inference"></a>Automatikus sémakövetkeztetés

A Parquet fájlok olvasásakor nem kell használnia az OPENROWSET WITH záradékot. Az oszlopneveket és adattípusokat a program automatikusan beolvassa a Parkettafájlokból.

Az alábbi minta a parquet-fájlok automatikus séma-következtetési képességeit mutatja be. A sorok számát adja vissza 2017 szeptemberében, séma megadása nélkül.

> [!NOTE]
> A Parkettafájlok olvasásakor nem kell oszlopokat megadnia az OPENROWSET WITH záradékban. Ebben az esetben az SQL igény szerinti lekérdezési szolgáltatás metaadatokat használ a Parketta fájlban, és név szerint kötésoszlopokat köt.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>Particionált adatok lekérdezése

Az ebben a mintában megadott adatkészlet külön almappákra van osztva (particionálva). A filepath függvénnyel meghatározott partíciókat célozhat meg. Ez a példa a 2017 első három hónapjában év, hónap és payment_type szerinti viteldíjösszegeket mutatja be.

> [!NOTE]
> Az SQL igény szerinti lekérdezés kompatibilis a Hive/Hadoop particionálási sémával.

```sql
SELECT
    nyc.filepath(1) AS [year],
    nyc.filepath(2) AS [month],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND pickup_datetime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type
ORDER BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type;
```

## <a name="type-mapping"></a>Típusleképezés

A parkettafájlok minden oszlop típusleírását tartalmazzák. Az alábbi táblázat bemutatja, hogyan vannak leképezve a paratta-típusok SQL natív típusokra.

| Parketta típusa | Parketta logikai típusa (jegyzet) | SQL-adattípus |
| --- | --- | --- |
| Logikai | | bit |
| BINÁRIS / BYTE_ARRAY | | varbinary között |
| Dupla | | lebegőpontos |
| Úszó | | real |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| Bináris |UTF8 |varchar \*(UTF8 rendezés) |
| Bináris |Karakterlánc |varchar \*(UTF8 rendezés) |
| Bináris |Enum|varchar \*(UTF8 rendezés) |
| Bináris |Uuid |uniqueidentifier |
| Bináris |Decimális |tizedes tört |
| Bináris |JSON |varchar(max) \*(UTF8 illesztés) |
| Bináris |BSON között |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |Decimális |tizedes tört |
| BYTE_ARRAY |Intervallum |varchar(max), szabványosított formátumba szerializálva |
| INT32 |INT(8; igaz) |smallint |
| INT32 |INT(16; igaz) |smallint |
| INT32 |INT(32; igaz) |int |
| INT32 |INT(8; hamis) |tinyint |
| INT32 |INT(16; hamis) |int |
| INT32 |INT(32; hamis) |bigint |
| INT32 |DATE |dátum |
| INT32 |Decimális |tizedes tört |
| INT32 |IDŐ (MILLIS)|time |
| INT64 |INT(64; igaz) |bigint |
| INT64 |INT(64, hamis ) |decimális(20,0) |
| INT64 |Decimális |tizedes tört |
| INT64 |IDŐ (MICROS / NANOS) |time |
|INT64 |IDŐBÉLYEG (MILLIS / MICROS / NANOS) |datetime2 |
|[Összetett típus](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |Lista |varchar(max), szerializálva a JSON-ba |
|[Összetett típus](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|Megjelenítése|varchar(max), szerializálva a JSON-ba |

## <a name="next-steps"></a>További lépések

A következő cikkből megtudhatja, hogyan lehet [lekérdezni az parketta beágyazott típusait.](query-parquet-nested-types.md)
