---
title: ENDSWITH Azure Cosmos DB lekérdezési nyelven
description: A Azure Cosmos DB ENDSWITH SQL System függvényének megismerése egy olyan logikai érték visszaadásához, amely azt jelzi, hogy az első karakterlánc kifejezése a másodikval végződik-e
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d7e7f3e33389d4a201ec3281829cb9f0415978e6
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83713556"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)

 Egy olyan logikai érték beolvasása, amely azt jelzi, hogy az első karakterlánc kifejezése a másodperctel végződik-e  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr1*  
   Egy karakterlánc-kifejezés.  
  
*str_expr2*  
   A *str_expr1*végéhez összehasonlítandó karakterlánc-kifejezés.

*bool_expr* Nem kötelező érték az eset figyelmen kívül hagyásával. Ha igaz értékre van állítva, a ENDSWITH kis-és nagybetűket nem megkülönböztető keresést hajt végre. Ha nincs megadva, ez az érték hamis.
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa az "ABC" végződést adja vissza "b" és "BC" értékkel.  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
