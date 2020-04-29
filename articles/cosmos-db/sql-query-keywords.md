---
title: Azure Cosmos DB SQL-kulcsszavai
description: A Azure Cosmos DB SQL-kulcsszavainak megismerése.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: tisande
ms.openlocfilehash: 069548b9b69ef6f7f6bde85ede830d97f3d312db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81261567"
---
# <a name="keywords-in-azure-cosmos-db"></a>Azure Cosmos DB kulcsszavai

Ez a cikk a Azure Cosmos DB SQL-lekérdezésekben felhasználható kulcsszavakat részletezi.

## <a name="between"></a>BETWEEN

A kulcsszó használatával a `BETWEEN` lekérdezéseket karakterláncok vagy numerikus értékek tartományán keresztül fejezheti ki. Például a következő lekérdezés az összes olyan elemet adja vissza, amelyben az első gyermek 1-5-as osztálya.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

A `BETWEEN` kulcsszót a `SELECT` záradékban is használhatja, ahogy az alábbi példában is látható.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Az SQL API-ban – az ANSI SQL-től eltérően – különböző típusú lekérdezéseket adhat meg a vegyes típusok tulajdonságainál. Előfordulhat például, `grade` hogy az egyes elemekhez `5` és a másokhoz hasonló `grade4` sztringek száma. Ezekben az esetekben, ahogy a JavaScriptben, a két különböző típus összehasonlítása eredményezi `Undefined`, így az elem kimarad.

> [!TIP]
> A lekérdezések gyorsabb végrehajtásához hozzon létre egy olyan indexelési házirendet, amely a tartomány indexelési típusát használja a `BETWEEN` záradék szűrői által használt numerikus tulajdonságok vagy elérési utak alapján.

## <a name="distinct"></a>DISTINCT

A `DISTINCT` kulcsszó kiküszöböli az ismétlődéseket a lekérdezés leképezésében.

Ebben a példában a lekérdezési projektek értékei az egyes vezetéknevek esetében:

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

Emellett egyedi objektumokat is létrehozhat. Ebben az esetben a lastName mező nem létezik a két dokumentum egyikében sem, így a lekérdezés üres objektumot ad vissza.

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

A DISTINCT a segédlekérdezés egy allekérdezésen belüli kivetítésében is használható:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Ez a lekérdezés egy tömböt hoz létre, amely tartalmazza az egyes gyermekekhez tartozó givenName, amelyekben duplikált elemek törlődnek. Ez a tömb ChildNames, és a külső lekérdezésben is szerepel.

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

Az összesített rendszerfunkcióval és allekérdezéssel rendelkező `DISTINCT` lekérdezések nem támogatottak. Például a következő lekérdezés nem támogatott:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

A IN kulcsszó használatával megvizsgálhatja, hogy egy adott érték egyezik-e egy lista bármely értékével. Például a következő lekérdezés az összes olyan `id` `WakefieldFamily` családi elemet adja vissza, ahol a `AndersenFamily`vagy a.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

A következő példa azokat az elemeket adja vissza, amelyekben az állapot a megadott értékek bármelyike:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

Az SQL API támogatást nyújt a [JSON-tömbök megismétléséhez](sql-query-object-array.md#Iteration), és egy új, a from forrásban található kulcsszóval hozzáadott összeállítást biztosít.

Ha a `IN` szűrőben tartalmazza a partíciós kulcsot, a lekérdezés automatikusan csak a megfelelő partíciókat fogja szűrni.

## <a name="top"></a>TOP

A felső kulcsszó a lekérdezés eredményeinek első `N` számát adja vissza nem definiált sorrendben. Az ajánlott eljárás a TOP és a `ORDER BY` záradék használata az eredményeknek az első `N` számú rendezett értékre való korlátozásához. A két záradék összevonása az egyetlen módszer arra, hogy előre jelezze, hogy mely sorok érintik a LEGFONTOSABBat.

A TOP értéket állandó értékkel használhatja, ahogy az alábbi példában, vagy egy változó értékkel, amely paraméteres lekérdezéseket használ.

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

- [Első lépések](sql-query-getting-started.md)
- [Illesztések](sql-query-join.md)
- [Allekérdezéseket](sql-query-subquery.md)