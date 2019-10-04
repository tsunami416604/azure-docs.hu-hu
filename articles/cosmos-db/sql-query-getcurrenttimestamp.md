---
title: GetCurrentTimestamp Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function GetCurrentTimestamp Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351007"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
 Azon ezredmásodpercek számát adja vissza, amelyek elteltek 00:00:00 csütörtök, 1970. január 1. között. 
  
## <a name="syntax"></a>Szintaxis
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus értéket ad vissza, amely a UNIX-kor óta eltelt ezredmásodpercek számát jelenti, azaz a 00:00:00 csütörtökön, a 1970. január 1. között eltelt ezredmásodpercek számát.

## <a name="remarks"></a>Megjegyzések

  A GetCurrentTimestamp () egy determinált-függvény.
  
  A visszaadott eredmény UTC (egyezményes világidő).

## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan kérheti le az aktuális időbélyeget a GetCurrentTimestamp () beépített függvény használatával.
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Íme egy példa az eredményhalmaz értékére.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>További lépések

- [Dátum és idő függvények Azure Cosmos DB](sql-query-date-time-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
