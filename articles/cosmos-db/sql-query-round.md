---
title: Azure Cosmos DB lekérdezési nyelv KEREKÍTÉSe
description: Ismerkedjen meg az SQL System függvény Azure Cosmos DBával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b6aac5a963d0f58a3b21b9fb0958793169a3d444
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302117"
---
# <a name="round-azure-cosmos-db"></a>KEREK (Azure Cosmos DB)
 Egy numerikus értéket ad vissza, amely a legközelebbi egész értékre van kerekítve.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="remarks"></a>Megjegyzések
  
  Az elvégezhető kerekítési művelet az a középpontba kerül, amely nulláról van lekerekítve. Ha a bemenet egy numerikus kifejezés, amely pontosan két egész szám közé esik, akkor az eredmény a legközelebb eső egész érték lesz a nulláról.  
  
  |<numeric_expr>|Lekerekített|
  |-|-|
  |– 6,5000|-7|
  |– 0,5|-1|
  |0,5|1|
  |6,5000|7||
  
## <a name="examples"></a>Példák
  
  A következő példa a következő pozitív és negatív számokat a legközelebbi egész számra kerekíti.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Itt látható az eredményhalmaz.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
