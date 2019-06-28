---
title: Ismerkedés az Azure Cosmos DB SQL-lekérdezések
description: Bevezetés az SQL-lekérdezések használatába
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 87b275806c06443e37e9e92c35a38b4cde378322
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342566"
---
# <a name="getting-started-with-sql-queries"></a>Ismerkedés az SQL-lekérdezések

Azure Cosmos DB SQL API-fiókok lekérdezését elemek Structured Query Language (SQL) használatával, egy JSON-lekérdezési nyelvet támogatja. Az Azure Cosmos DB lekérdezési nyelv a a céljai a következők:

* Támogatja az SQL, ismerős és a népszerű lekérdezés helyett egy új lekérdezési nyelvre inventing nyelvek valamelyikével. SQL formális programozási modellt biztosít részletes lekérdezéseket a JSON-elemek keresztül.  

* JavaScript a programozási modellt használjuk alapját a lekérdezési nyelv. JavaScript típus system, kifejezés kiértékelése függvény meghívási jsou az SQL API gyökeréhez. Ezek gyökerek természetes programozási modell leképezések relációs, hierarchikus navigációs JSON-elemek között Önillesztések, térinformatikai lekérdezéseket, és hívja meg a felhasználó által definiált függvények (UDF), teljes mértékben javascriptben írt, funkciókat biztosítanak.

## <a name="upload-sample-data"></a>Mintaadatok feltöltése

Az SQL API Cosmos DB-fiók létrehozása egy nevű tárolóban `Families`. Két egyszerű JSON-elemek létrehozása a tárolóban. Az Azure Cosmos DB lekérdezési docs Ez az adatkészlet használatával is futtathatja a mintalekérdezések a legtöbb.

### <a name="create-json-items"></a>Hozzon létre JSON-elemek

Az alábbi kód létrehoz két egyszerű JSON-elemek családok kapcsolatban. Az Andersen és Wakefield családhoz egyszerű JSON-elemek közé tartozik a szüleinek, gyermekek és a kisállatok, cím és regisztrációs adatok. Az első elem van a karakterláncokat, számok, logikai értékek, tömbök és beágyazott tulajdonságok.


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

A második elemet használja `givenName` és `familyName` helyett `firstName` és `lastName`.

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

Próbálja ki néhány lekérdezést elleni megérteni a legfontosabb szempontok az Azure Cosmos DB SQL lekérdezési nyelv a JSON-adatokat.

Az alábbi lekérdezés azon elemeit adja vissza. Ha a `id` egyezések mezőben `AndersenFamily`. Mivel ez egy `SELECT *` lekérdezést, a lekérdezés kimenete a teljes JSON-elem. SELECT szintaxissal kapcsolatos további információkért lásd: [SELECT utasítás](sql-query-select.md). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

A lekérdezés eredményeit a következők: 

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

A következő lekérdezés újraformázza a JSON-kimenet egy másik formázásához. A lekérdezés egy új JSON-projektek `Family` objektumot két kiválasztott mezőkkel `Name` és `City`, ha a cím, város ugyanaz, mint az állapot. "NY, NY" Ebben az esetben megegyezik.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

A lekérdezés eredményeit a következők:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

A következő lekérdezés a család adja vissza a megadott nevek a gyermekek amelynek `id` megegyezik `WakefieldFamily`, város szerint rendezett.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Az eredmények a következők:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Megjegyzések

Az előző példák bemutatják a Cosmos DB lekérdezési nyelv számos aspektusát:  

* Mivel az SQL API a JSON-értékeit működik, entitások fa alakú sorok és oszlopok helyett foglalkozik. Olvassa el a fa csomópontjai bármilyen tetszőleges mélységben például `Node1.Node2.Node3…..Nodem`, hasonlóan ahhoz, hogy a két részből hivatkozását `<table>.<column>` ANSI SQL-ben.

* A lekérdezési nyelv a séma nélküli adatokkal dolgozik, mert a típusa rendszer dinamikusan kell kötni. Egyazon kifejezésre sikerült eddig is számtalan előnyét eltérő konfigurációs elemeket a különböző típusú. A lekérdezés eredménye egy érvényes JSON-értéket, de nem valószínű, hogy a rögzített sémát.  

* Az Azure Cosmos DB támogatja a szigorú JSON-elemek csak. A rendszer típusa és -kifejezések korlátozódnak JSON típusú foglalkozik. További információkért lásd: a [JSON-specifikáció](https://www.json.org/).  

* A Cosmos DB-tárolók gyűjteménye sémamentes JSON-elemek. Belül és azok tároló elemek között kapcsolatok implicit módon nem az elsődleges kulcs-idegen kulcs kapcsolatokat által rögzített, tartalmazási. Ez a funkció fontos a cikkben később tárgyalt intra-cikk illesztéseket.

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB bemutatása](introduction.md)
- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmosdb-dotnet)
- [SELECT záradék](sql-query-select.md)
