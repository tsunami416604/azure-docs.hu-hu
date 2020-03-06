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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78295878"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 Egy tömb, amely az eredménye, összefűzi a két vagy több tömb értéket adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*arr_expr*  
   Egy tömb kifejezés, amely összefűzi a többi értéket. A `ARRAY_CONCAT` függvénynek legalább két *arr_expr* argumentumot kell megadnia.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy tömböt megadó kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa hogyan fűzze össze két tömb.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Array függvények Azure Cosmos DB](sql-query-array-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
