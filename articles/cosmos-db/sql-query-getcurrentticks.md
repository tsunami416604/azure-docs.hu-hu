---
title: GetCurrentTicks Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function GetCurrentTicks Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 610c545bf25822d27e0a641a1b2631f899502420
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340158"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A 00:00:00 csütörtök és 1970 január 1. között eltelt 100-ns kullancsok számát adja vissza.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Visszatérési típusok

Egy aláírt numerikus értéket ad vissza, amely a UNIX-kor óta eltelt 100 ns-osztásjelek aktuális számát adja eredményül. Ez azt jelenti, hogy a GetCurrentTicks a 100-es NS-kullancsok számát adja vissza, amelyek 00:00:00 csütörtök és 1970 január 1. között elteltek.

## <a name="remarks"></a>Megjegyzések

A GetCurrentTicks () egy determinált-függvény. A visszaadott eredmény UTC (egyezményes világidő).

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="examples"></a>Példák

Íme egy példa, amely az aktuális időpontot adja vissza, amely a kullancsokban van kifejezve:

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>További lépések

- [Dátum és idő függvények Azure Cosmos DB](sql-query-date-time-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
