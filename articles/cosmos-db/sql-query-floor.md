---
title: PADLÓ Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az Azure Cosmos DB EMELETi SQL System függvénnyel, hogy a megadott numerikus kifejezésnél kisebb vagy azzal egyenlő legnagyobb egész számot adja vissza.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 04dfa6a028cf7c44bf99c665b396d51d8a0f3cef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78303188"
---
# <a name="floor-azure-cosmos-db"></a>EMELET (Azure Cosmos DB)
 A legnagyobb olyan egész számot adja vissza, amely kisebb vagy egyenlő, mint a megadott numerikus kifejezés.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
FLOOR (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa a függvényt tartalmazó pozitív numerikus, negatív és nulla értékeket mutatja `FLOOR` .  
  
```sql
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{fl1: 123, fl2: -124, fl3: 0}]  
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
