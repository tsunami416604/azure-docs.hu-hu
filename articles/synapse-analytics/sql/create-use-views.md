---
title: Nézetek létrehozása és használata igény szerinti SQL-ben (előzetes verzió)
description: Ebből a szakaszból megtudhatja, hogyan hozhat létre és használhat nézeteket az SQL igény szerinti (előzetes verziójú) lekérdezésének becsomagolásához. A nézetek segítségével újra felhasználhatja ezeket a lekérdezéseket. A nézetekre akkor is szükség van, ha olyan eszközöket kíván használni, mint például a Power BI az SQL igény szerinti használata esetén.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: ca60b7c12ec7e7a5e04202e377c345055ce1090c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83736007"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Nézetek létrehozása és használata igény szerinti SQL-ben (előzetes verzió) az Azure szinapszis Analytics használatával

Ebből a szakaszból megtudhatja, hogyan hozhat létre és használhat nézeteket az SQL igény szerinti (előzetes verziójú) lekérdezésének becsomagolásához. A nézetek segítségével újra felhasználhatja ezeket a lekérdezéseket. A nézetekre akkor is szükség van, ha olyan eszközöket kíván használni, mint például a Power BI az SQL igény szerinti használata esetén.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként létre kell hoznia egy adatbázist, amelyben létrejön a nézet, és inicializálnia kell az Azure Storage-ban való hitelesítéshez szükséges objektumokat a [telepítési parancsfájlnak](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) az adatbázison történő végrehajtásával. A cikkben szereplő összes lekérdezés végrehajtása a mintaadatbázis alapján történik.

## <a name="create-a-view"></a>Nézet létrehozása

A nézetek ugyanúgy hozhatók létre, mint a rendszeres SQL Server nézetek létrehozásakor. A következő lekérdezés olyan nézetet hoz létre, amely beolvassa a *populációs. csv* fájlt.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz: [mydbname], így Ön a létrehozott adatbázist használja.

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
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

Az ebben a példában szereplő nézet az `OPENROWSET` alapul szolgáló fájlok abszolút elérési útját használó függvényt használja. Ha rendelkezik a `EXTERNAL DATA SOURCE` tároló gyökerének URL-címével, a és a `OPENROWSET` `DATA_SOURCE` relatív elérési út is használható:

```
CREATE VIEW TaxiView
AS SELECT *, nyc.filepath(1) AS [year], nyc.filepath(2) AS [month]
FROM
    OPENROWSET(
        BULK 'parquet/taxi/year=*/month=*/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc
```

## <a name="use-a-view"></a>Nézet használata

A lekérdezésekben a nézeteket ugyanúgy használhatja, mint SQL Server lekérdezésekben.

A következő lekérdezés azt mutatja be, hogyan használja a [nézet létrehozása nézetben](#create-a-view)létrehozott *population_csv* nézetet. Az ország/régió nevét adja vissza a 2019-es populációban csökkenő sorrendben.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz: [mydbname], így Ön a létrehozott adatbázist használja.

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

A különböző fájltípusok lekérdezésével kapcsolatos további információkért tekintse meg az [egyszerű CSV-fájl lekérdezése](query-single-csv-file.md), a [Parquet-fájlok lekérdezése](query-parquet-files.md)és a [JSON-fájlok lekérdezése](query-json-files.md) című cikket.
