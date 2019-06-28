---
title: Az Azure Cosmos DB SQL-kulcsszavak
description: Ismerje meg az SQL-kulcsszavak az Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: c9024f120e0a55162a1f6dba0cd9cbda97f5eebc
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342616"
---
# <a name="keywords-in-azure-cosmos-db"></a>Az Azure Cosmos DB kulcsszavak
Ez a cikk részletesen kulcsszavakat, amely felhasználható az Azure Cosmos DB SQL-lekérdezések.

## <a name="between"></a>KÖZÖTT

ANSI SQL, mint a BETWEEN kulcsszó használatával karakterlánc- vagy numerikus értékek tartományát lekérdezéseket express. Például a következő lekérdezést, amelyben az első alárendelt szintű 1-5, beleértve az összes elemet adja vissza.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Ellentétben az ANSI SQL-ben is használhatja a BETWEEN záradék a FROM záradék esetében az alábbi példában látható módon.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Az SQL API-t, ellentétben az ANSI SQL-lekérdezések vegyes típusú tulajdonságokhoz fejezhető. Például `grade` lehet egy szám hasonló `5` néhány elemet és a egy karakterlánc például `grade4` más. Ezekben az esetekben, JavaScript, mint a két különböző típusa közötti összehasonlítás eredménye `Undefined`, így az a cikk a rendszer kihagyta.

> [!TIP]
> Lekérdezés végrehajtása gyorsabb hozzon létre egy indexelési házirendet, amely minden olyan numerikus tulajdonságok vagy elérési utak a BETWEEN záradék szűri az index Tartománytípus használja.

## <a name="distinct"></a>DISTINCT

A DISTINCT kulcsszó használata esetén nem a lekérdezés leképezése az ismétlődéseket.

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Ebben a példában a lekérdezés-projektek, minden egyes Vezetéknév értékeit.

Az eredmények a következők:

```json
[
    "Andersen"
]
```

Kivetítheti az egyedi objektumot is. Ebben az esetben a lastName mező nem létezik a két dokumentumot, egy, a lekérdezés egy üres objektumot ad vissza.

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

A DISTINCT is használható a leképezésben allekérdezés belül:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Ez a lekérdezés-projektek, az ismétlődő eltávolítja minden gyermek givenName tartalmazó tömb. A tömb ChildNames aliasneve, és a külső lekérdezésben előre jelzett.

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
## <a name="in"></a> INDIA

Az IN kulcsszó használatával ellenőrizze, hogy egy megadott értéke megegyezik-e a lista bármely értéke. Például a következő lekérdezés az összes családi elemeket adja vissza, a `id` van `WakefieldFamily` vagy `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Az alábbi példa az összes elem visszaadása, ahol állapota a megadott értékeket:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

Az SQL API lehetővé teszi a támogatását [JSON-tömbök keresztül léptetés](sql-query-object-array.md#Iteration), egy új szerkezet a kulcsszó a FROM forrás hozzáadva a. 

## <a name="top"></a>TOP

A felső kulcsszó adja vissza az első `N` nem meghatározott sorrendben legyenek a lekérdezési eredmények száma. Az ajánlott eljárás használ a felső az ORDER BY záradékkal korlátozza az első eredményeket `N` rendezett értékek száma. Ezekkel a záradékokkal két kombinálásával az egyetlen módja lehetővé teszi a kiszámítható jelzi, amely sorok felső hatással van.

FELSŐ is használhatja, vagy egy változó értéke a paraméteres lekérdezések használatával egy állandó értékkel, az alábbi példában látható módon.

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

## <a name="next-steps"></a>További lépések

- [Bevezetés](sql-query-getting-started.md)
- [Illesztés](sql-query-join.md)
- [Segédlekérdezések](sql-query-subquery.md)