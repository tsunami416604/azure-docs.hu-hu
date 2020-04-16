---
title: Parquet beágyazott típusok lekérdezése az IGÉNY szerinti SQL használatával (előzetes verzió)
description: Ebből a cikkből megtudhatja, hogyan lehet lekérdezni a parketta beágyazott típusait.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a1e3d3c7494aa75b3f6d481d12135316791772d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431656"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Parketta beágyazott típusok lekérdezése az SQL igény szerinti (előzetes verzió) használatával az Azure Synapse Analytics szolgáltatásban

Ebben a cikkben megtudhatja, hogyan írhat lekérdezést az SQL igény szerinti (előzetes verzió) használatával az Azure Synapse Analytics szolgáltatásban.  Ez a lekérdezés a Parketta beágyazott típusait olvassa.

## <a name="prerequisites"></a>Előfeltételek

A cikk többi részének elolvasása előtt tekintse át a következő cikkeket:

- [Első beállítás](query-data-storage.md#first-time-setup)
- [Előfeltételek](query-data-storage.md#prerequisites)

## <a name="project-nested-or-repeated-data"></a>Projekt beágyazott vagy ismétlődő adatai

A következő lekérdezés beolvassa a *justSimpleArray.parquet* fájlt. A Parkettafájl összes oszlopát kivetíti, beleértve a beágyazott vagy ismétlődő adatokat is.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>Elemek elérése beágyazott oszlopokból

A következő lekérdezés beolvassa a *structExample.parquet* fájlt, és bemutatja, hogyan lehet egy beágyazott oszlop elemeit felszínre lépni:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/structExample.parquet',
        FORMAT='PARQUET'
    )
    WITH (
        -- you can see original n"sted columns values by uncommenting lines below
        --DateStruct VARCHAR(8000),
        [DateStruct.Date] DATE,
        --TimeStruct VARCHAR(8000),
        [TimeStruct.Time] TIME,
        --TimestampStruct VARCHAR(8000),
        [TimestampStruct.Timestamp] DATETIME2,
        --DecimalStruct VARCHAR(8000),
        [DecimalStruct.Decimal] DECIMAL(18, 5),
        --FloatStruct VARCHAR(8000),
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Elemek elérése ismétlődő oszlopokból

A következő lekérdezés beolvassa a *justSimpleArray.parquet* [fájlt,](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) és JSON_VALUE segítségével egy **skaláris** elemet olvas le egy ismétlődő oszlopból, például egy tömbből vagy egy térképből:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

A következő lekérdezés beolvassa a *mapExample.parquet* [fájlt,](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) és JSON_QUERY segítségével egy nem **skaláris** elemet olvas le egy ismétlődő oszlopból, például egy tömbből vagy egy térképből:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/mapExample.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="next-steps"></a>További lépések

A következő cikk bemutatja, hogyan kell lekérdezni a [JSON fájlokat.](query-json-files.md)
