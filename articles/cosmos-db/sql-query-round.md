---
title: Azure Cosmos DB lekérdezési nyelv KEREKÍTÉSe
description: Ismerkedjen meg az SQL System függvény Azure Cosmos DBával.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 41b1372cd5165b3548a4e574e7eb037111188bac
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341552"
---
# <a name="round-azure-cosmos-db"></a>KEREK (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
  
  Az elvégezhető kerekítési művelet az a középpontba kerül, amely nulláról van lekerekítve. Ha a bemenet egy numerikus kifejezés, amely pontosan két egész szám közé esik, akkor az eredmény a legközelebb eső egész érték lesz a nulláról. Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy).
  
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

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
