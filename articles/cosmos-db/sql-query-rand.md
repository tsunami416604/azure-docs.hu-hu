---
title: RAND az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL rendszer funkció RAND az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e44878b6d65725f08aeca4eb07088315ae2bb78a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302219"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
 Véletlenszerűen generált numerikus értéket ad eredményül a(z) [0,1 értékből).
 
## <a name="syntax"></a>Szintaxis
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Visszatérési típusok

  Numerikus kifejezést ad eredményül.

## <a name="remarks"></a>Megjegyzések

  `RAND`nem determinisztikus függvény. Az ismétlődő hívások `RAND` nem ugyanazt az eredményt adják vissza.

## <a name="examples"></a>Példák
  
  A következő példa egy véletlenszerűen generált numerikus értéket ad vissza.
  
```sql
SELECT RAND() AS rand 
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Az Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
