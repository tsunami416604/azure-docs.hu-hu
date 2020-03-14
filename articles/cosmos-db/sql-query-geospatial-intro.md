---
title: Térinformatikai és GeoJSON helye Azure Cosmos DB
description: Megtudhatja, hogyan hozhat létre térbeli objektumokat Azure Cosmos DB és az SQL API-val.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 655c3a96792fba83ac73365f02d48ce0347e9048
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137886"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Térinformatikai és GeoJSON helye Azure Cosmos DB

Ez a cikk az Azure Cosmos DB a térinformatikai funkciókat bemutató. Jelenleg csak Azure Cosmos DB SQL API-fiókok támogatják a térinformatikai adatok tárolását és elérését. A térinformatikai indexelés dokumentációjának elolvasása után a következő kérdésekre kaphat választ:

* Hogyan térbeli adatok tárolása az Azure Cosmos DB?
* Hogyan lekérdezheti az Azure Cosmos DB az SQL és a LINQ térinformatikai adatok?
* Hogyan engedélyezése vagy letiltása az Azure Cosmos DB kifejezéséhez?

## <a name="introduction-to-spatial-data"></a>Bevezetés a térbeli adatok

Térbeli adatok pozíció és objektumok terület alakja ismerteti. A legtöbb alkalmazásban ezek felel meg az objektumok a föld és a földrajzi adatok. Térbeli adatok segítségével egy személy, egy helyen a lényeges vagy a város vagy egy lake határait helyét jelölik. Gyakori alkalmazási helyzetek gyakran közelségi lekérdezések, például olyan, "minden kávézóban található a jelenlegi tartózkodási közel."

A Azure Cosmos DB SQL API két térbeli adattípust támogat: a **geometria** adattípust és a **Földrajz** adattípust.

- A **geometria** típusa egy euklideszi (Flat) koordináta-rendszerbe tartozó adatoknak felel meg.
- A **földrajzi** típus a kör alakú koordináta-rendszerbeli adathalmazokat jelöli.

## <a name="supported-data-types"></a>Támogatott adattípusok

Azure Cosmos DB támogatja a [GeoJSON-specifikáció](https://tools.ietf.org/html/rfc7946)alapján megjelenített térinformatikai pont adatok indexelését és lekérdezését. GeoJSON datové struktury mindig érvényes JSON-objektumok, így azok is tárolhatók, és az Azure Cosmos DB által kibocsátott speciális eszközök és kódtárak nélkül megkérdezi.

Azure Cosmos DB a következő térbeli adattípusokat támogatja:

- Pont
- LineString
- Sokszög
- MultiPolygon

### <a name="points"></a>Pontok

Egy **pont** egyetlen helyet jelöl a térben. A térinformatikai adatok egy pont, amely egy címe pékséglánc áruházbeli, a teljes képernyős, egy autó vagy város lehet pontos helyét jelöli.  A GeoJSON (és az Azure Cosmos DB) használatával a koordináta pár vagy szélességi és hosszúsági egy pont jelzi.

Íme egy példa JSON egy ponthoz:

**Azure Cosmos DBi pontok**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

A térbeli adattípusokat beágyazhatja egy Azure Cosmos DB-dokumentumba, ahogy az ebben a példában látható, hogy a felhasználói profil tartalmazza a helyadatok:

**Azure Cosmos DBban tárolt hellyel rendelkező profil használata**

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

### <a name="points-in-a-geometry-coordinate-system"></a>Geometriai koordináta-rendszer pontjai

A **geometria** adattípus esetében a GeoJSON-specifikáció meghatározza a vízszintes tengelyt és a második függőleges tengelyt.

### <a name="points-in-a-geography-coordinate-system"></a>Földrajzi koordináták rendszerbeli pontok

A **földrajzi** adattípus esetében a GeoJSON-specifikáció meghatározza az első és a szélességi fok értéket. Például a többi leképezési alkalmazások, a szélességi és hosszúsági szögek és fok formájában jelennek meg. Hosszúsági értékeket mért vannak a szélességi és-180 fok és 180.0 fok közé, és szélességi értékek vannak az Egyenlítőtől mért-90.0 fok és 90.0 fok közé.

Az Azure Cosmos DB értelmezi a koordináták a WGS-84. referenciarendszer egy határozzák meg. Koordináta referencia-rendszerekkel kapcsolatos további részletekért lásd az alábbi.

### <a name="linestrings"></a>Linestring

A **linestring** két vagy több pontból álló sorozatot jelöl, és az azokat összekapcsolási szegmenseket. A térinformatikai adatok Linestring gyakran használják autópályák vagy folyókat ábrázolásához.

**Linestring a GeoJSON**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Sokszögek

A **sokszög** a csatlakoztatott pontok határa, amelyek egy lezárt LineString alkotnak. Sokszög Lake például természetes kialakításokat vagy politikai, mint például a város és állam bíróságainak gyakran használják. Íme egy példa egy sokszögre a Azure Cosmos DBban:

**Sokszögek a GeoJSON**

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
> A GeoJSON specifikációnak megköveteli, hogy érvényes sokszögek, a megadott utolsó koordináta pár legyen ugyanaz, mint az első zárt alakzatot hozhat létre.
>
> Sokszög belül pontok óramutató járásával ellentétes irányban sorrendben kell adni. Egy megadott óramutató sorrendben sokszög benne a régió inverzét jelöli.
>
>

### <a name="multipolygons"></a>Multipolygon

A **Többsokszögű** tömb nulla vagy több sokszögből áll. A **Többsokszögű** oldalak nem lehetnek átfedésben a lapokkal, vagy nem lehetnek közös területek. Egy vagy több ponton is megjelenhetnek.

**Több sokszög a GeoJSON**

```json
{
    "type":"MultiPolygon",
    "coordinates":[ [
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
    ],
    [
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
    ] ]
}
```

## <a name="coordinate-reference-systems"></a>Koordináta referencia rendszerek

Mivel a föld alakja szabálytalan, a földrajzi térinformatikai adathalmazok koordinátái számos koordináta-hivatkozási rendszerben (CRS) jelennek meg, amelyek mindegyike saját hivatkozási kerettel és mértékegységgel rendelkezik. Ha például a "nemzeti rács-Britannia" hivatkozást a rendszer pontos-e az Egyesült Királyság, de nem azon kívül jelenik meg.

A jelenleg használatban lévő legnépszerűbb CRS a [WGS-84](https://earth-info.nga.mil/GandG/update/index.php)globális geodéziai rendszer. A GPS-eszközök és a sok leképezés-szolgáltatással, például a Google térkép alkalmazásban és a Bing térképek API-t használja a WGS-84. Azure Cosmos DB támogatja a földrajzi térinformatikai adatai indexelését és lekérdezését kizárólag a WGS-84 CRS használatával.

## <a name="creating-documents-with-spatial-data"></a>Térbeli adatokat tartalmazó dokumentumok létrehozása
Amikor GeoJSON értékeket tartalmazó dokumentumokat hoz létre, azok automatikusan indexelt térbeli indexszel rendelkező nevezhetnek, a tároló az indexelési házirendet. Ha egy Azure Cosmos DB SDK dinamikusan gépelt például a Python- vagy Node.js nyelven dolgozik, érvényes GeoJSON kell létrehoznia.

**Dokumentum létrehozása térinformatikai adattal a Node. js-ben**

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

Ha az SQL API-kkal dolgozik, a `Microsoft.Azure.Cosmos.Spatial` névtérben lévő `Point`, `LineString`, `Polygon`és `MultiPolygon` osztályok segítségével ágyazhatja be a hely adatait az alkalmazásobjektumok között. Ezeket az osztályokat segítségével egyszerűsítheti a szerializálási és a térbeli adatok deszerializálása GeoJSON be.

**Dokumentum létrehozása térinformatikai adattal a .NET-ben**

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

Ha nem rendelkezik a szélességi és a hosszúsági adatokkal, de a fizikai címeket vagy a hely nevét, például a várost vagy az országot/régiót, megkeresheti a tényleges koordinátákat egy olyan helymeghatározáshoz-szolgáltatás használatával, mint a Bing Maps REST Services. További információ a Bing Maps helymeghatározáshoz [itt](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulhatta, hogyan kezdheti el a földrajzi támogatásával az Azure Cosmos DB, ezután is:

* További információ a [Azure Cosmos db lekérdezésről](sql-query-getting-started.md)
* További információ a [térbeli adatainak a Azure Cosmos db való lekérdezéséről](sql-query-geospatial-query.md)
* További információ a [térbeli információk indexeléséről Azure Cosmos db](sql-query-geospatial-index.md)