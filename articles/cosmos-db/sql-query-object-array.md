---
title: Tömbök és objektumok használata az Azure Cosmos DB-ben
description: Ismerje meg az SQL szintaxist tömbök és objektumok létrehozásához az Azure Cosmos DB-ben. Ez a cikk néhány példát is tartalmaz a tömbobjektumokon végzett műveletek végrehajtására
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 5b2801b0a71f04803955e9d8bc18a97133019996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246551"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Tömbök és objektumok használata az Azure Cosmos DB-ben

Az Azure Cosmos DB SQL API egyik legfontosabb jellemzője a tömb- és objektumlétrehozás.

## <a name="arrays"></a>Tömbök

Tömböket hozhat létre, ahogy az a következő példában látható:

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Az eredmény a következő:

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

A [TÖMB kifejezéssel](sql-query-subquery.md#array-expression) a [segédlekérdezés](sql-query-subquery.md) eredményeiből is létrehozhatunk tömböt. Ez a lekérdezés lekérdezi a tömbben lévő gyermekek összes különböző megadott nevét.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a name="iteration"></a><a id="Iteration"></a>Iteráció

Az SQL API támogatja a JSON-tömböken keresztüli iterációt, és egy új konstrukciót ad hozzá a FROM [forrás IN kulcsszaván](sql-query-keywords.md#in) keresztül. A következő példában:

```sql
    SELECT *
    FROM Families.children
```

Az eredmény a következő:

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

A következő lekérdezés iterációt `Families` hajt végre a `children` tárolóban. A kimeneti tömb eltér az előző lekérdezéstől. Ez a `children`példa felosztja az eredményeket, és egyetlen tömbbe olvasztja az eredményeket:  

```sql
    SELECT *
    FROM c IN Families.children
```

Az eredmény a következő:

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

A tömb minden egyes bejegyzésére további szűrést tehet, ahogy az a következő példában látható:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Az eredmény a következő:

```json
    [{
      "givenName": "Lisa"
    }]
```

A tömbitítás eredményét is összesítheti. A következő lekérdezés például megszámolja a gyermekek számát az összes család között:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Az eredmény a következő:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>További lépések

- [Kezdetekhez](sql-query-getting-started.md)
- [Az Azure Cosmos DB .NET-mintái](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Illesztések](sql-query-join.md)