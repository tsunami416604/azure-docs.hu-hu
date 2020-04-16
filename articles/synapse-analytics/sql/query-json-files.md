---
title: JSON-fájlok lekérdezése az igény szerinti SQL használatával (előzetes verzió)
description: Ez a szakasz bemutatja, hogyan olvashatja a JSON-fájlokat az Sql on-demand használatával az Azure Synapse Analytics szolgáltatásban.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1054328216d0517b3f450ba4fe08f3bef32d68f7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431721"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>JSON-fájlok lekérdezése az SQL igény szerinti (előzetes verzió) használatával az Azure Synapse Analytics szolgáltatásban

Ebben a cikkben megtudhatja, hogyan írhat lekérdezést az SQL igény szerinti (előzetes verzió) használatával az Azure Synapse Analytics szolgáltatásban. A lekérdezés célja a JSON-fájlok olvasása.

## <a name="prerequisites"></a>Előfeltételek

A cikk többi részének elolvasása előtt tekintse át a következő cikkeket:

- [Első beállítás](query-data-storage.md#first-time-setup)
- [Előfeltételek](query-data-storage.md#prerequisites)

## <a name="sample-json-files"></a>Minta JSON-fájlok

Az alábbi szakasz mintaparancsfájlokat tartalmaz a JSON-fájlok olvasásához. A fájlok egy *json* tárolóban, *mappakönyvekben*tárolódnak, és egyetlen könyvbejegyzést tartalmaznak, amelynek szerkezete a következő:

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

Ha a JSON-fájlokat JSON_VALUE és [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)használatával szeretné feldolgozni, a JSON-fájlt egyetlen oszlopban kell elolvasnia a tárolóból. A következő parancsfájl egyetlen oszlopban olvassa be a *book1.json* fájlt:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/book1.json',
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
> A teljes JSON-fájlt egysorosként vagy oszlopként olvassa. Tehát a FIELDTERMINATOR, A FIELDQUOTE ÉS a ROWTERMINATOR beállítása 0x0b.

## <a name="query-json-files-using-json_value"></a>JSON-fájlok lekérdezése JSON_VALUE

Az alábbi lekérdezés bemutatja, hogyan lehet [használni JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) letölteni skaláris értékek (cím, kiadó) egy könyv című *valószínűségi és statisztikai módszerek cryptology, Bevezetés a kiválasztott cikkek:*

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
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

## <a name="query-json-files-using-json_query"></a>JSON-fájlok lekérdezése JSON_QUERY

A következő lekérdezés bemutatja, hogyan lehet [használni JSON_QUERY,](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) hogy lekérje tárgyak és tömbök (szerzők) egy könyv című *valószínűségi és statisztikai módszerek kriptológiában, Bevezetés a kiválasztott témák:*

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
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

## <a name="query-json-files-using-openjson"></a>JSON-fájlok lekérdezése OPENJSON használatával

A következő lekérdezés [openjson](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Ez letölteni tárgyak és tulajdonságok című könyv *valószínűségi és statisztikai módszerek cryptology, Bevezetés a kiválasztott cikkek:*

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent NVARCHAR(4000) --Note that you have to use NVARCHAR(4000) for OPENJSON to work.
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>További lépések

A következő cikkek ebben a sorozatban bemutatja, hogyan kell:

- [Mappák és több fájl lekérdezése](query-folders-multiple-csv-files.md)
- [Nézetek létrehozása és használata](create-use-views.md).
