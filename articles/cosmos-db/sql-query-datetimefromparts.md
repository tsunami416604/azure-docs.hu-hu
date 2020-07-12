---
title: DateTimeFromParts Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function DateTimeFromParts Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: aec567c642f8eb3cb421ef5a119fe58f3a5fe05a
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86262063"
---
# <a name="datetimefromparts-azure-cosmos-db"></a>DateTimeFromParts (Azure Cosmos DB)

A bemeneti értékből létrehozott dátum/idő értéket adja vissza.
  
## <a name="syntax"></a>Szintaxis
  
```sql
DateTimeFromParts(<numberYear>, <numberMonth>, <numberDay> [, numberHour]  [, numberMinute]  [, numberSecond] [, numberOfFractionsOfSecond])
```

## <a name="arguments"></a>Argumentumok
  
*numberYear* Az év egész értékének formátuma`YYYY`

*numberMonth*  
   A hónap egész értékének formátuma`MM`

*numberDay*  
   A nap egész értékének formátuma`DD`

*numberHour* (nem kötelező) egész érték az óra formátumában`hh`

*numberMinute* (nem kötelező) egész érték a perc formátumában`mm`

*numberSecond* (nem kötelező) egész érték a második formátumnál`ss`

*numberOfFractionsOfSecond* (nem kötelező) egész érték a másodpercek töredékes formátuma esetén`.fffffff`

## <a name="return-types"></a>Visszatérési típusok

Egy UTC dátum és idő ISO 8601 sztring értékét adja vissza abban a formátumban, `YYYY-MM-DDThh:mm:ss.fffffffZ` ahol:
  
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

Ha a megadott egész számok érvénytelen DateTime értéket hoznak létre, a rendszer visszaadja a DateTimeFromParts `undefined` .

Ha nem ad meg egy választható argumentumot, az értéke 0 lesz.

## <a name="examples"></a>Példák

Íme egy példa, amely csak a szükséges argumentumokat tartalmazza a DateTime létrehozásához:

```sql
SELECT DateTimeFromParts(2020, 9, 4) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T00:00:00.0000000Z"
    }
]
```

Íme egy másik példa, amely néhány opcionális argumentumot is használ a DateTime létrehozásához:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:00.0000000Z"
    }
]
```

Íme egy másik példa, amely az összes opcionális argumentumot is használja a DateTime létrehozásához:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52, 12, 3456789) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:12.3456789Z"
    }
]
```

## <a name="next-steps"></a>Következő lépések

- [Dátum és idő függvények Azure Cosmos DB](sql-query-date-time-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
