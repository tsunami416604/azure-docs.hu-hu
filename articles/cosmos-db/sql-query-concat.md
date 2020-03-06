---
title: Összefűzés Azure Cosmos DB lekérdezési nyelven
description: Ismerje meg, hogy a Azure Cosmos DB CONCAt SQL System függvénye hogyan adja vissza a karakterláncot, amely a két vagy több karakterlánc-érték összefűzését eredményezi.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8a0941376ed74d7f8cb819d78df43eb9f0b7bd1
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302610"
---
# <a name="concat-azure-cosmos-db"></a>CONCAt (Azure Cosmos DB)
 Legalább két karakterlánc-értékek összetűzésének eredménye karakterláncként adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés, amely összefűzi a többi értéket. A `CONCAT` függvénynek legalább két *str_expr* argumentumot kell megadnia.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa a megadott értékek összefűzött karakterláncot adja vissza.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
