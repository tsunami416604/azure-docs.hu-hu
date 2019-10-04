---
title: ARRAY_CONCAT Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function ARRAY_CONCAT Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ad973650ac205313f9045c170f99e15e385a82d1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348713"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 Egy tömb, amely az eredménye, összefűzi a két vagy több tömb értéket adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*arr_expr*  
   Egy tömb kifejezés, amely összefűzi a többi értéket. A `ARRAY_CONCAT` függvényhez legalább két *arr_expr* argumentum szükséges.  
  
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
  

## <a name="next-steps"></a>További lépések

- [Array függvények Azure Cosmos DB](sql-query-array-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
