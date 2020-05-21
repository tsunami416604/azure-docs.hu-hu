---
title: Azure Cosmos DB lekérdezési nyelvet tartalmaz
description: Ismerje meg, hogy az Azure Cosmos DB SQL System függvénye hogyan ad vissza egy logikai értéket, amely azt jelzi, hogy az első karakterlánc-kifejezés tartalmazza-e a másodpercet
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0de34e6e0e238887b8f75ae2397e9e650eaac340
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83711703"
---
# <a name="contains-azure-cosmos-db"></a>TARTALMAZZA (Azure Cosmos DB)

 Egy logikai értéket ad vissza, amely azt jelzi, hogy az első karakterlánc-kifejezés tartalmazza-e a másodpercet.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr1*  
   A keresendő karakterlánc-kifejezés.  
  
*str_expr2*  
   A keresendő karakterlánc-kifejezés.  

*bool_expr* Nem kötelező érték az eset figyelmen kívül hagyásával. Ha True értékre van állítva, akkor a tartalmaz egy kis-és nagybetűket megkülönböztető keresést. Ha nincs megadva, ez az érték hamis.
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa ellenőrzi, hogy az "ABC" tartalmazza-e az "AB" kifejezést, és ha az "ABC" a "d" kifejezést tartalmazza.  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
