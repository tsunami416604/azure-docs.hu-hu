---
title: StringToObject az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL rendszer funkció StringToObject az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c3e61d1efe20910d84ef4ff583d74982b3ea9f3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296381"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
 Objektummá fordított kifejezést ad vissza. Ha a kifejezés nem fordítható le, akkor nem definiált értéket ad vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   JSON-objektumkifejezésként elemezendő karakterlánc-kifejezés. Ne feledje, hogy a beágyazott karakterlánc-értékeket dupla idézőjelekkel kell írni, hogy érvényesek legyenek. A JSON formátumról a [json.org](https://json.org/)  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Objektumkifejezést ad vissza, vagy nem definiált.  
  
## <a name="examples"></a>Példák
  
  A következő példa `StringToObject` bemutatja, hogyan viselkedik a különböző típusok között. 
  
 Az alábbiakban érvényes bemenettel rendelkező példákat mutatunk be.

```sql
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Itt van az eredményhalmaz.

```json
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 Az alábbi példák érvénytelen bemenettel rendelkeznek.
Annak ellenére, hogy egy lekérdezésen belül érvényesek, nem elemzik érvényes objektumokat. Az objektumsorozaton belüli karakterláncokat vagy meg\\kell\\kerülni\\a\\"{ "a ": "str "}" vagy a környező idézőjelek között lévő karakterláncok legyenek "{"a": "str"}" típusúak.

A tulajdonságneveket körülvevő egyszeres idézőjelek nem érvényesek json.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Itt van az eredményhalmaz.

```json
[{}]
```  

A környező idézőjelek nélküli tulajdonságnevek érvénytelenek JSON.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Itt van az eredményhalmaz.

```json
[{}]
``` 

Az alábbi példák érvénytelen bemenettel rendelkeznek.

 Az átadott kifejezés JSON-objektumként lesz elemezve; ezek a bemenetek nem értékelik ki a típusobjektumot, és így nem definiált értéket adnak vissza.

```sql
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 Itt van az eredményhalmaz.

```json
[{}]
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB karakterlánc-függvényei](sql-query-string-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
