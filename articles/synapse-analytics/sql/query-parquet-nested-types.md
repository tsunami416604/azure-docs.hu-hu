---
title: A parketta beágyazott típusai lekérdezése az SQL on-demand (előzetes verzió) használatával
description: Ebből a cikkből megtudhatja, hogyan lehet lekérdezni a parketta beágyazott típusait.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 82edee84317b5d542bf65e29514286f96c18bbcc
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744228"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Az SQL on-demand (előzetes verzió) használata az Azure szinapszis Analytics szolgáltatásban a parketta beágyazott típusainak lekérdezése

Ebből a cikkből megtudhatja, hogyan írhat egy lekérdezést az SQL on-demand (előzetes verzió) használatával az Azure szinapszis Analytics szolgáltatásban.  Ez a lekérdezés a parketta beágyazott típusait olvassa be.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként létre kell **hoznia** egy olyan adatbázist, amely hivatkozik az adatforrásra. Ezután inicializálja az objektumokat a [telepítési parancsfájl](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) végrehajtásával az adatbázison. Ez a telepítési parancsfájl létrehozza az adatforrásokat, az adatbázis-hatókörrel rendelkező hitelesítő adatokat, valamint az ezekben a mintákban használt külső fájlformátumokat.

## <a name="project-nested-or-repeated-data"></a>Beágyazott vagy ismétlődő projekt

A következő lekérdezés beolvassa a *justSimpleArray. Parque* fájlt. A program a Parquet fájl összes oszlopát a beágyazott vagy ismétlődő adatokkal együtt feltervezi.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>Elemek elérése beágyazott oszlopokból

A következő lekérdezés beolvassa a *structExample. Parque* fájlt, és bemutatja, hogyan lehet egy beágyazott oszlop Surface elemeit:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
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

A következő lekérdezés beolvassa a *justSimpleArray. Parque* fájlt, és a [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) használatával beolvas egy **skaláris** elemet egy ismétlődő oszlopból, például egy tömbből vagy egy térképből:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

A következő lekérdezés beolvassa a *mapExample. Parque* fájlt, és a [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) használatával lekéri a **nem skaláris** elemet egy ismétlődő oszlopból, például tömbből vagy térképből:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="next-steps"></a>További lépések

A következő cikk bemutatja, hogyan lehet [lekérdezni a JSON-fájlokat](query-json-files.md).
