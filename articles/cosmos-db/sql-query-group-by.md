---
title: GROUP BY záradék az Azure Cosmos DB-ben
description: Ismerje meg az Azure Cosmos DB GROUP BY záradékát.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tisande
ms.openlocfilehash: e41e81457421bfe27e3c0313fc06e39e6df4cdce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73819107"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>GROUP BY záradék az Azure Cosmos DB-ben

A GROUP BY záradék egy vagy több megadott tulajdonság értéke szerint osztja fel a lekérdezés eredményeit.

> [!NOTE]
> Az Azure Cosmos DB jelenleg támogatja a GROUP BY-t a .NET SDK 3.3-as és újabb verzióiban, valamint a JavaScript SDK 3.4-es és újabb verzióit.
> Más nyelvi SDK-k támogatása jelenleg nem érhető el, de a tervek szerint.

## <a name="syntax"></a>Szintaxis

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Argumentumok

- `<scalar_expression_list>`

   Megadja a lekérdezés eredményének felosztásához használt kifejezéseket.

- `<scalar_expression>`
  
   Minden skaláris kifejezés engedélyezett, kivéve a skaláris allekérdezéseket és a skaláris aggregátumokat. Minden skaláris kifejezésnek legalább egy tulajdonsághivatkozást tartalmaznia kell. Az egyes kifejezések száma vagy az egyes kifejezések számossága nincs korlátozva.

## <a name="remarks"></a>Megjegyzések
  
  Ha egy lekérdezés GROUP BY záradékot használ, a SELECT záradék csak a GROUP BY záradékban szereplő tulajdonságok és rendszerfüggvények részhalmazát tartalmazhatja. Az egyik kivétel az [összesítő rendszerfüggvény,](sql-query-aggregates.md)amely a GROUP BY záradékban való szerepeltetés nélkül jelenhet meg a SELECT záradékban. A SELECT záradékban mindig szerepelhet literális értékek is.

  A GROUP BY záradéknak a SELECT, FROM és WHERE záradék után, az OFFSET LIMIT záradék előtt kell lennie. Jelenleg nem használhatja a GROUP BY parancsot ORDER BY záradékkal, de ez a terv.

  A GROUP BY záradék nem teszi lehetővé a következők egyikét sem:
  
- Aliasing tulajdonságok vagy aliasing rendszerfüggvények (aliasing továbbra is engedélyezett a SELECT záradékban)
- Segédlekérdezések
- Összesítési rendszerfüggvények (ezek csak a SELECT záradékban engedélyezettek)

## <a name="examples"></a>Példák

Ezek a példák az [Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo)szolgáltatáson keresztül elérhető tápérték-adatkészletet használják.

Például az itt egy lekérdezés, amely az egyes élelmiszercsoportokban lévő elemek teljes számát adja vissza:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Néhány eredmény (TOP kulcsszó segítségével korlátozza az eredményeket):

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

Ez a lekérdezés két kifejezést használ az eredmények felosztására:

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

Ennek a lekérdezésnek van egy rendszerfüggvénye a GROUP BY záradékban:

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

Ez a lekérdezés kulcsszavakat és rendszerfüggvényeket is használ az elemtulajdonság-kifejezésben:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Az eredmény a következő:

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

## <a name="next-steps"></a>További lépések

- [Kezdetekhez](sql-query-getting-started.md)
- [SELECT záradék](sql-query-select.md)
- [Összesítő függvények](sql-query-aggregates.md)
