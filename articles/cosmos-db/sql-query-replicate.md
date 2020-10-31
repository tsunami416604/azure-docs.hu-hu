---
title: REPLIKÁLÁS Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System függvény REPLIKÁLÁSával Azure Cosmos DBban.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8b1fc1cb28b62a388df53238df58420bd3317a8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082861"
---
# <a name="replicate-azure-cosmos-db"></a>REPLIKÁLÁS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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

  Az eredmény maximális hossza 10 000 karakter, azaz (hossz ( *str_expr* ) *  *num_expr* ) <= 10 000. Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan használható `REPLICATE` egy lekérdezésben.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Itt látható az eredményhalmaz.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
