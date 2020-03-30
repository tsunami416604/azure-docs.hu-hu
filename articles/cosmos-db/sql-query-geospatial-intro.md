---
title: Térinformatikai és geoJSON-helyadatok az Azure Cosmos DB-ben
description: Ismerje meg, hogyan hozhat létre térbeli objektumokat az Azure Cosmos DB és az SQL API használatával.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 59c8b31dcc8594d2cafb2db7832e290b01026f60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367584"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Térinformatikai és geoJSON-helyadatok az Azure Cosmos DB-ben

Ez a cikk az Azure Cosmos DB térinformatikai funkcióinak bemutatása. A térinformatikai adatok jelenleg tárolását és elérését csak az Azure Cosmos DB SQL API-fiókok támogatják. A térinformatikai indexelésről szóló dokumentáció elolvasása után a következő kérdésekre válaszolhat:

* Hogyan tárolhatom a térbeli adatokat az Azure Cosmos DB-ben?
* Hogyan kérdezhetem le a térinformatikai adatokat az Azure Cosmos DB-ben AZ SQL és a LINQ rendszerben?
* Hogyan engedélyezhetem vagy tilthatja le a térbeli indexelést az Azure Cosmos DB-ben?

## <a name="introduction-to-spatial-data"></a>A térbeli adatok bemutatása

A térbeli adatok az objektumok helyzetét és alakját írják le a térben. A legtöbb alkalmazásban ezek a Föld és a térinformatikai adatok objektumainak felelnek meg. A térbeli adatok egy személy, egy érdekes hely, egy város vagy egy tó határát ábrázolhatják. A gyakori használati esetek gyakran közelségi lekérdezésekkel járnak, például "az aktuális tartózkodási helyem közelében található összes kávézó megkeresése".

Az Azure Cosmos DB SQL API-ja két térbeli adattípust támogat: a **geometriai adattípust** és a **földrajzi** adattípust.

- A **geometriatípus** egy euklideszi (lapos) koordináta-rendszer adatait jelöli
- A **földrajzi** típus egy földkörüli koordináta-rendszerben lévő adatokat jelöli.

## <a name="supported-data-types"></a>Támogatott adattípusok

Az Azure Cosmos DB támogatja a [GeoJSON-specifikáció](https://tools.ietf.org/html/rfc7946)használatával képviselt térinformatikai pontadatok indexelését és lekérdezését. A GeoJSON-adatstruktúrák mindig érvényes JSON-objektumok, így az Azure Cosmos DB használatával speciális eszközök vagy kódtárak nélkül tárolhatók és kérdezhetők le.

Az Azure Cosmos DB a következő térbeli adattípusokat támogatja:

- Pont
- Vonalsor-karakterlánc
- Sokszög
- MultiPoligon

### <a name="points"></a>Pont

A **pont** egyetlen helyet jelöl a térben. A térinformatikai adatokban a pont a pontos helyet jelöli, amely lehet egy élelmiszerbolt, egy kioszk, egy autó vagy egy város utcacíme.  Egy pont a GeoJSON (és az Azure Cosmos DB) a koordinátapár vagy hosszúsági és szélességi.

Íme egy példa JSON egy pontot:

**Pontok az Azure Cosmos DB-ben**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

A térbeli adattípusok beágyazhatók egy Azure Cosmos DB-dokumentumba, amint az a helyadatokat tartalmazó felhasználói profil ban látható:

**A Profil használata az Azure Cosmos DB-ben tárolt helyekkel**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

### <a name="points-in-a-geometry-coordinate-system"></a>Pontok a geometriakoordináta-rendszerben

A **geometriai** adattípushoz a GeoJSON specifikáció határozza meg először a vízszintes tengelyt és a függőleges tengelyt a második.

### <a name="points-in-a-geography-coordinate-system"></a>Pontok egy földrajzi koordináta-rendszerben

A **földrajzi** adattípushoz a GeoJSON specifikáció az első és a második szélességi fokot határozza meg. Mint más térképészeti alkalmazások, hosszúsági és szélességi szögek és képviseli a fok. A hosszúsági értékeket a Prime Meridian-tól mérik, és -180 fok és 180,0 fok között vannak, a szélességi értékeket pedig az egyenlítőtől, és -90,0 fok és 90,0 fok között.

Az Azure Cosmos DB a WGS-84 referenciarendszer szerint értelmezi a koordinátákat. A koordináta-referenciarendszerekkel kapcsolatos további részletekért lásd alább.

### <a name="linestrings"></a>LineStrings (Vonalsorok)

**A lineStringek** két vagy több pontból álló sorozatot jelentenek a térben, valamint az azokat összekötő vonalszakaszokat. A térinformatikai adatokban a LineStrings-et gyakran használják autópályák vagy folyók ábrázolására.

**LineStrings a GeoJSON-ban**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Sokszög

A **sokszög** a kapcsolódó pontok határa, amely zárt LineString-et képez. A sokszögeket gyakran használják természetes képződmények, például tavak vagy politikai joghatóságok, például városok és államok ábrázolására. Íme egy példa egy sokszögre az Azure Cosmos DB-ben:

**Sokszögek a GeoJSON-ban**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> A GeoJSON specifikáció megköveteli, hogy érvényes sokszögek esetén az utolsó megadott koordinátapár nak meg kell egyeznie az elsővel, hogy zárt alakzatot hozzon létre.
>
> A sokszögen belüli pontokat az óramutató járásával ellentétes sorrendben kell megadni. Az óramutató járásával megegyező sorrendben megadott sokszög a benne lévő régió inverzét jelöli.
>
>

### <a name="multipolygons"></a>MultiPoligonok

A **MultiPolygon** nulla vagy több sokszögből álló tömb. **A MultiPolygons** nem fedheti át az oldalakat, és nem rendelkezhet közös területtel. Egy vagy több ponton is megérinthetnek.

**MultiPolikonok a GeoJSON-ban**

```json
{
    "type":"MultiPolygon",
    "coordinates":[[[
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
        ]],
        [[
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
        ]]]
}
```

## <a name="coordinate-reference-systems"></a>Koordináta-referenciarendszerek

Mivel a Föld alakja szabálytalan, a földrajzi térinformatikai adatok koordinátái számos koordináta-referenciarendszerben (CRS) jelennek meg, amelyek mindegyike saját referenciakerettel és mértékegységekkel rendelkezik. Például a "National Grid of Britain" egy referenciarendszer pontos az Egyesült Királyság, de nem azon kívül.

A legnépszerűbb CRS használatban van ma a World Geodéziai Rendszer [WGS-84](https://earth-info.nga.mil/GandG/update/index.php). A GPS-eszközök és számos térképezési szolgáltatás, köztük a Google Térkép és a Bing Maps API-k a WGS-84-et használják. Az Azure Cosmos DB csak a WGS-84 CRS használatával támogatja a földrajzi térinformatikai adatok indexelését és lekérdezését.

## <a name="creating-documents-with-spatial-data"></a>Dokumentumok létrehozása téradatokkal
GeoJSON-értékeket tartalmazó dokumentumokat hoz létre, azok automatikusan indexelésre kerülnek egy térbeli indexszel a tároló indexelési házirendjének megfelelően. Ha egy Azure Cosmos DB SDK-val dolgozik egy dinamikusan gépelt nyelven, például python vagy Node.js, létre kell hoznia érvényes GeoJSON.

**Dokumentum létrehozása térinformatikai adatokkal a Node.js**

```javascript
var userProfileDocument = {
    "id":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Ha az SQL API-kkal dolgozik, a `Point` `LineString`, `Polygon`, `MultiPolygon` és a `Microsoft.Azure.Cosmos.Spatial` névtérben lévő osztályok segítségével helyadatokat ágyazhat be az alkalmazásobjektumokba. Ezek az osztályok segítenek egyszerűsíteni a térbeli adatok szerializálását és deszerializálását a GeoJSON-ba.

**Dokumentum létrehozása térinformatikai adatokkal a .NET-ben**

```csharp
using Microsoft.Azure.Cosmos.Spatial;

public class UserProfile
{
    [JsonProperty("id")]
    public string id { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await container.CreateItemAsync( new UserProfile
    {
        id = "cosmosdb",
        Location = new Point (-122.12, 47.66)
    });
```

Ha nem rendelkezik a szélességi és hosszúsági adatokkal, de rendelkezik a fizikai címekkel vagy a hely nevével, például várossal vagy országgal/régióval, a tényleges koordinátákat egy geokódolási szolgáltatás, például a Bing Maps REST-szolgáltatások használatával keresheti meg. A Bing Maps geokódolásáról itt olvashat [bővebben.](https://msdn.microsoft.com/library/ff701713.aspx)

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan kezdheti el a térinformatikai támogatást az Azure Cosmos DB-ben, a következő ta-

* További információ az [Azure Cosmos DB-lekérdezésről](sql-query-getting-started.md)
* További információ a [térbeli adatok lekérdezéséről az Azure Cosmos DB segítségével](sql-query-geospatial-query.md)
* További információ az [Index térbeli adatokról az Azure Cosmos DB segítségével](sql-query-geospatial-index.md)