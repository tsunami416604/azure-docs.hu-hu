---
title: StringToArray Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function StringToArray Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2d1f90da50950ac6ff4f87ffe96ebad9f3d811cc
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349275"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
 Egy tömbre fordított kifejezést ad vissza. Ha a kifejezés nem fordítható le, a nem definiált értéket adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés, amely JSON-tömb kifejezésként lesz értelmezve. 
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy tömböt megadó kifejezést vagy nem definiált értéket ad vissza. 
  
## <a name="remarks"></a>Megjegyzések
  A beágyazott karakterlánc-értékeket idézőjelek közé kell írni, hogy érvényes JSON legyen. A JSON formátumával kapcsolatos részletekért lásd: [JSON.org](https://json.org/)
  
## <a name="examples"></a>Példák
  
  Az alábbi példa azt szemlélteti, hogyan viselkedik a `StringToArray` a különböző típusok között. 
  
 Az alábbi példák érvényes bemenettel rendelkeznek.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Íme az eredményhalmaz.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Az alábbi példa érvénytelen bemenetet mutat be. 
   
 A tömbben lévő szimpla idézőjelek nem érvényesek a JSON-ban.
Annak ellenére, hogy egy lekérdezésen belül érvényesek, nem fogják értelmezni az érvényes tömböket. A tömb karakterláncán belüli karakterláncokat el kell menekülnie a következőnek: "[\\" \\ "]", vagy a környező idézőjelnek egyetlen "[" "]" értékűnek kell lennie.

```sql
SELECT
    StringToArray("['5','6','7']")
```

Íme az eredményhalmaz.

```json
[{}]
```

A következő példák érvényes bemenetre mutatnak.
   
 Az átadott kifejezés JSON-tömbként lesz értelmezve; a következő nem értékeli ki a tömböt, ezért a nem definiált értéket adja vissza.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Íme az eredményhalmaz.

```json
[{}]
```

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
