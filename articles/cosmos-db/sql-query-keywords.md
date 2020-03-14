---
title: Azure Cosmos DB SQL-kulcsszavai
description: A Azure Cosmos DB SQL-kulcsszavainak megismerése.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: 711e961bd5eb1607e2e6f11b0b5762423d78c0e7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246577"
---
# <a name="keywords-in-azure-cosmos-db"></a>Azure Cosmos DB kulcsszavai
Ez a cikk a Azure Cosmos DB SQL-lekérdezésekben felhasználható kulcsszavakat részletezi.

## <a name="between"></a>KÖZÖTT

Ahogy az ANSI SQL esetében is, a közötti kulcsszó használatával a lekérdezéseket karakterláncok vagy numerikus értékek tartományán keresztül lehet kifejezni. Például a következő lekérdezés az összes olyan elemet adja vissza, amelyben az első gyermek 1-5-as osztálya.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Az ANSI SQL-től eltérően a FROM záradékban is használhatja a FROM záradék közötti záradékot, ahogy az az alábbi példában is látható.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Az SQL API-ban – az ANSI SQL-től eltérően – különböző típusú lekérdezéseket adhat meg a vegyes típusok tulajdonságainál. Előfordulhat például, hogy az `grade` egy szám, például `5` egyes elemekben, és egy karakterlánc, például `grade4` a többinél. Ezekben az esetekben, ahogy a JavaScriptben, a két különböző típus összehasonlítása `Undefined`eredményez, így az elem kimarad.

> [!TIP]
> A lekérdezések gyorsabb végrehajtásához hozzon létre egy olyan indexelési házirendet, amely egy tartomány-index típust használ bármely numerikus tulajdonság vagy elérési út alapján, amely a záradékok között szerepel.

## <a name="distinct"></a>KÜLÖNBÖZŐ

A DISTINCT kulcsszó kiküszöböli az ismétlődéseket a lekérdezés leképezésében.

Ebben a példában a lekérdezési projektek értékei az egyes vezetéknevek esetében:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Az eredmények a következők:

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

Az eredmények a következők:

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

Az eredmények a következők:

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

Az összesített rendszerfunkcióval rendelkező lekérdezések és a különálló allekérdezések nem támogatottak. Például a következő lekérdezés nem támogatott:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

A IN kulcsszó használatával megvizsgálhatja, hogy egy adott érték egyezik-e egy lista bármely értékével. Például a következő lekérdezés az összes olyan családi elemet adja vissza, amelyben a `id` `WakefieldFamily` vagy `AndersenFamily`.

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

A felső kulcsszó a lekérdezés eredményének első `N` számát adja vissza, meghatározatlan sorrendben. Ajánlott eljárásként használja az ORDER BY záradékot, hogy az eredményeket az első `N` a rendezett értékek számára korlátozza. A két záradék összevonása az egyetlen módszer arra, hogy előre jelezze, hogy mely sorok érintik a LEGFONTOSABBat.

A TOP értéket állandó értékkel használhatja, ahogy az alábbi példában, vagy egy változó értékkel, amely paraméteres lekérdezéseket használ.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Az eredmények a következők:

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

## <a name="next-steps"></a>Következő lépések

- [Első lépések](sql-query-getting-started.md)
- [Csatlakozik](sql-query-join.md)
- [Allekérdezéseket](sql-query-subquery.md)