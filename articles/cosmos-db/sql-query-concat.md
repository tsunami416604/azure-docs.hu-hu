---
title: CONCAT az Azure Cosmos DB lekérdezési nyelvében
description: Megtudhatja, hogy az Azure Cosmos DB CONCAT SQL rendszerfüggvénye hogyan ad vissza egy karakterláncot, amely két vagy több karakterlánc-érték összefűzésének eredménye
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8a0941376ed74d7f8cb819d78df43eb9f0b7bd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302610"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
 Olyan karakterláncot ad vissza, amely két vagy több karakterlánc-érték összefűzésének eredménye.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   A többi értékhez összefűző karakterlánc-kifejezés. A `CONCAT` függvénylegalább két *str_expr* argumentumot igényel.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Karakterlánc-kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a megadott értékek összefont karakterláncát adja vissza.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB karakterlánc-függvényei](sql-query-string-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
