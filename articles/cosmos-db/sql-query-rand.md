---
title: RAND Azure Cosmos DB lekérdezési nyelven
description: Ismerje meg az SQL System Function RAND Azure Cosmos DB-ben való megismerését.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4a0672000e630c9e06df84d9c2da5cb8b988c05a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349600"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
 Egy véletlenszerűen generált numerikus értéket ad vissza [0, 1).
 
## <a name="syntax"></a>Szintaxis
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Visszatérési típusok

  A numerikus kifejezést ad vissza.

## <a name="remarks"></a>Megjegyzések

  a `RAND` egy determinált függvény. A `RAND` ismétlődő hívásai nem adják vissza ugyanazt az eredményt.

## <a name="examples"></a>Példák
  
  A következő példa egy véletlenszerűen generált numerikus értéket ad vissza.
  
```sql
SELECT RAND() AS rand 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
