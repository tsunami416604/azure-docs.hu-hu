---
title: ABS Azure Cosmos DB lekérdezési nyelven
description: Ismerje meg, hogy az Azure Cosmos DB abszolút (ABS) SQL System függvénye hogyan adja vissza a megadott numerikus kifejezés pozitív értékét.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6d173827f2695cc20fa208d390731acf0edb3848
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78301097"
---
# <a name="abs-azure-cosmos-db"></a>ABS (Azure Cosmos DB)
 A megadott numerikus kifejezés (pozitív) abszolút értékét adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ABS (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa az `ABS` függvény három különböző számon való használatának eredményét mutatja be.  
  
```sql
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{abs1: 1, abs2: 0, abs3: 1}]  
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
