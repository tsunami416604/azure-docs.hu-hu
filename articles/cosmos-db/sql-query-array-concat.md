---
title: ARRAY_CONCAT az Azure Cosmos DB lekérdezési nyelvében
description: Megtudhatja, hogy az Azure Cosmos DB Array Concat SQL rendszerfüggvénye hogyan ad vissza egy tömböt, amely két vagy több tömbérték összefűzésének eredménye
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f2b37181e5d743809bb1f60be4056cb4442a8d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78295878"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 Olyan tömböt ad eredményül, amely két vagy több tömbérték összefűzésének eredménye.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*arr_expr*  
   A többi értékhez összefűző tömbkifejezés. A `ARRAY_CONCAT` függvénylegalább két *arr_expr* argumentumot igényel.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Tömbkifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa két tömb összefűzésére.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Tömbfüggvények Az Azure Cosmos DB](sql-query-array-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
