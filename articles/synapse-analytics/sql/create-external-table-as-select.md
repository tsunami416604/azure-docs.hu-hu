---
title: Lekérdezési eredmények tárolása a tárolóba
description: Ebből a cikkből megtudhatja, hogyan tárolhatja a lekérdezési eredményeket a tárolóba az SQL igény szerinti (előzetes verzió) használatával.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 462185feb2b9cbebd17ce9cba54c2b23deea6c75
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425146"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>A lekérdezési eredmények tárolása a tárhelyhez az SQL igény szerinti (előzetes verzió) használatával az Azure Synapse Analytics használatával

Ebben a cikkben megtudhatja, hogyan tárolhatja a lekérdezési eredményeket a tárolóban az SQL On-demand (előzetes verzió) használatával.

## <a name="prerequisites"></a>Előfeltételek

Az első lépés az alábbi cikkek áttekintése és az előfeltételek teljesülése:

- [Első beállítás](query-data-storage.md#first-time-setup)
- [Előfeltételek](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>Külső tábla létrehozása kijelölésként

A CREATE EXTERNAL TABLE AS SELECT (CETAS) utasítást használhatja a lekérdezés eredményeinek tárolásához a tárolóban.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz a [mydbname] sort, hogy a létrehozott adatbázist használja. Ha még nem hozott létre adatbázist, olvassa el [az Első alkalommal beállítás .](query-data-storage.md#first-time-setup)

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

A CETAS-on keresztül létrehozott külső táblát normál külső táblához hasonlóan használhatja.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz a [mydbname] sort, hogy a létrehozott adatbázist használja. Ha még nem hozott létre adatbázist, olvassa el [az Első alkalommal beállítás .](query-data-storage.md#first-time-setup)

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

A különböző fájltípusok lekérdezéséről a [Query single CSV-fájl](query-single-csv-file.md), a [Query Parquet files](query-parquet-files.md)és a Query [JSON-fájlok](query-json-files.md) című cikkekben talál további információt.
