---
title: Összefűzés Azure Cosmos DB lekérdezési nyelven
description: Ismerje meg, hogy a Azure Cosmos DB CONCAt SQL System függvénye hogyan adja vissza a karakterláncot, amely a két vagy több karakterlánc-érték összefűzését eredményezi.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0b3401e8de4987670f74a02286cb66eb7a9dfb29
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081620"
---
# <a name="concat-azure-cosmos-db"></a>CONCAt (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Egy olyan karakterláncot ad vissza, amely két vagy több karakterlánc-érték összefűzését eredményezi.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés, amely összefűzi a többi értéket. A `CONCAT` függvény legalább két *str_expr* argumentumot igényel.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a megadott értékek összefűzött karakterláncát adja vissza.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
