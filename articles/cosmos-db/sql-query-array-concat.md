---
title: Azure Cosmos DB lekérdezési nyelv ARRAY_CONCAT
description: Ismerje meg, hogy a tömb concat SQL System függvénye Azure Cosmos DB egy tömböt ad vissza, amely a két vagy több tömb értékének összefűzését eredményezi.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f2b37181e5d743809bb1f60be4056cb4442a8d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "78295878"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 Egy olyan tömböt ad vissza, amely két vagy több tömb értékének összefűzését eredményezi.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*arr_expr*  
   Egy tömb kifejezés, amely összefűzi a többi értéket. A `ARRAY_CONCAT` függvény legalább két *arr_expr* argumentumot igényel.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy tömböt megadó kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa két tömb összefűzését mutatja be.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>További lépések

- [Array függvények Azure Cosmos DB](sql-query-array-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
