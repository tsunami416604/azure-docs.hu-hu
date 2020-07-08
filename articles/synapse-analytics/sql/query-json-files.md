---
title: JSON-fájlok lekérdezése az SQL on-demand használatával (előzetes verzió)
description: Ez a szakasz azt ismerteti, hogyan olvashatók be a JSON-fájlok az SQL igény szerinti használatával az Azure szinapszis Analytics szolgáltatásban.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 5d02736e9cb0a612e434dc5a79a73d7a62785728
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85207651"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>JSON-fájlok lekérdezése az SQL on-demand (előzetes verzió) használatával az Azure szinapszis Analyticsben

Ebből a cikkből megtudhatja, hogyan írhat egy lekérdezést az SQL on-demand (előzetes verzió) használatával az Azure szinapszis Analytics szolgáltatásban. A lekérdezés célja, hogy beolvassa a JSON-fájlokat. A támogatott formátumok a [OpenRowset](develop-openrowset.md)-ben vannak felsorolva.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként létre kell **hoznia egy adatbázist** , amelyen végre fogja hajtani a lekérdezéseket. Ezután inicializálja az objektumokat a [telepítési parancsfájl](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) végrehajtásával az adatbázison. Ez a telepítési parancsfájl létrehozza az adatforrásokat, az adatbázis-hatókörrel rendelkező hitelesítő adatokat, valamint az ezekben a mintákban használt külső fájlformátumokat.

## <a name="sample-json-files"></a>JSON-fájlok mintája

Az alábbi szakasz példákat tartalmaz a JSON-fájlok olvasásához. A fájlok *JSON* -tárolóban, *címjegyzékekben*tárolódnak, és a következő szerkezettel rendelkező egyetlen könyvből állhatnak:

```json
{
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

## <a name="read-json-files"></a>JSON-fájlok olvasása

A JSON-fájlok JSON_VALUE és [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)használatával történő feldolgozásához a JSON-fájlt egyetlen oszlopként kell beolvasnia a tárterületről. A következő parancsfájl egyetlen oszlopként olvassa *be abook1.js* fájlt:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'json/books/book1.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r];
```

> [!NOTE]
> A teljes JSON-fájlt egyetlen sorra vagy oszlopba olvassa. Így a FIELDTERMINATOR, a FIELDQUOTE és a ROWTERMINATOR értéke 0x0b.

## <a name="query-json-files-using-json_value"></a>JSON-fájlok lekérdezése JSON_VALUE használatával

Az alábbi lekérdezés azt mutatja be, hogyan használhatók a [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a skaláris értékek (title, kiadó) egy olyan könyvből való lekéréséhez, amely egy *valószínűségi és statisztikai metódusokat tartalmaz a Cryptology-ben, a kiválasztott témakörök bemutatása*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-json_query"></a>JSON-fájlok lekérdezése JSON_QUERY használatával

A következő lekérdezés azt mutatja be, hogyan használhatók a [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) egy olyan könyvben lévő objektumok és tömbök (szerzők) lekéréséhez *, amelyek valószínűségi és statisztikai módszerei a Cryptology-ben, a kiválasztott témakörök bemutatása*:

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-openjson"></a>JSON-fájlok lekérdezése a OPENJSON UTASÍTÁSSAL használatával

A következő lekérdezés a [openjson utasítással](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-t használja. Az objektumok és tulajdonságok lekérése egy, a *Cryptology-ben a valószínűségi és statisztikai módszerekkel foglalkozó könyvben, a kiválasztott témakörök bemutatása*:

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent NVARCHAR(max) -- Use appropriate length. Make sure JSON fits. 
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>További lépések

A sorozat következő cikkei a következőket szemléltetik:

- [Mappák és több fájl lekérdezése](query-folders-multiple-csv-files.md)
- [Nézetek létrehozása és használata](create-use-views.md)
