---
title: ARRAY_CONTAINS az Azure Cosmos DB lekérdezési nyelvében
description: Megtudhatja, hogy a tömb hogyan tartalmazza az SQL rendszerfüggvényt az Azure Cosmos DB-ben, logikai értéket ad vissza, amely jelzi, hogy a tömb tartalmazza-e a megadott értéket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 47fe20888aa546e414b268b30c2e03580750a040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303477"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
Logikai értéket ad vissza, amely azt jelzi, hogy a tömb tartalmazza-e a megadott értéket. Egy objektum részleges vagy teljes egyezését a parancsban lévő logikai kifejezés sel ellenőrizheti. 

## <a name="syntax"></a>Szintaxis
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*arr_expr*  
   A tömbkifejezés ben keresendő.  
  
*Kifejezés*  
   A megtalált kifejezés.  

*bool_expr*  
   Ez egy logikai kifejezés. Ha a kiértékelés "igaz", és a megadott keresési érték objektum, a parancs részleges egyezést keres (a keresési objektum az egyik objektum részhalmaza). Ha azt értékeli, hogy "hamis", a parancs ellenőrzi a teljes egyezést az összes objektum a tömbön belül. Ha nincs megadva, az alapértelmezett érték hamis. 
  
## <a name="return-types"></a>Visszatérési típusok
  
  Logikai értéket ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa arra, hogyan ellenőrizheti a tömbtagságot a használatával. `ARRAY_CONTAINS`  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"b1": true, "b2": false}]  
```  

A következő példa, hogyan ellenőrizheti a JSON részleges egyezését egy tömbben ARRAY_CONTAINS használatával.  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}]
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció egy [tartományindex](index-policy.md#includeexclude-strategy)előnyeit élvezi .

## <a name="next-steps"></a>További lépések

- [Tömbfüggvények Az Azure Cosmos DB](sql-query-array-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
