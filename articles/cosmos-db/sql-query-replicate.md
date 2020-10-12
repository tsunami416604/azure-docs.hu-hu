---
title: REPLIKÁLÁS Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System függvény REPLIKÁLÁSával Azure Cosmos DBban.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: aea29cfff6b3827cfb9169722e48120e3a5a3709
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88794321"
---
# <a name="replicate-azure-cosmos-db"></a>REPLIKÁLÁS (Azure Cosmos DB)
 Megadott számú alkalommal ismétli meg a karakterláncot.
  
## <a name="syntax"></a>Szintaxis
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.
  
*num_expr*  
   Egy numerikus kifejezés. Ha *num_expr* negatív vagy nem véges, az eredmény nincs meghatározva.
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezést ad vissza.
  
## <a name="remarks"></a>Megjegyzések

  Az eredmény maximális hossza 10 000 karakter, azaz (hossz (*str_expr*) *  *num_expr*) <= 10 000. Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan használható `REPLICATE` egy lekérdezésben.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Itt látható az eredményhalmaz.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
