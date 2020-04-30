---
title: Nézetek létrehozása és használata igény szerinti SQL-ben (előzetes verzió)
description: Ebből a szakaszból megtudhatja, hogyan hozhat létre és használhat nézeteket az SQL igény szerinti (előzetes verziójú) lekérdezésének becsomagolásához. A nézetek segítségével újra felhasználhatja ezeket a lekérdezéseket. A nézetekre akkor is szükség van, ha olyan eszközöket kíván használni, mint például a Power BI az SQL igény szerinti használata esetén.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0f5323193706fdd00739be6c71a4fe12cfedf21b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424537"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Nézetek létrehozása és használata igény szerinti SQL-ben (előzetes verzió) az Azure szinapszis Analytics használatával

Ebből a szakaszból megtudhatja, hogyan hozhat létre és használhat nézeteket az SQL igény szerinti (előzetes verziójú) lekérdezésének becsomagolásához. A nézetek segítségével újra felhasználhatja ezeket a lekérdezéseket. A nézetekre akkor is szükség van, ha olyan eszközöket kíván használni, mint például a Power BI az SQL igény szerinti használata esetén.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként tekintse át az alábbi cikkeket, és győződjön meg arról, hogy teljesítette az SQL igény szerinti nézeteinek létrehozásához és használatához szükséges előfeltételeket:

- [Első beállítás](query-data-storage.md#first-time-setup)
- [Előfeltételek](query-data-storage.md#prerequisites)

## <a name="create-a-view"></a>Nézet létrehozása

A nézetek ugyanúgy hozhatók létre, mint a rendszeres SQL Server nézetek létrehozásakor. Az alábbi lekérdezés olyan nézetet hoz létre, amely beolvassa a *populációs. csv* fájlt.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz: [mydbname], így Ön a létrehozott adatbázist használja. Ha nem hozott létre adatbázist, kérjük, olvassa el az [első alkalommal történő telepítést](query-data-storage.md#first-time-setup).

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

A lekérdezésekben a nézeteket ugyanúgy használhatja, mint SQL Server lekérdezésekben.

A következő lekérdezés azt mutatja be, hogyan használja a [nézet létrehozása nézetben](#create-a-view)létrehozott *population_csv* nézetet. 2019 csökkenő sorrendben adja vissza az ország nevét.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz: [mydbname], így Ön a létrehozott adatbázist használja. Ha nem hozott létre adatbázist, kérjük, olvassa el az [első alkalommal történő telepítést](query-data-storage.md#first-time-setup).

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
