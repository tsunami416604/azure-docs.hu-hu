---
title: SQL-kulcsszavak az Azure Cosmos DB-hez
description: Ismerje meg az Azure Cosmos DB SQL-kulcsszavait.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: f2da2695ec20eac9dd2636104d3314427e60d541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498541"
---
# <a name="keywords-in-azure-cosmos-db"></a>Kulcsszavak az Azure Cosmos DB-ben

Ez a cikk az Azure Cosmos DB SQL-lekérdezésekben használható kulcsszavakat részletezi.

## <a name="between"></a>BETWEEN

A `BETWEEN` kulcsszó segítségével karakterlánc- vagy numerikus értékek tartományaialapján fejezhet ki lekérdezéseket. A következő lekérdezés például minden olyan elemet visszaad, amelyben az első gyermek osztályzata 1-5, beleértve a értéket is.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

A kulcsszót `BETWEEN` a `SELECT` záradékban is használhatja, ahogy az a következő példában is.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Az SQL API-ban az ANSI SQL-től eltérően tartománylekérdezéseket adhat meg a vegyes típusok tulajdonságaival szemben. Lehet például egy szám, mint `grade` `5` egyes elemekben, és egy karakterlánc, mint `grade4` a többiben. Ezekben az esetekben, mint a JavaScript, a `Undefined`két különböző típusú összehasonlítása eredményez, így az elem kimarad.

> [!TIP]
> A lekérdezések gyorsabb végrehajtási ideje érdekében hozzon létre egy indexelési házirendet, amely tartományindex-típust használ a `BETWEEN` záradék által szűrt numerikus tulajdonságokkal vagy elérési utakkal szemben.

## <a name="distinct"></a>DISTINCT

A `DISTINCT` kulcsszó kiküszöböli az ismétlődéseket a lekérdezés kivetítésében.

Ebben a példában a lekérdezés minden vezetéknévhez kivetíti az értékeket:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Az eredmény a következő:

```json
[
    "Andersen"
]
```

Egyedi objektumokat is kivetíthet. Ebben az esetben a LastName mező nem létezik a két dokumentum egyikében, így a lekérdezés üres objektumot ad vissza.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Az eredmény a következő:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

A DISTINCT segédlekérdezésen belüli vetületben is használható:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Ez a lekérdezés kivetít egy tömböt, amely tartalmazza az egyes gyermek givenName az ismétlődések eltávolítása. Ezt a tömböt gyermeknévként aliasálod, és a külső lekérdezésvetíti.

Az eredmény a következő:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

Az összesítő rendszerfüggvénnyel és `DISTINCT` segédlekérdezéssel rendelkező lekérdezések nem támogatottak. A következő lekérdezés például nem támogatott:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

Az IN kulcsszó segítségével ellenőrizheti, hogy egy adott érték megfelel-e a lista bármely értékének. A következő lekérdezés például az összes `id` `WakefieldFamily` olyan `AndersenFamily`családi elemet visszaadja, ahol a vagy a .

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

A következő példa minden olyan elemet visszaad, ahol az állapot a megadott értékek bármelyike:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

Az SQL API támogatja a [JSON-tömböken keresztüli iterációt,](sql-query-object-array.md#Iteration)egy új konstrukcióval, amelyet a FROM forrásban lévő kulcsszó on keresztül ad hozzá.

Ha a partíciókulcsot is `IN` beilleszti a szűrőbe, a lekérdezés automatikusan csak a megfelelő partíciókra szűr.

## <a name="top"></a>Top

A TOP kulcsszó `N` a lekérdezési eredmények első számát adja meg határozatlan sorrendben. Ajánlott eljárásként használja a `ORDER BY` TOP-ot a záradékkal az eredmények első `N` számú rendezett értékre való korlátozásához. E két záradék kombinálása az egyetlen módja annak, hogy kiszámíthatóan jelezze, hogy a TOP sorok mely hatással vannak.

A TOP-ot állandó értékkel használhatja, mint a következő példában, vagy egy változó értékkel paraméterezett lekérdezések használatával.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Az eredmény a következő:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>További lépések

- [Kezdetekhez](sql-query-getting-started.md)
- [Illesztések](sql-query-join.md)
- [Segédlekérdezések](sql-query-subquery.md)