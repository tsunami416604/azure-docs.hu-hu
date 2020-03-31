---
title: StringToArray az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL rendszer függvényStringToArray az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 18acbd94fa3d717fc20b9e1020b9bf7c6db7744d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302916"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
 Tömbre fordított kifejezést ad vissza. Ha a kifejezés nem fordítható le, akkor nem definiált értéket ad vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   JSON tömbkifejezésként elemezendő karakterlánc-kifejezés. 
  
## <a name="return-types"></a>Visszatérési típusok
  
  Tömbkifejezést ad vissza, vagy nem definiált. 
  
## <a name="remarks"></a>Megjegyzések
  A beágyazott karakterlánc-értékeket dupla idézőjelekkel kell írni ahhoz, hogy érvényes JSON-ok legyenek. A JSON formátumról a [json.org](https://json.org/)
  
## <a name="examples"></a>Példák
  
  A következő példa `StringToArray` bemutatja, hogyan viselkedik a különböző típusok között. 
  
 Az alábbiakban érvényes bemenettel rendelkező példákat mutatunk be.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Itt van az eredményhalmaz.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Az alábbi példa érvénytelen bemenetet mutat be. 
   
 A tömbön belüli egyszeres idézőjelek nem érvényesek json.
Annak ellenére, hogy egy lekérdezésen belül érvényesek, nem elemzik az érvényes tömböket. A tömbkarakterláncok karakterláncait vagy meg\\\\kell kerülni a "[ " "]" karakterből, vagy a környező idézőjeleknek "[""]"-nak kell lenniük.

```sql
SELECT
    StringToArray("['5','6','7']")
```

Itt van az eredményhalmaz.

```json
[{}]
```

Az alábbi példák érvénytelen bemenetre mutatnak.
   
 Az átadott kifejezés JSON-tömbként lesz elemezve; a következő nem értékeli ki a szövegtömböt, és így nem definiált értéket ad vissza.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
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
