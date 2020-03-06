---
title: REPLIKÁLÁS Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System függvény REPLIKÁLÁSával Azure Cosmos DBban.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 19fcde522c5cb0355e53a5616145f27fada7dad9
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302185"
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
   A numerikus kifejezés. Ha *num_expr* negatív vagy nem véges, az eredmény nincs meghatározva.
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezés adja vissza.
  
## <a name="remarks"></a>Megjegyzések
  Az eredmény maximális hossza 10 000 karakter, azaz (hossz (*str_expr*) * *num_expr*) < = 10 000.

## <a name="examples"></a>Példák
  
  Az alábbi példa azt szemlélteti, hogyan használható a `REPLICATE` egy lekérdezésben.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Íme az eredményhalmaz.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
