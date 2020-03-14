---
title: Térinformatikai adatindexek indexelése Azure Cosmos DB
description: Térbeli adatainak indexelése Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: eb0a2b2778b3217e185b9883def6eaa54674cc5b
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137903"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Térinformatikai adatindexek indexelése Azure Cosmos DB

Azure Cosmos DB adatbázis-motorját úgy terveztük, hogy valóban séma-agnosztikus legyen, és első osztályú támogatást nyújtson a JSON-hoz. A Azure Cosmos DB írásra optimalizált adatbázismotor natív módon értelmezi a GeoJSON standardban ábrázolt térbeli adatokra vonatkozó információkat.

Dióhéjban a geometria a geodéziai Koordinátákból egy 2D-síkra van kialakítva, majd fokozatosan, egy **quadtree**használatával osztva a cellákba. Ezek a cellák az 1D-re vannak leképezve a cella helye alapján a Hilbert belül, amely megőrzi a pontok helyi **kitöltését**. Emellett, ha a helyadatok indexelve lettek, egy **mozaikként**ismert folyamaton halad át, azaz az adott helyet keresztező összes cella azonosítható és kulcsként tárolódik a Azure Cosmos db indexben. Lekérdezéskor például a pontok és poligonok argumentumok is Csempézett, bontsa ki a megfelelő azonosító cellatartományokról, akkor az indexből adatok lekérésére használt.

Ha olyan indexelési házirendet ad meg, amely tartalmazza a/* (az összes elérési út) térbeli indexét, akkor a tárolóban található összes adathalmaz a hatékony térbeli lekérdezésekhez van indexelve.

> [!NOTE]
> Azure Cosmos DB támogatja a pontok, Linestring, sokszögek és többsokszögek indexelését
>
>

## <a name="modifying-geospatial-data-type"></a>Térinformatikai adattípus módosítása

A tárolóban a `geospatialConfig` megadja, hogy a térinformatikai adatai hogyan lesznek indexelve. Egy `geospatialConfig` kell megadnia a Container: földrajz vagy a geometriában. Ha nincs megadva, a `geospatialConfig` alapértelmezés szerint a földrajzi adattípust fogja megadni. A `geospatialConfig`módosításakor a rendszer a tárolóban lévő összes meglévő térinformatikai adattal újraindexeli.

> [!NOTE]
> Azure Cosmos DB jelenleg csak a 3,6-es és újabb verziókban támogatja a .NET SDK-ban lévő geospatialConfig módosításait.
>

Az alábbi példa a térinformatikai adattípus módosítását `geometry` a `geospatialConfig` tulajdonság beállításával és a **boundingBox**hozzáadásával:

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>Földrajzi adatindexelési példák

A következő JSON-kódrészlet egy indexelési házirendet mutat be, amelyen engedélyezve van a **földrajzi** adattípusú térbeli indexelés. A földrajzi adattípussal rendelkező térbeli értékek esetében érvényes, és indexeli az GeoJSON pontot, a sokszöget, a többsokszögű vagy a LineString dokumentumokat a térbeli lekérdezésekhez. Ha az indexelési házirendet a Azure Portal használatával módosítja, megadhatja a következő JSON-t az indexelési házirendhez, hogy engedélyezze a térbeli indexelést a tárolón:

**Tároló-indexelési házirend JSON földrajzi térbeli indexeléssel**

```json
    {
       "automatic":true,
       "indexingMode":"Consistent",
        "includedPaths": [
        {
            "path": "/*"
        }
        ],
        "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
       "excludedPaths":[]
    }
```

> [!NOTE]
> Ha a hely a dokumentum GeoJSON érték helytelen formátumú vagy érvénytelen, majd, nem get indexelve a térbeli lekérdezéséhez. Hely értékek ST_ISVALID és ST_ISVALIDDETAILED használatával ellenőrizheti.

Az [indexelési házirendet](how-to-manage-indexing-policy.md) az Azure CLI, a PowerShell vagy bármely SDK használatával is módosíthatja.

## <a name="geometry-data-indexing-examples"></a>Geometriai adatok indexelésére vonatkozó példák

Ha a **geometria** adattípusa hasonló a földrajzi adattípushoz, a megfelelő elérési utakat és típusokat kell megadnia az indexhez. Emellett meg kell adnia egy `boundingBox` az indexelési házirendben is, hogy jelezze az adott elérési útra vonatkozó kívánt területeket. Minden földrajzi útvonalhoz saját`boundingBox`szükséges.

A határoló mező a következő tulajdonságokat tartalmazza:

- **xmin**: a minimálisan indexelt x koordináta
- **ymin**: a minimális indexelt y koordináta
- **Xmax**: a maximális indexelt x koordináta
- **Ymax**: a maximális indexelt y koordináta

A határolókeret megadása kötelező, mert a geometriai adat egy olyan gépet foglal, amely végtelen lehet. A térbeli indexek esetében azonban véges terület szükséges. A **földrajzi** adattípushoz a föld a határ, és nem kell megadnia egy határoló mezőt.

Létre kell hoznia egy határoló mezőt, amely az összes (vagy a legtöbb) adatát tartalmazza. A térbeli indexet csak a teljes mértékben a határolókeret belsejében lévő objektumokra kiszámított műveletek lesznek képesek használni. A határolókeret a szükségesnél lényegesen nagyobb mértékben nem végezhető el, mert ez negatív hatással lesz a lekérdezés teljesítményére.

Az alábbi példa egy olyan indexelési házirendet mutat be, amely a **geometriai** adatok **geospatialConfig** -készlettel való indexelését `geometry`:

```json
 {
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

A fenti indexelési házirend **boundingBox** (-10, 10) tartalmaz x koordinátákat és (-20, 20) az y koordinátákhoz. A fenti indexelési szabályzattal rendelkező tároló az összes olyan pontot, sokszöget, többsokszögű és Linestring indexeli, amely teljes egészében ebben a régióban található.

> [!NOTE]
> Ha olyan indexelési házirendet próbál hozzáadni egy **boundingBox** , amely `geography` adattípusú tárolót hoz létre, az sikertelen lesz. A **boundingBox**hozzáadása előtt módosítania kell a tároló `geometry` **geospatialConfig** . A tároló térinformatikai adattípusának kiválasztását megelőzően vagy azt követően módosíthatja az indexelési szabályzat hátralévő részét (például az elérési utakat és típusokat).

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan kezdheti el a földrajzi támogatásával az Azure Cosmos DB, ezután is:

* További információ a [Azure Cosmos db lekérdezésről](sql-query-getting-started.md)
* További információ a [térbeli adatainak a Azure Cosmos db való lekérdezéséről](sql-query-geospatial-query.md)
* További információ a [térinformatikai és a GeoJSON-hely adatainak Azure Cosmos db](sql-query-geospatial-intro.md)