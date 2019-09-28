---
title: REPLIKÁLÁS Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System függvény REPLIKÁLÁSával Azure Cosmos DBban.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5e0b7f29c503daa8a95dcc46238e60728c0cec50
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349567"
---
# <a name="replicate-azure-cosmos-db"></a>REPLIKÁLÁS (Azure Cosmos DB)
 Egy karakterláncértéket a megadott számú alkalommal ismétlődik.
  
## <a name="syntax"></a>Szintaxis
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.
  
*num_expr*  
   A numerikus kifejezés. Ha a *num_expr* negatív vagy nem véges, az eredmény nincs meghatározva.
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezés adja vissza.
  
## <a name="remarks"></a>Megjegyzések
  Az eredmény maximális hossza 10 000 karakter, azaz (hossz (*str_expr*) * *num_expr*) < = 10 000.

## <a name="examples"></a>Példák
  
  Az alábbi példa azt szemlélteti, hogyan használható a `REPLICATE` a lekérdezésekben.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Íme az eredményhalmaz.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
