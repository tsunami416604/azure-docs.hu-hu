---
title: KEREKÍTÉS az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL-rendszer funkció KEREK az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b6aac5a963d0f58a3b21b9fb0958793169a3d444
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302117"
---
# <a name="round-azure-cosmos-db"></a>KEREK (Azure Cosmos DB)
 Egy numerikus értéket ad eredményül, amely a legközelebbi egész értékre kerekítve.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Ez egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="remarks"></a>Megjegyzések
  
  A végrehajtott kerekítési művelet a középpont im.l.-t követi a nullától távol. Ha a bemenet olyan numerikus kifejezés, amely pontosan két egész szám közé esik, akkor az eredmény a nullától a legközelebbi egész érték lesz.  
  
  |<numeric_expr numeric_expr> numeric_expr|Lekerekített|
  |-|-|
  |-6.5000|-7|
  |-0.5|-1|
  |0,5|1|
  |6.5000|7||
  
## <a name="examples"></a>Példák
  
  A következő példa a következő pozitív és negatív számokat a legközelebbi egész számra kerekíti.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Itt van az eredményhalmaz.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció egy [tartományindex](index-policy.md#includeexclude-strategy)előnyeit élvezi .

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Az Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
