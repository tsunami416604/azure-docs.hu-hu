---
title: A Parquet-fájlok lekérdezése az SQL on-demand használatával (előzetes verzió)
description: Ebből a cikkből megtudhatja, hogyan kérdezheti le a Parquet-fájlokat az SQL on-demand (előzetes verzió) használatával.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431695"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>A Parquet-fájlok lekérdezése az SQL on-demand (előzetes verzió) használatával az Azure szinapszis Analyticsben

Ebből a cikkből megtudhatja, hogyan írhat egy lekérdezést az SQL on-demand (előzetes verzió) használatával, amely beolvassa a parketta-fájlokat.

## <a name="prerequisites"></a>Előfeltételek

A cikk további részének beolvasása előtt tekintse át a következő cikkeket:

- [Első beállítás](query-data-storage.md#first-time-setup)
- [Előfeltételek](query-data-storage.md#prerequisites)

## <a name="dataset"></a>Adatkészlet

A parketta-fájlok lekérdezése ugyanúgy végezhető el, mint a CSV-fájlok olvasása. Az egyetlen különbség, hogy a FILEFORMAT paramétert a parketta értékre kell beállítani. A jelen cikkben szereplő példák a Parquet-fájlok olvasásának sajátosságait mutatják be.

> [!NOTE]
> A Parquet-fájlok olvasásakor nem szükséges oszlopokat megadni a OPENROWSET WITH záradékban. Az SQL on-demand a parketta-fájlban található metaadatokat fogja használni, és az oszlopokat név alapján köti össze.

A minta lekérdezésekhez a *Parquet/taxi* mappát fogja használni. A New York-i, a sárga taxis utazás a 2016. júliustól nyilvántartott adatokat tartalmaz. – Június 2018.

Az adat az év és a hónap szerint van particionálva, és a mappa szerkezete a következő:

- év = 2016
  - hónap = 6
  - ...
  - hónap = 12
- év = 2017
  - hónap = 1
  - ...
  - hónap = 12
- év = 2018
  - hónap = 1
  - ...
  - hónap = 6

## <a name="query-set-of-parquet-files"></a>A Parquet-fájlok lekérdezési készlete

A Parquet-fájlok lekérdezése során csak a fontos oszlopokat adhatja meg.

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

## <a name="automatic-schema-inference"></a>Automatikus séma-következtetés

A Parquet-fájlok olvasásakor nem szükséges a OPENROWSET WITH záradék használata. Az oszlopnevek és az adattípusok automatikusan beolvashatók a Parquet-fájlokból.

Az alábbi minta a Parquet-fájlok automatikus sémájának következtetéseit mutatja be. A sorok számát a 2017 szeptemberében adja vissza a séma meghatározása nélkül.

> [!NOTE]
> A Parquet-fájlok olvasásakor nem szükséges oszlopokat megadni a OPENROWSET WITH záradékban. Ebben az esetben az SQL igény szerinti lekérdezési szolgáltatása metaadatokat használ a Parquet fájlban, és az oszlopokat név alapján köti össze.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>Particionált adatlekérdezés

Az ebben a mintában megadott adathalmaz külön almappákba van osztva (particionálva). A filepath függvény használatával konkrét partíciókat is megcélozhat. Ez a példa a viteldíjak összegét mutatja év, hónap és payment_type szerint az 2017-as első három hónapban.

> [!NOTE]
> Az igény szerinti SQL-lekérdezés a kaptár/Hadoop particionálási sémával kompatibilis.

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
| BINÁRIS |UTF8 |varchar \*(UTF8-rendezés) |
| BINÁRIS |KARAKTERLÁNC |varchar \*(UTF8-rendezés) |
| BINÁRIS |ENUM|varchar \*(UTF8-rendezés) |
| BINÁRIS |UUID |uniqueidentifier |
| BINÁRIS |DECIMÁLIS |tizedes tört |
| BINÁRIS |JSON |varchar (max) \*(UTF8-rendezés) |
| BINÁRIS |BSON |varbinary (max.) |
| FIXED_LEN_BYTE_ARRAY |DECIMÁLIS |tizedes tört |
| BYTE_ARRAY |IDŐKÖZ |varchar (max), szabványosított formátumba szerializálva |
| INT32 |INT (8, igaz) |smallint |
| INT32 |INT (16, igaz) |smallint |
| INT32 |INT (32, true) |int |
| INT32 |INT (8, hamis) |tinyint |
| INT32 |INT (16, hamis) |int |
| INT32 |INT (32, hamis) |bigint |
| INT32 |DATE |dátum |
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
