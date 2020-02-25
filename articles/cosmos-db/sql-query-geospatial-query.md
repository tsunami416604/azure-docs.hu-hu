---
title: Térinformatikai adatbázis lekérdezése Azure Cosmos DB
description: Térbeli adatainak lekérdezése Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 08b12bd9d35aaa61c79d35a55068983cdc0f1b83
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566321"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Térinformatikai adatbázis lekérdezése Azure Cosmos DB

Ez a cikk bemutatja, hogyan lehet lekérdezni a térinformatikai adataikat Azure Cosmos DB az SQL és a LINQ használatával. Jelenleg csak Azure Cosmos DB SQL API-fiókok támogatják a térinformatikai adatok tárolását és elérését. Az Azure Cosmos DB a következő nyissa meg a földrajzi Consortium (OGC) beépített függvények támogatja a térinformatikai lekérdezéséhez. Az SQL nyelv beépített funkcióinak teljes készletével kapcsolatos további információkért lásd: [rendszerfunkciók lekérdezése Azure Cosmos DBban](sql-query-system-functions.md).

## <a name="spatial-sql-built-in-functions"></a>Térbeli SQL beépített függvények

Itt találja a Azure Cosmos DB lekérdezéséhez hasznos térinformatikai rendszerfunkciók listáját:

|**Használat**|**Leírás**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | A két GeoJSON-pont, Polygon vagy LineString kifejezések között adja vissza a távolságot.|
|ST_WITHIN (spatial_expr, spatial_expr) | Egy logikai kifejezés, amely azt jelzi, hogy a második GeoJSON-objektum (pont, Polygon vagy LineString) belül van-e az első GeoJSON-objektumot (pont, Polygon vagy LineString) adja vissza.|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Egy logikai kifejezés jelzi, hogy a két megadott GeoJSON objektum (pont, Polygon vagy LineString) átfedésben adja vissza.|
|ST_ISVALID| Jelzi, hogy a megadott GeoJSON-pont, Polygon vagy LineString kifejezés érvénytelen egy logikai értéket ad vissza.|
| ST_ISVALIDDETAILED| Egy logikai értéket tartalmazó JSON-értéket ad vissza, ha a megadott GeoJSON pont, sokszög vagy LineString kifejezés érvényes. Ha érvénytelen, a rendszer karakterlánc-értékként adja vissza az okot.|

Térbeli funkciók térbeli adatokon közelségi lekérdezések végrehajtásához használható. Például egy olyan lekérdezés, amely az összes olyan családi dokumentumot adja vissza, amely a megadott hely 30 km-n belül található a `ST_DISTANCE` beépített függvény használatával.

**Lekérdezés**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

**Results**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Ha is kifejezéséhez az indexelési házirendet, majd "distance lekérdezések" fog hatékonyan kiszolgálható keresztül az index. További információ a térbeli indexelésről: [térinformatikai indexelés](sql-query-geospatial-index.md). Ha nem rendelkezik térbeli indexszel a megadott elérési utakhoz, a lekérdezés ellenőrzi a tárolót.

a `ST_WITHIN` használatával ellenőrizhető, hogy egy pont egy Sokszögen belül található-e. Sokszög gyakran határokat, például az irányítószámok, állam határok vagy természetes kialakításokat képviselő szolgálnak. Újra Ha is kifejezéséhez az indexelési házirendet, majd "belül" lekérdezések fog hatékonyan kiszolgálható keresztül az index.

A `ST_WITHIN`ban található sokszög argumentumok csak egyetlen gyűrűt tartalmazhatnak, azaz a sokszögek nem tartalmazhatnak lyukakat bennük.

**Lekérdezés**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon',
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**Results**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> Hasonlóan ahhoz, ahogyan a nem egyező típusok működnek Azure Cosmos DB lekérdezésekben, ha az argumentumban megadott Helykód helytelen formátumú vagy érvénytelen, akkor a rendszer nem **definiált** értékre értékeli, és a kiértékelt dokumentumot kihagyja a lekérdezés eredményeiből. Ha a lekérdezés nem ad vissza találatot, a `ST_ISVALIDDETAILED` futtatásával ellenőrizze, hogy a térbeli típus érvénytelen-e.
>
>

Az Azure Cosmos DB is támogatja a más néven inverz lekérdezések végrehajtásához, index poligonok vagy az Azure Cosmos DB sorokat, majd lekérdezni a területeken, amelyek tartalmaznak egy adott pont a. Ez a minta általában arra használják, a logisztika azonosítását, például amikor egy teherautó be- vagy egy erre kijelölt területen.

**Lekérdezés**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)
```

**Results**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

`ST_ISVALID` és `ST_ISVALIDDETAILED` használatával ellenőrizhető, hogy a térbeli objektum érvényes-e. A következő lekérdezés például kívüli tartományértéke szélesség (-132.8) rendelkező pontok érvényességét ellenőrzi. `ST_ISVALID` csak egy logikai értéket ad vissza, és a `ST_ISVALIDDETAILED` a logikai azonosítót és egy olyan karakterláncot ad vissza, amely az érvénytelennek minősülő okot adja meg.

**Lekérdezés**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Results**

```json
    [{
      "$1": false
    }]
```

Ezek a függvények is használható poligonok ellenőrzése. Például itt használjuk a `ST_ISVALIDDETAILED`t egy nem lezárt sokszög ellenőrzéséhez.

**Lekérdezés**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**Results**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>LINQ-lekérdezés a .NET SDK-ban

Az SQL .NET SDK emellett a következő, `Distance()` és `Within()` a LINQ-kifejezéseken belüli használatra szolgáló helyettes metódusokat is tartalmazza. Az SQL LINQ-szolgáltató fordítja le ezt a módszert a megfelelő SQL beépített függvényhívások-hívások (ST_DISTANCE és ST_WITHIN jelölik).

Íme egy példa egy LINQ-lekérdezésre, amely megkeresi az Azure Cosmos-tárolóban található összes olyan dokumentumot, amelynek `location` értéke a megadott pont 30 km sugarán belül van a LINQ használatával.

**LINQ-lekérdezés távolsághoz**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

Hasonlóképpen, az alábbi lekérdezéssel megtalálhatja az összes olyan dokumentumot, amelynek `location` a megadott dobozban/sokszögben van.

**LINQ-lekérdezés a következőn belül:**

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

Most, hogy megtanulhatta, hogyan kezdheti el a földrajzi támogatásával az Azure Cosmos DB, ezután is:

* További információ a [Azure Cosmos db lekérdezésről](sql-query-getting-started.md)
* További információ a [térinformatikai és a GeoJSON-hely adatainak Azure Cosmos db](sql-query-geospatial-intro.md)
* További információ a [térbeli információk indexeléséről Azure Cosmos db](sql-query-geospatial-index.md)
