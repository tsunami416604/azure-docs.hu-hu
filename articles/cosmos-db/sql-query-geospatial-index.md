---
title: Térinformatikai adatok indexelése az Azure Cosmos DB-vel
description: Térbeli adatok indexelése az Azure Cosmos DB-vel
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: eb0a2b2778b3217e185b9883def6eaa54674cc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137903"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Térinformatikai adatok indexelése az Azure Cosmos DB-vel

Úgy terveztük meg az Azure Cosmos DB adatbázis-motorját, hogy valóban séma-agnosztikus legyen, és első osztályú támogatást nyújtson a JSON-hoz. Az Azure Cosmos DB írásra optimalizált adatbázis-motorja natív módon értelmezi a GeoJSON-szabványban képviselt térbeli adatokat.

Dióhéjban, a geometria vetítik geodéziai koordináták rá egy 2D sík, majd osztva fokozatosan sejtek segítségével **quadtree**. Ezek a cellák a **Hilbert térkitöltési görbén**belüli cella helye alapján vannak leképezve az 1D-re, amely megőrzi a pontok helyét. Emellett, ha a helyadatok indexelése, megy keresztül egy folyamat néven **tessellation**, azaz az összes olyan cella, amely metszi a helyet, azonosítja és tárolja a kulcsokat az Azure Cosmos DB index. Lekérdezési időpontban a lekérdezési időpontban a pontokhoz és a sokszögekhez hasonló argumentumok is elvannak tervezve a megfelelő cellaazonosító-tartományok kinyeréséhez, majd az indexből az adatok beolvasására szolgálnak.

Ha olyan indexelési házirendet ad meg, amely tartalmazza a /* (az összes elérési út) térbeli indexét, akkor a tárolóban található összes adat indexelésre kerül a hatékony térbeli lekérdezések érdekében.

> [!NOTE]
> Az Azure Cosmos DB támogatja a pontok, linestringek, sokszögek és multipoligonok indexelését
>
>

## <a name="modifying-geospatial-data-type"></a>Térinformatikai adattípus módosítása

A tárolóban `geospatialConfig` a térinformatikai adatok indexelésének módját adja meg. Tárolónként egyet `geospatialConfig` kell megadnia: földrajzi vagy geometriai. Ha nincs megadva, a `geospatialConfig` földrajzi adattípus alapértelmezett lesz. A módosításakor `geospatialConfig`a tárolóban lévő összes térinformatikai adat újraindexelésre kerül.

> [!NOTE]
> Az Azure Cosmos DB jelenleg csak a 3.6-os vagy újabb verziókban támogatja a térinformatikaikonfiguráció módosításait.
>

Íme egy példa a térinformatikai adattípus `geometry` módosítására `geospatialConfig` a tulajdonság beállításával és egy **határolókeret hozzáadásával:**

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

## <a name="geography-data-indexing-examples"></a>Példák földrajzi adatok indexelésére

A következő JSON-kódrészlet egy indexelési házirendet jelenít meg, amelynek térbeli indexelése engedélyezve van a **földrajzi** adattípushoz. A földrajzi adattípussal rendelkező térbeli adatokra érvényes, és indexeli a dokumentumokban térbeli lekérdezéshez található GeoJSON-pontot, Sokszöget, MultiPoligont vagy LineStringet. Ha az Azure Portalon keresztül módosítja az indexelési szabályzatot, megadhatja a következő JSON-t az indexelési szabályzathoz a tároló térbeli indexelésének engedélyezéséhez:

**Tároló indexelési házirend JSON földrajzi térbeli indexelés**

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
> Ha a geoJSON hely értéke a dokumentumon belül hibás vagy érvénytelen, akkor nem lesz indexelve térbeli lekérdezéshez. A helyértékeket ST_ISVALID és ST_ISVALIDDETAILED segítségével érvényesítheti.

Az Azure CLI, a PowerShell vagy bármely SDK használatával [indexelési szabályzat is módosíthatja.](how-to-manage-indexing-policy.md)

## <a name="geometry-data-indexing-examples"></a>Példák geometriai adatok indexelési

A földrajzi adattípushoz hasonló **geometriai** adattípussal meg kell adnia a megfelelő görbéket és típusokat az indexeléshez. Ezenkívül meg kell adnia egy `boundingBox` indexelési házirenden belül, amely jelzi az adott elérési úthoz indexelendő kívánt területet. Minden térinformatikai útvonalhoz`boundingBox`saját kell.

A határolókeret a következő tulajdonságokból áll:

- **xmin**: a minimális indexelt x koordináta
- **ymin**: a minimális indexelt y koordináta
- **xmax**: a maximális indexelt x koordináta
- **ymax**: a maximális indexelt y koordináta

Határolókeretre azért van szükség, mert a geometriai adatok végtelen síkot foglalnak el. A térbeli indexek azonban véges teret igényelnek. A **földrajzi** adattípus esetében a Föld a határvonal, és nem kell határolókeretet beállítania.

Hozzon létre egy határolókeretet, amely az összes adatot (vagy a legtöbb adatot) tartalmazza. Csak a teljes mértékben a határolókereten belüli objektumokon számított műveletek tudják használni a térbeli indexet. A határolókeretet ne tegye jelentősen nagyobbá a szükségesnél, mert ez negatívan befolyásolja a lekérdezés teljesítményét.

Íme egy példa indexelési házirend, amely indexeli `geometry` **a geometriai** adatok at **térinformatikaiConfig** beállítása:

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

A fenti indexelési házirend **határolókoorbája** (-10, 10) x koordináták, y koordináták esetén pedig (-20, 20). A fenti indexelési házirendet tartalmazó tároló indexeli az összes olyan pontot, sokszöget, multipoligont és linestringet, amelyek teljes egészében ebben a régióban találhatók.

> [!NOTE]
> Ha egy **boundingbox-pal-t** tartalmazó indexelési `geography` házirendet próbál hozzáadni egy adattípusú tárolóhoz, az sikertelen lesz. A **boundingbox**hozzáadása `geometry` előtt módosítania kell a tároló **térinformatikai konfigurációját.** Adatokat adhat hozzá, és módosíthatja az indexelési házirend fennmaradó részét (például az elérési utakat és típusokat) a tároló térinformatikai adattípusának kiválasztása előtt vagy után.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan kezdheti el a térinformatikai támogatást az Azure Cosmos DB-ben, a következő ta-

* További információ az [Azure Cosmos DB-lekérdezésről](sql-query-getting-started.md)
* További információ a [térbeli adatok lekérdezéséről az Azure Cosmos DB segítségével](sql-query-geospatial-query.md)
* További információ a [Térinformatikai és geoJSON-helyadatokról az Azure Cosmos DB-ben](sql-query-geospatial-intro.md)