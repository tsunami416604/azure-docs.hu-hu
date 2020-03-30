---
title: ABS az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg, hogyan adja vissza az Absolute(ABS) SQL rendszer függvényét az Azure Cosmos DB-ben a megadott numerikus kifejezés pozitív értékét.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6d173827f2695cc20fa208d390731acf0edb3848
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301097"
---
# <a name="abs-azure-cosmos-db"></a>ABS (Azure Cosmos DB)
 A megadott numerikus kifejezés abszolút (pozitív) értékét adja eredményül.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ABS (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Ez egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa a `ABS` függvény három különböző számon való használatának eredményeit mutatja be.  
  
```sql
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{abs1: 1, abs2: 0, abs3: 1}]  
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció egy [tartományindex](index-policy.md#includeexclude-strategy)előnyeit élvezi .

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Az Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
