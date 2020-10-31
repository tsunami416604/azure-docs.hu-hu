---
title: DateTimePart Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function DateTimePart Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: dd88f8d6a0f5271da97d6f2f95eecb60bf94ed78
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095798"
---
# <a name="datetimepart-azure-cosmos-db"></a>DateTimePart (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A megadott DateTimePart értékét adja vissza a megadott dátum és idő között.
  
## <a name="syntax"></a>Szintaxis
  
```sql
DateTimePart (<DateTimePart> , <DateTime>)
```

## <a name="arguments"></a>Argumentumok
  
*DateTimePart*  
   Annak a dátumnak a része, amelyre a DateTimePart vissza fogja adni az értéket. Ez a táblázat felsorolja az összes érvényes DateTimePart argumentumot:

| DateTimePart | rövidítéseket        |
| ------------ | -------------------- |
| Year         | "Year", "yyyy", "éé" |
| Month (hónap)        | "hónap", "PP", "m"   |
| Nap          | "Day", "dd", "d"     |
| Óra         | "Hour", "hh"         |
| Minute       | "minute", "mi", "n"  |
| Second       | "Second", "ss", "s"  |
| Ezredmásodperces  | "ezredmásodperc", "MS"  |
| Mikromásodperces  | "második", "MCS" |
| NS   | "NS", "NS"   |

*Dátum/idő*  
   Az UTC dátum és idő ISO 8601 karakterláncának értéke formátumban `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Visszatérési típusok

Pozitív egész értéket ad vissza.

## <a name="remarks"></a>Megjegyzések

`undefined`A DateTimePart a következő okok miatt fog visszatérni:

- A megadott DateTimePart érték érvénytelen.
- A DateTime nem érvényes ISO 8601 DateTime

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="examples"></a>Példák

Íme egy példa, amely a hónap egész értékét adja vissza:

```sql
SELECT DateTimePart("m", "2020-01-02T03:04:05.6789123Z") AS MonthValue
```

```json
[
    {
        "MonthValue": 1
    }
]
```

Íme egy példa, amely a másodpercenkénti számok számát adja vissza:

```sql
SELECT DateTimePart("mcs", "2020-01-02T03:04:05.6789123Z") AS MicrosecondsValue
```

```json
[
    {
        "MicrosecondsValue": 678912
    }
]
```

## <a name="next-steps"></a>Következő lépések

- [Dátum és idő függvények Azure Cosmos DB](sql-query-date-time-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
