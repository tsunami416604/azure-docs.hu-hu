---
title: WHERE záradék Azure Cosmos DB
description: Tudnivalók a Azure Cosmos DB SQL WHERE záradékáról
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: cd5643d8be06afcd43c5bfe38d6f5e9caa6f906e
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326644"
---
# <a name="where-clause-in-azure-cosmos-db"></a>WHERE záradék Azure Cosmos DB

A választható WHERE záradék (`WHERE <filter_condition>`) olyan feltételeket határoz meg, amelyeknek a forrás JSON-elemeinek meg kell felelniük ahhoz, hogy a lekérdezés tartalmazza azokat az eredmények között. A JSON-elemek kiértékeléséhez meg kell vizsgálni a megadott feltételeket, hogy figyelembe lehessen venni az eredményhez `true`. Az index réteg a WHERE záradék használatával határozza meg az eredmény részét képező forrásoldali elemek legkisebb részhalmazát.
  
## <a name="syntax"></a>Szintaxis
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumentumok

- `<filter_condition>`  
  
   Itt adhatja meg az állapotot, a dokumentumok vissza kell teljesülniük.  
  
- `<scalar_expression>`  
  
   A kifejezés a következő időpontban számítja értéket jelölő. Részletekért lásd a [skaláris kifejezéseket](sql-query-scalar-expressions.md) .  
  

## <a name="remarks"></a>Megjegyzések
  
  Ahhoz, hogy a dokumentum egy kifejezést a megadott kifejezés adja vissza a feltétel igaz értéket kell adnia. Csak az IGAZ logikai értéket eleget tesz a feltételt, semmilyen más érték: nem meghatározott, NULL értékű, false, szám, tömböt vagy objektumot nem teljesítik a feltételt. 

## <a name="examples"></a>Példák

A következő lekérdezés olyan elemeket kér, amelyek `id` tulajdonságot tartalmaznak, amelynek értéke `AndersenFamily`. Kizár minden olyan olyan tételt, amely nem rendelkezik `id` tulajdonsággal, vagy amelynek értéke nem egyezik `AndersenFamily`.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmények a következők:

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

Az előző példából kiderült, egy egyszerű egyenlőség lekérdezést. Az SQL API különböző [skaláris kifejezéseket](sql-query-scalar-expressions.md)is támogat. A leggyakrabban használt olyan bináris és egyoperandusú kifejezés. A forrás JSON-objektumból tulajdonság hivatkozásokat akkor is érvényes kifejezések.

A következő támogatott bináris operátorok használhatók:  

|**Művelettípus**  | **Értékek** |
|---------|---------|
|Aritmetikai | +,-,*,/,% |
|Bitenkénti    | \|, &, ^, <<>>,, >>> (nulla ki jobbra tolást) |
|Logikai    | ÉS, VAGY SEM      |
|Összehasonlítás | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Sztring     |  \|\| (fűzze össze) |

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

A lekérdezésekben tulajdonságok hivatkozásait is használhatja. A `SELECT * FROM Families f WHERE f.isRegistered` például a tulajdonságot tartalmazó JSON-elemeket adja vissza, `isRegistered` értékkel egyenlő `true`. Bármely más érték, például `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`vagy `<array>`, kizárja az elemet az eredményből. 

## <a name="next-steps"></a>Következő lépések

- [Bevezetés](sql-query-getting-started.md)
- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [FROM záradékban](sql-query-from.md)