---
title: Azure Cosmos DB lekérdezési nyelv KEREKÍTÉSe
description: Ismerkedjen meg az SQL System függvény Azure Cosmos DBával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4bad140ea1c6b1c59c8f752bc5336bbd49952e16
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349517"
---
# <a name="round-azure-cosmos-db"></a>KEREK (Azure Cosmos DB)
 Egy numerikus értéket, kerekítve a legközelebbi egész értéket ad vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="remarks"></a>Megjegyzések
  
  Az elvégezhető kerekítési művelet az a középpontba kerül, amely nulláról van lekerekítve. Ha a bemenet egy numerikus kifejezés, amely pontosan két egész szám közé esik, akkor az eredmény a legközelebb eső egész érték lesz a nulláról.  
  
  |< numeric_expr >|Lekerekített|
  |-|-|
  |– 6,5000|-7|
  |– 0,5|-1|
  |0,5|1|
  |6,5000|7||
  
## <a name="examples"></a>Példák
  
  Az alábbi példa a következő pozitív és negatív számokat a legközelebbi egész számra kerekít.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Íme az eredményhalmaz.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
