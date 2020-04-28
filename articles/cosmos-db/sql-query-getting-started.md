---
title: Az SQL-lekérdezések első lépései a Azure Cosmos DBban
description: Ismerje meg, hogyan lehet SQL-lekérdezéseket lekérdezni a Azure Cosmos DBról. A mintaadatok feltölthetők egy Azure Cosmos DB tárolóba, és lekérdezheti azt.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74873335"
---
# <a name="getting-started-with-sql-queries"></a>SQL-lekérdezések – első lépések

Azure Cosmos DB SQL API-fiókok az Structured Query Language (SQL) használatával JSON-lekérdezési nyelvet használó elemek lekérdezését támogatják. A Azure Cosmos DB lekérdezési nyelv tervezési céljai a következők:

* Támogassa az SQLot, amely az egyik legismertebb és legnépszerűbb lekérdezési nyelv, és nem egy új lekérdezési nyelvet talál. Az SQL egy formális programozási modellt biztosít a JSON-elemek részletes lekérdezéséhez.  

* A JavaScript programozási modelljét a lekérdezési nyelv alapjaként használhatja. A JavaScript típusú rendszer, a kifejezés kiértékelése és a függvény meghívása az SQL API gyökerei. Ezek a gyökerek természetes programozási modellt biztosítanak olyan funkciókhoz, mint például a kapcsolatok kivetítése, a hierarchikus Navigálás a JSON-elemek, az önillesztések, a térbeli lekérdezések és a teljes mértékben JavaScriptben írt felhasználói függvények meghívása (UDF) számára.

## <a name="upload-sample-data"></a>Mintaadatok feltöltése

Az SQL API Cosmos DB fiókjában hozzon létre egy nevű `Families`tárolót. Hozzon létre két egyszerű JSON-elemet a tárolóban. Az Azure Cosmos DB lekérdezési docs legtöbb lekérdezését futtathatja ezen adathalmaz használatával.

### <a name="create-json-items"></a>JSON-elemek létrehozása

A következő kód két egyszerű JSON-elemet hoz létre a családokról. Az Andersen és a Wakefield család egyszerű JSON-elemei közé tartoznak a szülők, a gyermekek és a hozzájuk tartozó háziállatok, a címek és a regisztrációs adatok. Az első elem sztringeket, számokat, logikai elemeket, tömböket és beágyazott tulajdonságokat tartalmaz.


```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

A második tétel a `givenName` és `familyName` a helyett `firstName` a `lastName`és a értéket használja.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>A JSON-elemek lekérdezése

Próbáljon ki néhány lekérdezést a JSON-adaton, hogy megértse a Azure Cosmos DB SQL-lekérdezési nyelvének főbb szempontjait.

A következő lekérdezés azokat az elemeket adja vissza `id` , amelyekben a mező megfelel `AndersenFamily`. Mivel ez egy `SELECT *` lekérdezés, a lekérdezés kimenete a teljes JSON-elem. A SELECT szintaxissal kapcsolatos további információkért lásd: [SELECT utasítás](sql-query-select.md). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

A lekérdezés eredményei a következők: 

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

A következő lekérdezés újraformázza a JSON-kimenetet egy másik alakzatba. A lekérdezés egy új JSON `Family` -objektumot választ ki két kiválasztott mezővel, `Name` és `City`ha a városi város megegyezik az állapottal. "NY, NY" megfelel ebben az esetben.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

A lekérdezés eredményei a következők:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

A következő lekérdezés az összes olyan gyermek nevét adja vissza a családban, `id` amelyek `WakefieldFamily`egyezéseit város szerint rendezi.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Az eredmény a következő:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Megjegyzések

Az előző példák a Cosmos DB lekérdezési nyelvének számos aspektusát mutatják be:  

* Mivel az SQL API JSON-értékeken működik, sorok és oszlopok helyett faszerkezetű entitásokkal foglalkozik. A facsomópontok tetszőleges részletességgel, `Node1.Node2.Node3…..Nodem`például az ANSI SQL- `<table>.<column>` ben lévő kétrészes hivatkozáshoz hasonlóan is megtekinthetők.

* Mivel a lekérdezési nyelv a séma nélküli adatmennyiséggel működik, a típusrendszer számára dinamikusan kell kötni. Ugyanez a kifejezés különböző típusokat eredményezhet különböző elemeken. A lekérdezés eredménye egy érvényes JSON-érték, de nem garantált, hogy rögzített séma legyen.  

* A Azure Cosmos DB csak a szigorú JSON-elemeket támogatja. A Type rendszer és a kifejezések kizárólag JSON-típusokkal való kezelésre korlátozódnak. További információ: [JSON-specifikáció](https://www.json.org/).  

* A Cosmos-tároló a JSON-elemek séma nélküli gyűjteménye. A tároló elemein belüli és azok közötti kapcsolatok implicit módon vannak rögzítve az adattárolással, nem az elsődleges kulcs és a idegenkulcs-kapcsolatok esetében. Ez a funkció fontos a cikk későbbi részében tárgyalt elemekhez tartozó illesztések esetében.

## <a name="next-steps"></a>További lépések

- [Bevezetés a Azure Cosmos DBba](introduction.md)
- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT záradék](sql-query-select.md)
