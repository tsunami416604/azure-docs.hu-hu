---
title: Az Azure Cosmos DB WHERE záradék
description: Ismerje meg az Azure Cosmos DB SQL WHERE záradék
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 6a942e48ffea7785fe971cc2f8fa66e8569ed672
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342810"
---
# <a name="where-clause"></a>WHERE záradék

A választható WHERE záradék (`WHERE <filter_condition>`) feltétel(ek) megadja, hogy a forrás JSON-elemek eredmények tartalmazzák a lekérdezés meg kell felelniük. Egy JSON-elem ki kell értékelnie, hogy a megadott feltételeket `true` figyelembe kell venni az eredményt. Az index réteg használja a legkisebb részhalmazát forrás elemek, amelyek az eredmény része lehet meghatározni a WHERE záradékban.
  
## <a name="syntax"></a>Szintaxis
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumentumok

- `<filter_condition>`  
  
   Itt adhatja meg az állapotot, a dokumentumok vissza kell teljesülniük.  
  
- `<scalar_expression>`  
  
   A kifejezés a következő időpontban számítja értéket jelölő. Lásd: [skaláris kifejezések](sql-query-scalar-expressions.md) részleteiről.  
  

## <a name="remarks"></a>Megjegyzések
  
  Ahhoz, hogy a dokumentum egy kifejezést a megadott kifejezés adja vissza a feltétel igaz értéket kell adnia. Csak az IGAZ logikai értéket eleget tesz a feltételt, semmilyen más érték: nem meghatározott, NULL értékű, false, szám, tömböt vagy objektumot nem teljesítik a feltételt. 

## <a name="examples"></a>Példák

Az alábbi lekérdezés kérelmek elemek, amelyek tartalmaznak egy `id` tulajdonsága, amelynek az értéke `AndersenFamily`. Nem tartalmazza a minden olyan cikk, amely nem rendelkezik egy `id` tulajdonság, vagy amelynek az értéke nem egyezik `AndersenFamily`.

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

### <a name="scalar-expressions-in-the-where-clause"></a>A WHERE záradékban skaláris kifejezések

Az előző példából kiderült, egy egyszerű egyenlőség lekérdezést. Az SQL API-t is támogatja a különböző [skaláris kifejezések](sql-query-scalar-expressions.md). A leggyakrabban használt olyan bináris és egyoperandusú kifejezés. A forrás JSON-objektumból tulajdonság hivatkozásokat akkor is érvényes kifejezések.

A következő támogatott bináris operátorok használhatók:  

|**Művelettípus**  | **Értékek** |
|---------|---------|
|Aritmetikai | +,-,*,/,% |
|Bitenkénti    | \|, &, ^, <<>>,, >>> (nulla ki jobbra tolást) |
|Logikai    | ÉS, VAGY SEM      |
|Összehasonlítás | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Karakterlánc     |  \|\| (fűzze össze) |

A következő lekérdezéseket a bináris operátor használható:

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

Is használhatja az egyoperandusú operátorokat +,-, ~, és nem a lekérdezéseket, a következő példákban szemléltetett módon:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Használhatja a tulajdonság hivatkozások a lekérdezésekben. Ha például `SELECT * FROM Families f WHERE f.isRegistered` tulajdonságot tartalmazó JSON-elemét adja vissza `isRegistered` értékkel egyenlőnek `true`. Bármely más érték például `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`, vagy `<array>`, nem tartalmazza a cikk az eredményből. 

## <a name="next-steps"></a>További lépések

- [Bevezetés](sql-query-getting-started.md)
- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmosdb-dotnet)
- [FROM záradékban](sql-query-from.md)