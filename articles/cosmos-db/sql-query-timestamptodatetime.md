---
title: TimestampToDateTime Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function TimestampToDateTime Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 9d4b5179ea08d5d6eca03422db7dfc7c8c4b5c3e
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608768"
---
# <a name="timestamptodatetime-azure-cosmos-db"></a>TimestampToDateTime (Azure Cosmos DB)

A megadott időbélyeg-értéket dátum és idő értékre alakítja.
  
## <a name="syntax"></a>Szintaxis
  
```sql
TimestampToDateTime (<Timestamp>)
```

## <a name="arguments"></a>Argumentumok

*Timestamp*  

Egy aláírt numerikus érték, a UNIX-kor óta eltelt ezredmásodpercek aktuális száma. Más szóval a 00:00:00 csütörtök, 1970. január 1. között eltelt ezredmásodpercek száma.

## <a name="return-types"></a>Visszatérési típusok

Az UTC dátum és idő ISO 8601 sztring értékét adja vissza abban a formátumban, `YYYY-MM-DDThh:mm:ss.fffffffZ` ahol:
  
  |Formátum|Leírás|
  |-|-|
  |ÉÉÉÉ|négyjegyű év|
  |MM|kétjegyű hónap (01 = Január stb.)|
  |DD|hónap kétjegyű napja (01 – 31)|
  |T|az időelemek kezdetének jelölője|
  |óó|kétjegyű óra (00 – 23)|
  |hh|kétjegyű perc (00 – 59)|
  |mm|kétszámjegyű másodperc (00 – 59)|
  |.fffffff|hét számjegyű tört másodperc|
  |Z|UTC (egyezményes világidő) jelölő||
  
  Az ISO 8601 formátumával kapcsolatos további információkért lásd: [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Megjegyzések

A TimestampToDateTime akkor tér vissza `undefined` , ha a megadott időbélyeg-érték érvénytelen.

## <a name="examples"></a>Példák
  
Az alábbi példa egy DateTime értékre konvertálja az időbélyeget:

```sql
SELECT TimestampToDateTime(1594227912345) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-08T17:05:12.3450000Z"
    }
]
```  

## <a name="next-steps"></a>Következő lépések

- [Dátum és idő függvények Azure Cosmos DB](sql-query-date-time-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
