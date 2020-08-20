---
title: DateTimeToTimestamp Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function DateTimeToTimestamp Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 673e6a8cb86986ff24e3ee55589d13e8a9e4fdc5
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608762"
---
# <a name="datetimetotimestamp-azure-cosmos-db"></a>DateTimeToTimestamp (Azure Cosmos DB)

A megadott dátum és idő időbélyeget alakítja át.
  
## <a name="syntax"></a>Szintaxis
  
```sql
DateTimeToTimestamp (<DateTime>)
```

## <a name="arguments"></a>Argumentumok

*Dátum/idő*  
   Az UTC dátum és idő ISO 8601 karakterláncának értéke a (z) formátumban `YYYY-MM-DDThh:mm:ss.fffffffZ` :
  
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

## <a name="return-types"></a>Visszatérési típusok

Egy aláírt numerikus értéket ad vissza, amely a UNIX-kor óta eltelt ezredmásodpercek száma, azaz a 00:00:00 csütörtök és 1970 január 1. között eltelt ezredmásodpercek száma.

## <a name="remarks"></a>Megjegyzések

A DateTimeToTimestamp akkor tér vissza `undefined` , ha a megadott datetime érték érvénytelen.

## <a name="examples"></a>Példák
  
Az alábbi példa átalakítja a DateTime értéket egy időbélyegre:

```sql
SELECT DateTimeToTimestamp("2020-07-09T23:20:13.4575530Z") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594336813457
    }
]
```  

Egy további példa:

```sql
SELECT DateTimeToTimestamp("2020-07-09") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594252800000
    }
]
```  

## <a name="next-steps"></a>Következő lépések

- [Dátum és idő függvények Azure Cosmos DB](sql-query-date-time-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
