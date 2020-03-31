---
title: GetCurrentTimestamp az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL rendszer funkció GetCurrentTimestamp az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71351007"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
 Az 1970. 
  
## <a name="syntax"></a>Szintaxis
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus értéket ad eredményül, amely a Unix-korszak óta eltelt ezredmásodperc, azaz az 1970.

## <a name="remarks"></a>Megjegyzések

  A GetCurrentTimestamp() egy nem determinisztikus függvény.
  
  A visszaadott eredmény UTC (koordinált világidő).

## <a name="examples"></a>Példák
  
  A következő példa bemutatja, hogyan szerezheti be az aktuális időbélyeget a GetCurrentTimestamp() beépített függvény használatával.
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Íme egy példa eredményhalmaz.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB dátum- és időfüggvényei](sql-query-date-time-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
