---
title: Térinformatikai adatok lekérdezése az Azure Cosmos DB-vel
description: Térbeli adatok lekérdezése az Azure Cosmos DB-vel
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 08b12bd9d35aaa61c79d35a55068983cdc0f1b83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77566321"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Térinformatikai adatok lekérdezése az Azure Cosmos DB-vel

Ez a cikk ismerteti, hogyan lekérdezheti a térinformatikai adatokat az Azure Cosmos DB SQL és LINQ használatával. A térinformatikai adatok jelenleg tárolását és elérését csak az Azure Cosmos DB SQL API-fiókok támogatják. Az Azure Cosmos DB a következő Open Geospatial Consortium (OGC) beépített függvényeket támogatja a térinformatikai lekérdezéshez. Az SQL-nyelv beépített függvényeinek teljes készletéről az [Azure Cosmos DB Query System Functions című témakörben](sql-query-system-functions.md)talál további információt.

## <a name="spatial-sql-built-in-functions"></a>Térbeli SQL beépített függvények

Az alábbiakban az Azure Cosmos DB lekérdezéséhez hasznos térinformatikai rendszerfüggvények listája látható:

|**Használat**|**Leírás**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | A két GeoJSON-pont, sokszög vagy LineString kifejezés közötti távolságot adja eredményül.|
|ST_WITHIN (spatial_expr, spatial_expr) | Logikai kifejezést ad vissza, amely azt jelzi, hogy az első GeoJSON objektum (Point, Polygon vagy LineString) a második GeoJSON objektumon belül van-e (Point, Polygon vagy LineString).|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Logikai kifejezést ad vissza, amely azt jelzi, hogy a két megadott GeoJSON-objektum (Point, Polygon vagy LineString) metszik-e egymást.|
|ST_ISVALID| Logikai értéket ad vissza, amely azt jelzi, hogy a megadott GeoJSON-pont, Sokszög vagy LineString kifejezés érvényes-e.|
| ST_ISVALIDDETAILED| Logikai értéket tartalmazó JSON-értéket ad eredményül, ha a megadott GeoJSON-pont, Sokszög vagy LineString kifejezés érvényes. Ha érvénytelen, az okot karakterlánc-értékként adja vissza.|

A térbeli függvények segítségével térbeli adatokkal kapcsolatos közelségi lekérdezések végezhetők. Például az alábbiakban egy lekérdezést, amely visszaadja az összes családi dokumentumok, amelyek 30 km-re a megadott hely a beépített függvény használatával. `ST_DISTANCE`

**Lekérdezés**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

**Results (Eredmények)**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Ha az indexelési házirendben területi indexelést is beilleszt, akkor a "távolságlekérdezések" hatékonyan jelennek meg az indexen keresztül. A térbeli indexelésről további információt a [térinformatikai indexelés](sql-query-geospatial-index.md)című témakörben talál. Ha nem rendelkezik térbeli index a megadott elérési utak, a lekérdezés a tároló vizsgálata.

`ST_WITHIN`segítségével ellenőrizheti, hogy egy pont egy sokszögön belül helyezkedik-e el. A sokszögek általában olyan határokat jelölnek, mint az irányítószámok, az államhatárok vagy a természetes alakzatok. Ismét, ha az indexelési házirend térbeli indexelést is beilleszti, akkor a "within" lekérdezések hatékonyan lesznek kézbesítve az indexen keresztül.

A sokszög argumentumok csak egyetlen gyűrűt `ST_WITHIN` tartalmazhatnak, azaz a sokszögek nem tartalmazhatnak lyukakat.

**Lekérdezés**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon',
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**Results (Eredmények)**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> Hasonlóan ahhoz, ahogyan az Azure Cosmos DB-lekérdezésben nem egyező típusok működnek, ha az argumentumban megadott helyérték hibás vagy érvénytelen, akkor a kiértékelendő **dokumentumot nem definiáltnak** értékeli, és a kiértékelendő dokumentumot ki kell hagyni a lekérdezés eredményéből. Ha a lekérdezés nem `ST_ISVALIDDETAILED` ad vissza eredményt, futtassa a hibakereséshez, hogy miért érvénytelen a térbeli típus.
>
>

Az Azure Cosmos DB is támogatja az inverz lekérdezések, azaz az Azure Cosmos DB-ben indexelheti a sokszögeket vagy vonalakat, majd lekérdezheti a megadott pontot tartalmazó területeket. Ezt a mintát gyakran használják a logisztikában, például annak azonosítására, amikor egy teherautó belép egy kijelölt területre, vagy elhagyja azt.

**Lekérdezés**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)
```

**Results (Eredmények)**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

`ST_ISVALID`és `ST_ISVALIDDETAILED` akkor lehet ellenőrizni, ha egy térbeli objektum érvényes. A következő lekérdezés például egy tartományon kívüli szélességi értékkel rendelkező pont érvényességét ellenőrzi (-132.8). `ST_ISVALID`csak egy logikai értéket `ST_ISVALIDDETAILED` ad vissza, és a logikai értéket és egy karakterláncot ad vissza, amely az érvénytelenség okát tartalmazza.

**Lekérdezés**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Results (Eredmények)**

```json
    [{
      "$1": false
    }]
```

Ezek a függvények a sokszögek érvényesítésére is használhatók. Itt például egy `ST_ISVALIDDETAILED` nem lezárt sokszög érvényesítésére használjuk.

**Lekérdezés**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**Results (Eredmények)**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>LINQ lekérdezés a .NET SDK-ban

Az SQL .NET SDK a `Distance()` `Within()` stub metódusokat is szolgáltatókat, valamint a LINQ-kifejezésekben való használatra. Az SQL LINQ-szolgáltató lefordítja ezt a metódust a megfelelő SQL beépített függvényhívásokra (ST_DISTANCE és ST_WITHIN).

Íme egy példa egy LINQ-lekérdezésre, amely megtalálja az `location` összes dokumentumot az Azure Cosmos tárolóban, amelynek értéke a megadott pont 30 km-es sugarú sugarú körén belül van a LINQ használatával.

**LINQ lekérdezés a távolsághoz**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

Hasonlóképpen, itt egy lekérdezés tegyezik `location` meg az összes dokumentumot, amely a megadott mezőben / Sokszög.

**LINQ lekérdezés belül**

```csharp
    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
         .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }
```

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan kezdheti el a térinformatikai támogatást az Azure Cosmos DB-ben, a következő ta-

* További információ az [Azure Cosmos DB-lekérdezésről](sql-query-getting-started.md)
* További információ a [Térinformatikai és geoJSON-helyadatokról az Azure Cosmos DB-ben](sql-query-geospatial-intro.md)
* További információ az [Index térbeli adatokról az Azure Cosmos DB segítségével](sql-query-geospatial-index.md)
