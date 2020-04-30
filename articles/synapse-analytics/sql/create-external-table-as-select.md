---
title: Lekérdezés eredményeinek tárolása a tárterületen
description: Ebből a cikkből megtudhatja, hogyan tárolhat lekérdezési eredményeket a Storage-ban az SQL on-demand (előzetes verzió) használatával.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 462185feb2b9cbebd17ce9cba54c2b23deea6c75
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81425146"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Lekérdezési eredmények tárolása az SQL on-demand (előzetes verzió) használatával az Azure szinapszis Analytics használatával

Ebből a cikkből megtudhatja, hogyan tárolhat lekérdezési eredményeket a Storage-ban az SQL on-demand (előzetes verzió) használatával.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként tekintse át az alábbi cikkeket, és győződjön meg arról, hogy teljesítette az előfeltételeket:

- [Első beállítás](query-data-storage.md#first-time-setup)
- [Előfeltételek](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>Külső tábla létrehozása kijelölésként

A CREATE EXTERNAL TABLE AS SELECT (CETAS) utasítás használatával tárolhatja a lekérdezési eredményeket a tárolóban.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz: [mydbname], így Ön a létrehozott adatbázist használja. Ha nem hozott létre adatbázist, kérjük, olvassa el az [első alkalommal történő telepítést](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://showdemoweu.dfs.core.windows.net/data/population_csv/population.csv',
        FORMAT='CSV'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-a-external-table-created"></a>Létrehozott külső tábla használata

Használhatja a CETAS-en keresztül létrehozott külső táblázatot, például egy normál külső táblázatot.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz: [mydbname], így Ön a létrehozott adatbázist használja. Ha nem hozott létre adatbázist, kérjük, olvassa el az [első alkalommal történő telepítést](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>További lépések

A különböző fájltípusok lekérdezésével kapcsolatos további információkért tekintse meg az [egyszerű CSV-fájl lekérdezése](query-single-csv-file.md), a [Parquet-fájlok lekérdezése](query-parquet-files.md)és a [JSON-fájlok lekérdezése](query-json-files.md) című cikket.
