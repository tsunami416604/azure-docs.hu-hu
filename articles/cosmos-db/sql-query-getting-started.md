---
title: Sql-lekérdezések – első lépések az Azure Cosmos DB-ben
description: Ismerje meg, hogyan sql-lekérdezések segítségével adatok lekérdezése az Azure Cosmos DB.Ismerje meg, hogyan sql-lekérdezések adatok lekérdezése az Azure Cosmos DB. Mintaadatokat tölthet fel egy tárolóba az Azure Cosmos DB-ben, és lekérdezheti azokat.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873335"
---
# <a name="getting-started-with-sql-queries"></a>Az SQL-lekérdezések első lépései

Az Azure Cosmos DB SQL API-fiókok támogatják az elemek lekérdezését strukturált lekérdezési nyelv (SQL) json-lekérdezési nyelvként. Az Azure Cosmos DB lekérdezési nyelv tervezési céljai a következők:

* Új lekérdezési nyelv feltalálása helyett az SQL-t, az egyik legismerősebb és legnépszerűbb lekérdezési nyelvet támogatja. Az SQL egy hivatalos programozási modellt biztosít a JSON-elemek gazdag lekérdezéseihez.  

* Használja a JavaScript programozási modelljét a lekérdezési nyelv alapjaként. A JavaScript típusrendszere, a kifejezéskiértékelés és a függvénymeghívás az SQL API gyökerei. Ezek a gyökerek természetes programozási modellt biztosítanak olyan funkciókhoz, mint a relációs vetületek, a JSON-elemek hierarchikus navigációja, az önillesztők, a térbeli lekérdezések és a felhasználó által definiált függvények (UDF-ek) teljes egészében JavaScript-ben írt meghívása.

## <a name="upload-sample-data"></a>Mintaadatok feltöltése

Az SQL API Cosmos DB-fiókjában `Families`hozzon létre egy tárolót. Hozzon létre két egyszerű JSON-elemet a tárolóban. A legtöbb mintalekérdezést az Azure Cosmos DB lekérdezési dokumentumok ezzel az adatkészlethasználatával futtathatja.

### <a name="create-json-items"></a>JSON-elemek létrehozása

A következő kód két egyszerű JSON-elemet hoz létre a családokról. Az egyszerű JSON elemek az Andersen és Wakefield családok közé tartozik a szülők, a gyermekek és a háziállatok, cím, és a regisztrációs információkat. Az első elem karakterláncokat, számokat, logikai, tömböket és beágyazott tulajdonságokat tartalmaz.


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

A második `givenName` elem `familyName` a `firstName` `lastName`helyett a .

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

Próbáljon ki néhány lekérdezést a JSON-adatokkal szemben az Azure Cosmos DB SQL-lekérdezési nyelvének néhány kulcsfontosságú szempontjának megértéséhez.

A következő lekérdezés azokat `id` az `AndersenFamily`elemeket adja vissza, amelyeknek a mező egyezik. Mivel ez egy `SELECT *` lekérdezés, a lekérdezés kimenete a teljes JSON-elem. A SELECT szintaxisról a [SELECT utasításban](sql-query-select.md)talál további információt. 

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

A következő lekérdezés a JSON kimenetet egy másik alakzatba formázza. A lekérdezés egy új `Family` JSON-objektumot `Name` `City`vetít ki két kijelölt mezővel, és ha a címváros megegyezik az állapottal. "NY, NY" megegyezik ezzel az üggyel.

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

A következő lekérdezés visszaadja a család összes `id` `WakefieldFamily`olyan gyermekének a megadott nevét, akiknek a megegyezéseit város szerint rendezve.

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

Az előző példák a Cosmos DB lekérdezési nyelvének több aspektusát mutatják be:  

* Mivel az SQL API JSON-értékeken működik, a sorok és oszlopok helyett fa alakú entitásokkal foglalkozik. A facsomópontokat tetszőleges mélységben is hivatkozhat, hasonlóan `Node1.Node2.Node3…..Nodem`az ANSI `<table>.<column>` SQL kétrészből álló hivatkozásához.

* Mivel a lekérdezési nyelv séma nélküli adatokkal működik, a típusrendszert dinamikusan kell kötni. Ugyanaz a kifejezés különböző típusú elemeket eredményezhet. A lekérdezés eredménye egy érvényes JSON-érték, de nem garantált, hogy egy rögzített séma.  

* Az Azure Cosmos DB csak a szigorú JSON-elemeket támogatja. A típusrendszer és a kifejezések csak JSON-típusokkal foglalkoznak. További információt a [JSON specifikációban](https://www.json.org/)talál.  

* A Cosmos-tároló JSON-elemek sémamentes gyűjteménye. A tárolóelemeken belüli és azok közötti kapcsolatokat implicit módon rögzíti az elszigetelés, nem pedig az elsődleges kulcs és az idegenkulcs-kapcsolatok. Ez a funkció fontos a cikk későbbi részében tárgyalt elemen belüli illesztések esetében.

## <a name="next-steps"></a>További lépések

- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
- [Az Azure Cosmos DB .NET-mintái](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT záradék](sql-query-select.md)
