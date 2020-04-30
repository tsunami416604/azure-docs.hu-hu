---
title: Külső táblák létrehozása és használata az igény szerinti SQL-ben (előzetes verzió)
description: Ebből a szakaszból megtudhatja, hogyan hozhat létre és használhat külső táblákat az igény szerinti SQL-ben (előzetes verzió). A külső táblák akkor hasznosak, ha szabályozni szeretné az SQL igény szerinti külső adatelérését, és ha olyan eszközöket szeretne használni, mint például a Power BI, az SQL igény szerinti használatával.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a708e5e713e62ab5b7ca70b61235a84830b051cd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424551"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Külső táblák létrehozása és használata az igény szerinti SQL-ben (előzetes verzió) az Azure szinapszis Analytics használatával

Ebből a szakaszból megtudhatja, hogyan hozhat létre és használhat külső táblákat az igény szerinti SQL-ben (előzetes verzió). A külső táblák akkor hasznosak, ha szabályozni szeretné az SQL igény szerinti külső adatelérését, és ha olyan eszközöket szeretne használni, mint például a Power BI, az SQL igény szerinti használatával.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként tekintse át az alábbi cikkeket, és győződjön meg arról, hogy teljesítette az SQL igény szerinti külső táblák létrehozásához és használatához szükséges előfeltételeket:

- [Első beállítás](query-data-storage.md#first-time-setup)
- [Előfeltételek](query-data-storage.md#prerequisites)

## <a name="create-an-external-table"></a>Külső tábla létrehozása

Külső táblákat ugyanúgy hozhat létre, mint a normál SQL Server külső táblákat. Az alábbi lekérdezés egy külső táblát hoz létre, amely beolvassa a *populáció. csv* fájlt.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz: [mydbname], így Ön a létrehozott adatbázist használja. Ha nem hozott létre adatbázist, kérjük, olvassa el az [első alkalommal történő telepítést](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [CsvDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT CSVFileFormat
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT,
    FORMAT_OPTIONS (
        FIELD_TERMINATOR = ',',
        STRING_DELIMITER = '"',
        FIRST_ROW = 2
    )
);
GO

CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
);
WITH (
    LOCATION = 'population/population.csv',
    DATA_SOURCE = CsvDataSource,
    FILE_FORMAT = CSVFileFormat
);
GO
```

## <a name="use-a-external-table"></a>Külső tábla használata

A lekérdezésekben a külső táblákat ugyanúgy használhatja, mint SQL Server lekérdezésekben.

A következő lekérdezés azt mutatja be, hogyan kell használni a [külső tábla létrehozása](#create-an-external-table) szakaszban létrehozott, külső *populációs* táblát. 2019 csökkenő sorrendben adja vissza az ország nevét.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz: [mydbname], így Ön a létrehozott adatbázist használja. Ha nem hozott létre adatbázist, kérjük, olvassa el az [első alkalommal történő telepítést](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>További lépések

A lekérdezés eredményeinek a tárolóra való tárolásával kapcsolatos információkért tekintse meg az [áruház lekérdezési eredményeit a tárolóra](../sql/create-external-table-as-select.md).
