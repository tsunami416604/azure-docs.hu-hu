---
title: ISMÉTLÉS az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL-rendszer függvény replikálása az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 19fcde522c5cb0355e53a5616145f27fada7dad9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302185"
---
# <a name="replicate-azure-cosmos-db"></a>REPLICATE (Azure Cosmos DB)
 Adott számú alkalommal ismétli a karakterlánc értékét.
  
## <a name="syntax"></a>Szintaxis
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.
  
*num_expr*  
   Ez egy numerikus kifejezés. Ha *num_expr* negatív vagy nem véges, az eredmény nincs definiálva.
  
## <a name="return-types"></a>Visszatérési típusok
  
  Karakterlánc-kifejezést ad vissza.
  
## <a name="remarks"></a>Megjegyzések
  Az eredmény maximális hossza 10 000 karakter, azaz (hossz(*str_expr)** *num_expr*) <= 10 000.

## <a name="examples"></a>Példák
  
  A következő példa bemutatja, hogyan használható `REPLICATE` egy lekérdezésben.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Itt van az eredményhalmaz.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB karakterlánc-függvényei](sql-query-string-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
