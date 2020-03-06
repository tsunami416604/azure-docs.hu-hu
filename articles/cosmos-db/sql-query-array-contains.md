---
title: Azure Cosmos DB lekérdezési nyelv ARRAY_CONTAINS
description: Ismerje meg, hogy a tömb hogyan tartalmazza az SQL System függvényt a Azure Cosmos DB egy logikai értéket ad vissza, amely jelzi, hogy a tömb tartalmazza-e a megadott értéket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 47fe20888aa546e414b268b30c2e03580750a040
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303477"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
Jelzi, hogy a tömb tartalmazza-e a megadott érték logikai érték beolvasása. Egy objektum részleges vagy teljes egyezését a parancsban található logikai kifejezés használatával tekintheti meg. 

## <a name="syntax"></a>Szintaxis
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*arr_expr*  
   A keresendő tömb kifejezése.  
  
*kifejezés*  
   A következő kifejezés található:.  

*bool_expr*  
   Egy logikai kifejezés. Ha a értéke "true" (igaz), és ha a megadott keresési érték egy objektum, a parancs részleges egyezést keres (a keresési objektum az egyik objektum részhalmaza). Ha "false" (hamis) értéket ad vissza, a parancs a tömbben lévő összes objektum teljes egyezését ellenőrzi. Ha nincs megadva, az alapértelmezett érték false (hamis). 
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai értéket ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa egy tömb tagságának ellenőrzését `ARRAY_CONTAINS`használatával.  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"b1": true, "b2": false}]  
```  

Az alábbi példa egy részleges egyezéssel a JSON használatával ARRAY_CONTAINS tömbben ellenőrzése.  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}]
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Következő lépések

- [Array függvények Azure Cosmos DB](sql-query-array-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
