---
title: Külső táblák létrehozása és használata az SQL igény szerinti verziójában (előzetes verzió)
description: Ebben a szakaszban megtudhatja, hogyan hozhat létre és használhat külső táblákat az SQL igény szerinti (előzetes verzióban). A külső táblák akkor hasznosak, ha az SQL On-demand-ben szeretné szabályozni a külső adatokhoz való hozzáférést, és ha olyan eszközöket szeretne használni, mint például a Power BI, az SQL igény szerinti használatával együtt.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a708e5e713e62ab5b7ca70b61235a84830b051cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424551"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Külső táblák létrehozása és használata az SQL igény szerinti (előzetes verzióban) szolgáltatásában az Azure Synapse Analytics használatával

Ebben a szakaszban megtudhatja, hogyan hozhat létre és használhat külső táblákat az SQL igény szerinti (előzetes verzióban). A külső táblák akkor hasznosak, ha az SQL On-demand-ben szeretné szabályozni a külső adatokhoz való hozzáférést, és ha olyan eszközöket szeretne használni, mint például a Power BI, az SQL igény szerinti használatával együtt.

## <a name="prerequisites"></a>Előfeltételek

Az első lépés az alábbi cikkek áttekintése, és győződjön meg arról, hogy megfelelt az SQL igény szerinti külső táblák létrehozásának és használatának előfeltételeinek:

- [Első beállítás](query-data-storage.md#first-time-setup)
- [Előfeltételek](query-data-storage.md#prerequisites)

## <a name="create-an-external-table"></a>Külső tábla létrehozása

Külső táblákat ugyanúgy hozhat létre, mint a hagyományos SQL Server külső táblákat. Az alábbi lekérdezés létrehoz egy külső táblát, amely a *population.csv* fájlt olvassa.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz a [mydbname] sort, hogy a létrehozott adatbázist használja. Ha még nem hozott létre adatbázist, olvassa el [az Első alkalommal beállítás .](query-data-storage.md#first-time-setup)

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

A lekérdezésekben ugyanúgy használhat külső táblákat, mint az SQL Server-lekérdezésekben.

A következő lekérdezés bemutatja a külső [tábla létrehozása](#create-an-external-table) szakaszban létrehozott *külső populációs* külső tábla használatával. 2019-ben csökkenő sorrendben adja vissza a lakossággal rendelkező országneveket.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz a [mydbname] sort, hogy a létrehozott adatbázist használja. Ha még nem hozott létre adatbázist, olvassa el [az Első alkalommal beállítás .](query-data-storage.md#first-time-setup)

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

A lekérdezés ek tárolására szolgáló adatok tárolásáról a [Tároló lekérdezésének eredményeit a tárolóba](../sql/create-external-table-as-select.md)című dokumentum tartalmazza.
