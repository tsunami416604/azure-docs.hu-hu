---
title: Összefűzés Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function CONCAt Azure Cosmos DB-ben.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1e61d61a3d64ca7d7808619159e4dfc8e8b33d68
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351262"
---
# <a name="concat-azure-cosmos-db"></a>CONCAt (Azure Cosmos DB)
 Legalább két karakterlánc-értékek összetűzésének eredménye karakterláncként adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés, amely összefűzi a többi értéket. A `CONCAT` függvényhez legalább két *str_expr* argumentum szükséges.  
  
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
  

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
