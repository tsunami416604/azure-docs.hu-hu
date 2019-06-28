---
title: -Tömbök és az Azure Cosmos DB objektumok használata
description: További információ az Azure Cosmos DB tömb- és létrehozása SQL-szintaxis.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 338f3b51edf38d20a963992e121b7e2dbd0c6873
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342781"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>-Tömbök és az Azure Cosmos DB objektumok használata

Az Azure Cosmos DB SQL API egyik legfőbb jellemzője a tömb- és létrehozása.

## <a name="arrays"></a>tömbök

Tömbök, hozhatnak létre, az alábbi példában látható módon:

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Az eredmények a következők:

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

Is használhatja a [TÖMBÖT megadó kifejezést](sql-query-subquery.md#array-expression) létrehozására a tömb [segédlekérdezés](sql-query-subquery.md) eredményeket. A lekérdezés lekérdezi a distinct megadott nevek a gyermekek tömbben.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>Iteráció

Az SQL API támogatja a léptetés át, JSON-tömbök és a egy új szerkezet hozzáadva a [KULCSSZÓT](sql-query-keywords.md#in) a FROM forrás. Az alábbi példában:

```sql
    SELECT *
    FROM Families.children
```

Az eredmények a következők:

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

A következő lekérdezés végrehajtja az iteráció `children` a a `Families` tároló. A kimeneti tömbben eltér az előző lekérdezés. Ebben a példában bontja `children`, és simítja egybe az eredményeket egy egyetlen olyan tömböt be:  

```sql
    SELECT *
    FROM c IN Families.children
```

Az eredmények a következők:

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

Szűrheti a tömb minden egyes bejegyzés a további, az alábbi példában látható módon:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Az eredmények a következők:

```json
    [{
      "givenName": "Lisa"
    }]
```

Egy tömb iteráció eredményét feletti is lehet összesíteni. A következő lekérdezés például megszámlálja a gyermekek többek között az összes olyan családot:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Az eredmények a következők:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>További lépések

- [Bevezetés](sql-query-getting-started.md)
- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Illesztés](sql-query-join.md)