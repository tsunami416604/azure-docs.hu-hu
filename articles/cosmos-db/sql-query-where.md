---
title: WHERE záradék az Azure Cosmos DB-ben
description: Információ az Azure Cosmos DB SQL WHERE záradékáról
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 483a0533eafc81ef8698d260a753062ae074f6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898774"
---
# <a name="where-clause-in-azure-cosmos-db"></a>WHERE záradék az Azure Cosmos DB-ben

A választható WHERE`WHERE <filter_condition>`záradék ( ) olyan feltételt (feltételeket) határoz meg, amelyeknek a forrás JSON-elemeknek meg kell felelniük ahhoz, hogy a lekérdezés bevonja őket az eredményekbe. A JSON-elemnek ki kell `true` értékelnie az eredményhez figyelembe veendő feltételeket. Az indexréteg a WHERE záradék segítségével határozza meg a forráselemek azon legkisebb részhalmazát, amely az eredmény része lehet.
  
## <a name="syntax"></a>Szintaxis
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumentumok

- `<filter_condition>`  
  
   Megadja a visszaadandó dokumentumok teljesítésének feltételét.  
  
- `<scalar_expression>`  
  
   A kiszámítandó értéket jelölő kifejezés. A részleteket [lásd: Skaláris kifejezések.](sql-query-scalar-expressions.md)  
  
## <a name="remarks"></a>Megjegyzések
  
  Ahhoz, hogy a dokumentum visszaadható legyen, a szűrőfeltételként megadott kifejezésnek igaz értéket kell kiértékelnie. Csak a `true` logikai érték felel meg a feltételnek, bármely más érték: nem definiált, null, hamis, Szám, Tömb vagy Objektum nem felel meg a feltételnek.

  Ha a partíciókulcsot egy `WHERE` egyenlőségszűrő részeként adja meg a záradékban, a lekérdezés automatikusan csak a megfelelő partíciókra szűr.

## <a name="examples"></a>Példák

A következő lekérdezés olyan `id` elemeket kér, amelyek olyan tulajdonságot tartalmaznak, amelynek `AndersenFamily`értéke . Nem tartalmaz minden olyan elemet, `id` amely nem rendelkezik `AndersenFamily`tulajdonsággal, vagy amelynek értéke nem egyezik meg.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmény a következő:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Skaláris kifejezések a WHERE záradékban

Az előző példa egy egyszerű egyenlőségi lekérdezést mutatott. Az SQL API különböző [skaláris kifejezéseket](sql-query-scalar-expressions.md)is támogat. A leggyakrabban használt bináris és unary kifejezések. A forrás JSON-objektumból származó tulajdonsághivatkozások szintén érvényes kifejezések.

A következő támogatott bináris operátorokat használhatja:  

|**Operátor típusa**  | **Értékek** |
|---------|---------|
|Számtani | +,-,*,/,% |
|Bitenkénti    | \|, &, ^, <<, >>, >>> (nulla kitöltéses jobb eltolás) |
|Logikai    | ÉS, VAGY, NEM      |
|Összehasonlítás | =, &lt;!=, &gt; &lt;, &gt;=, =,  <> |
|Sztring     |  \|\|(összefűzés) |

A következő lekérdezések bináris operátorokat használnak:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

A +,-, ~, és a NOT operátorokat is használhatja a lekérdezésekben, ahogy az a következő példákban látható:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

A lekérdezésekben tulajdonsághivatkozásokat is használhat. Például `SELECT * FROM Families f WHERE f.isRegistered` a tulajdonságot `isRegistered` tartalmazó JSON elemet adja `true`eredményül, amelynek értéke megegyezik a értékével. Bármely más érték, `false` `null`például , `<object>`, `<array>` `Undefined` `<number>`, , `<string>`, , , , vagy , kizárja a cikket az eredményből.

## <a name="next-steps"></a>További lépések

- [Kezdetekhez](sql-query-getting-started.md)
- [IN kulcsszó](sql-query-keywords.md#in)
- [FROM záradék](sql-query-from.md)