---
title: GROUP BY záradék Azure Cosmos DB
description: A Azure Cosmos DB GROUP BY záradékának megismerése.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tisande
ms.openlocfilehash: d92e24836a0eb5757de9bbdb516be290456deb7f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333260"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>GROUP BY záradék Azure Cosmos DB

A GROUP BY záradék a lekérdezés eredményét egy vagy több megadott tulajdonság értékei alapján osztja el.

> [!NOTE]
> A Azure Cosmos DB jelenleg a [.net SDK 3,3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.3.0) -as vagy újabb verziójában támogatja a csoportosítást.
> A más nyelvi SDK-k és az Azure Portal támogatása jelenleg nem érhető el, de tervezték.

## <a name="syntax"></a>Szintaxis

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Argumentumok

- `<scalar_expression_list>`

   Meghatározza azokat a kifejezéseket, amelyeket a rendszer a lekérdezési eredmények felosztásához használ majd.

- `<scalar_expression>`
  
   Minden skaláris kifejezés engedélyezett a skaláris allekérdezések és a skaláris összesítések kivételével. Minden skaláris kifejezésnek tartalmaznia kell legalább egy tulajdonság-referenciát. Nem korlátozható az egyes kifejezések száma, illetve a kifejezések kardinálisa.

## <a name="remarks"></a>Megjegyzések
  
  Ha egy lekérdezés GROUP BY záradékot használ, a SELECT záradék csak a GROUP BY záradékban szereplő tulajdonságok és rendszerfüggvények részhalmazát tartalmazza. Az egyetlen kivétel a [rendszerfüggvények összesítése](sql-query-aggregates.md), amely a Select záradékban megjelenhet a Group By záradékba való felvétel nélkül. A SELECT záradékban mindig szerepelhetnek literál értékeket is.

  A GROUP BY záradéknak a SELECT, a FROM, a WHERE záradék és az ELTOLÁSi korlát záradéka után kell szerepelnie. A GROUP BY utasítás nem használható ORDER BY záradékkal, de ez tervezett.

  A GROUP BY záradék nem engedélyezi a következők egyikét sem:
  
- Alias-tulajdonságok vagy aliasrendszer-függvények (a SELECT záradékban továbbra is engedélyezett az alias)
- Allekérdezéseket
- Összesítő rendszerfunkciók (ezek csak a SELECT záradékban engedélyezettek)

## <a name="examples"></a>Példák

Ezek a példák a [Azure Cosmos DB lekérdezési demókörnyezet](https://www.documentdb.com/sql/demo)által elérhető táplálkozási adatkészletet használják.

Például itt egy olyan lekérdezés, amely az egyes foodGroup lévő elemek teljes számát adja vissza:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Néhány eredmény (a leggyakoribb kulcsszó az eredmények korlátozására szolgál):

```json
[{
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "foodGroup": "Meals, Entrees, and Side Dishes",
  "foodGroupCount": 113
},
{
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

Ez a lekérdezés két kifejezést használ az eredmények felosztásához:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Néhány eredmény:

```json
[{
  "version": 1,
  "foodGroup": "Nut and Seed Products",
  "foodGroupCount": 133
},
{
  "version": 1,
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "version": 1,
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "version": 1,
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

A lekérdezés a GROUP BY záradékban található rendszerfüggvényt tartalmaz:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Néhány eredmény:

```json
[{
  "foodGroupCount": 371,
  "upperFoodGroup": "FAST FOODS"
},
{
  "foodGroupCount": 267,
  "upperFoodGroup": "FINFISH AND SHELLFISH PRODUCTS"
},
{
  "foodGroupCount": 389,
  "upperFoodGroup": "LEGUMES AND LEGUME PRODUCTS"
},
{
  "foodGroupCount": 113,
  "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
}]
```

Ez a lekérdezés a kulcsszavakat és a rendszerfüggvényeket is használja az Item tulajdonság kifejezésében:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Az eredmények a következők:

```json
[{
  "correctVersion": true,
  "containsOrangeTag": false,
  "foodGroupCount": 8608
},
{
  "correctVersion": true,
  "containsOrangeTag": true,
  "foodGroupCount": 10
}]
```

## <a name="next-steps"></a>Következő lépések

- [Első lépések](sql-query-getting-started.md)
- [SELECT záradék](sql-query-select.md)
- [Összesítő függvények](sql-query-aggregates.md)
