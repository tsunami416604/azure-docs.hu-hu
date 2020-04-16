---
title: Nézetek létrehozása és használata az SQL igény szerinti verziójában (előzetes verzió)
description: Ebben a szakaszban megtudhatja, hogyan hozhat létre és használhat nézeteket az SQL igény szerinti (előzetes verziójú) lekérdezéseinek burkolásához. A nézetek lehetővé teszik a lekérdezések újbóli felhasználását. Nézetekre akkor is szükség van, ha olyan eszközöket szeretne használni, mint például a Power BI, az SQL igény szerinti használatával együtt.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0f5323193706fdd00739be6c71a4fe12cfedf21b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424537"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Nézetek létrehozása és használata az SQL-ben igény szerint (előzetes verzió) az Azure Synapse Analytics használatával

Ebben a szakaszban megtudhatja, hogyan hozhat létre és használhat nézeteket az SQL igény szerinti (előzetes verziójú) lekérdezéseinek burkolásához. A nézetek lehetővé teszik a lekérdezések újbóli felhasználását. Nézetekre akkor is szükség van, ha olyan eszközöket szeretne használni, mint például a Power BI, az SQL igény szerinti használatával együtt.

## <a name="prerequisites"></a>Előfeltételek

Az első lépés az alábbi cikkek áttekintése, és győződjön meg arról, hogy megfelelt az SQL igény szerinti nézetek létrehozásának és használatának előfeltételeinek:

- [Első beállítás](query-data-storage.md#first-time-setup)
- [Előfeltételek](query-data-storage.md#prerequisites)

## <a name="create-a-view"></a>Nézet létrehozása

A nézeteket ugyanúgy hozhat létre, mint a normál SQL Server nézeteket. Az alábbi lekérdezés a *population.csv* fájlt beolvasó nézetet hoz létre.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz a [mydbname] sort, hogy a létrehozott adatbázist használja. Ha még nem hozott létre adatbázist, olvassa el [az Első alkalommal beállítás .](query-data-storage.md#first-time-setup)

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV', 
        FIELDTERMINATOR =',', 
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r];
```

## <a name="use-a-view"></a>Nézet használata

A lekérdezésekben ugyanúgy használhatja a nézeteket, mint az SQL Server-lekérdezésekben.

A következő lekérdezés a [nézet létrehozása](#create-a-view)mezőben létrehozott *population_csv* nézet használatával szemlélteti. 2019-ben csökkenő sorrendben adja vissza a lakossággal rendelkező országneveket.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz a [mydbname] sort, hogy a létrehozott adatbázist használja. Ha még nem hozott létre adatbázist, olvassa el [az Első alkalommal beállítás .](query-data-storage.md#first-time-setup)

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>További lépések

A különböző fájltípusok lekérdezéséről a [Query single CSV-fájl](query-single-csv-file.md), a [Query Parquet files](query-parquet-files.md)és a Query [JSON-fájlok](query-json-files.md) című cikkekben talál további információt.
