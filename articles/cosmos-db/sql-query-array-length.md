---
title: ARRAY_LENGTH az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg, hogyan adja vissza a tömbhosszúságú SQL-rendszer függvényt az Azure Cosmos DB-ben a megadott tömbkifejezés elemeinek számát.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3596ce4bc702d5e54225d8c90db2f9563feab670
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303987"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH (Azure Cosmos DB)
 A megadott tömbkifejezés elemeinek számát adja eredményül.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*arr_expr*  
   Tömbkifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa, hogyan lehet letenni a tömb hosszát a használatával. `ARRAY_LENGTH`  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"len": 3}]  
```  
  
## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Tömbfüggvények Az Azure Cosmos DB](sql-query-array-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
