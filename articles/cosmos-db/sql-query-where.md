---
title: WHERE záradék Azure Cosmos DB
description: Tudnivalók a Azure Cosmos DB SQL WHERE záradékáról
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 483a0533eafc81ef8698d260a753062ae074f6d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78898774"
---
# <a name="where-clause-in-azure-cosmos-db"></a>WHERE záradék Azure Cosmos DB

A választható WHERE záradék (`WHERE <filter_condition>`) olyan feltételt határoz meg, amelynek a forrás JSON-elemeinek meg kell felelniük ahhoz, hogy a lekérdezés tartalmazza azokat az eredmények között. A JSON-elemek kiértékeléséhez a megadott `true` feltételeket kell figyelembe venni az eredmény szempontjából. Az index réteg a WHERE záradék használatával határozza meg az eredmény részét képező forrásoldali elemek legkisebb részhalmazát.
  
## <a name="syntax"></a>Szintaxis
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumentumok

- `<filter_condition>`  
  
   Meghatározza a visszaadott dokumentumok számára teljesítendő feltételt.  
  
- `<scalar_expression>`  
  
   A kiszámítani kívánt értéket jelölő kifejezés. Részletekért lásd a [skaláris kifejezéseket](sql-query-scalar-expressions.md) .  
  
## <a name="remarks"></a>Megjegyzések
  
  Ahhoz, hogy a dokumentum visszaadja a szűrési feltételnek megadott kifejezést, az igaz értéket kell kiértékelni. Csak a logikai `true` érték felel meg a feltételnek, bármely más érték: nem definiált, null, hamis, szám, tömb vagy objektum nem felel meg a feltételnek.

  Ha egy egyenlőségi szűrő részeként belefoglalja a partíciós kulcsot a `WHERE` záradékba, a lekérdezés automatikusan csak a megfelelő partíciókat fogja szűrni.

## <a name="examples"></a>Példák

A következő lekérdezés olyan `id` elemeket kér, amelyek értéke a tulajdonság. `AndersenFamily` Kizár minden olyan olyan tételt, amely nem rendelkezik `id` tulajdonsággal, vagy amelynek értéke nem `AndersenFamily`egyezik.

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

Az előző példában egy egyszerű egyenlőségi lekérdezés látható. Az SQL API különböző [skaláris kifejezéseket](sql-query-scalar-expressions.md)is támogat. A leggyakrabban használt bináris és egyoperandusú kifejezések. A forrás JSON-objektumra mutató tulajdonságok szintén érvényes kifejezések.

A következő támogatott bináris operátorok használhatók:  

|**Operátor típusa**  | **Értékek** |
|---------|---------|
|Aritmetikai | +,-,*,/,% |
|Bitenkénti    | \|, &, ^,  <<,  >>,  >>>  (nulla kitöltés jobb eltolása) |
|Logikai    | ÉS, VAGY NEM      |
|Összehasonlítás | =,! =, &lt;, &gt;, &lt;=, &gt;=,  <> |
|Sztring     |  \|\|CONCATENATE |

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

A következő példákban látható módon használhatja az unáris operátorok +,-, ~, és nem a lekérdezésekben is:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

A lekérdezésekben tulajdonságok hivatkozásait is használhatja. Például `SELECT * FROM Families f WHERE f.isRegistered` visszaadja a tulajdonságot `isRegistered` tartalmazó olyan JSON- `true`tételt, amelynek értéke egyenlő. Bármely más érték `false`, például `null` `Undefined` `<number>` `<string>` `<object>`,,,,, vagy `<array>`, kizárja az elemet az eredményből.

## <a name="next-steps"></a>További lépések

- [Első lépések](sql-query-getting-started.md)
- [A kulcsszóban](sql-query-keywords.md#in)
- [FROM záradék](sql-query-from.md)