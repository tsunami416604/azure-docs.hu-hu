---
title: Egyszerű adatréteg hozzáadása | Microsoft Azure térképek
description: Megtudhatja, hogyan adhat hozzá egy egyszerű adatréteget a Azure Maps web SDK által biztosított térbeli IO-modul használatával.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3fa54e3227496c11fcafc2f42e980daa5c716365
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370985"
---
# <a name="add-a-simple-data-layer"></a>Egyszerű adatréteg hozzáadása

A térbeli IO-modul egy `SimpleDataLayer` osztályt biztosít. Ez az osztály megkönnyíti a Térkép stílusú funkcióinak megjelenítését. Akár olyan adatkészleteket is tartalmazhat, amelyek stílus tulajdonságokkal és adatkészletekkel rendelkeznek, amelyek vegyes geometriai típusokat tartalmaznak. Az egyszerű adatréteg ezt a funkciót több renderelési réteg körbefuttatásával és a stílus kifejezések használatával éri el. A stílus kifejezések a becsomagolt rétegekben található szolgáltatások általános stílusának tulajdonságait keresik. A `atlas.io.read` függvény és a `atlas.io.write` függvény ezeket a tulajdonságokat használja a stílusok támogatott fájlformátumba való olvasására és írására. A tulajdonságok támogatott fájlformátumhoz való hozzáadása után a fájl különböző célokra használható. A fájl használható például a Térkép stílusú funkcióinak megjelenítésére.

A formázási funkciók mellett a `SimpleDataLayer` egy beépített előugró funkciót is biztosít egy előugró sablonnal. Az előugró ablak akkor jelenik meg, ha egy szolgáltatásra kattint. Ha szükséges, az alapértelmezett előugró funkció le is tiltható. Ez a réteg támogatja a fürtözött adattárolást is. Ha egy fürtre kattint, a Térkép nagyítja a fürtöt, és kibontja az egyes pontokra és alfürtökre.

A `SimpleDataLayer` osztály számos geometriai típussal rendelkező nagyméretű adathalmazokon, valamint a szolgáltatásokon alkalmazott számos stíluson használható. Ez az osztály felhasználja a Style kifejezéseket tartalmazó hat rétegből álló terhelést. Ezért vannak olyan esetek, amikor hatékonyabban használják az alapvető renderelési rétegeket. Például egy alapszintű réteg használatával megjelenítheti a különböző geometriai típusokat és néhány stílust a szolgáltatásban

## <a name="default-supported-style-properties"></a>Alapértelmezett támogatott stílus tulajdonságai

Ahogy azt korábban említettük, az egyszerű adatréteg több alapvető renderelési réteget is betakar: buborék, szimbólum, vonal, sokszög és extrudált sokszög. Ezután kifejezések használatával keres érvényes stílusú tulajdonságokat az egyes szolgáltatásokban.

A Azure Maps és a GitHub stílusú tulajdonságok a támogatott tulajdonságok két fő halmaza. A különböző Azure Maps-rétegbeli beállítások többsége az egyszerű adatrétegben található szolgáltatások stílusának tulajdonságaiként támogatott. Bizonyos rétegbeli beállításokhoz kifejezések lettek hozzáadva, amelyek támogatják a GitHub által leggyakrabban használt stílusú tulajdonságokat. Ezeket a tulajdonságokat a [GitHub GeoJSON-leképezési támogatása](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)határozza meg, és a platformon belül tárolt és megjelenített GeoJSON-fájlok stílusához használatosak. A GitHub összes stílusának tulajdonsága támogatott az egyszerű adatrétegben, kivéve a `marker-symbol` stílus tulajdonságait.

Ha az olvasó egy kevésbé gyakori stílusú tulajdonsággal találkozik, a rendszer a legközelebbi Azure Maps Style tulajdonságra konvertálja. Emellett az alapértelmezett stílusú kifejezések felülbírálható az egyszerű adatréteg `getLayers` funkciójával, valamint az egyes rétegek beállításainak frissítésével is.

A következő szakasz az egyszerű adatréteg által támogatott alapértelmezett stílusú tulajdonságok részleteit tartalmazza. A támogatott tulajdonságnév sorrendje szintén a tulajdonság prioritása. Ha ugyanahhoz a réteghez két stílus van definiálva, akkor a lista első elemének magasabb a prioritása.

## <a name="simple-data-layer-options"></a>Az adatrétegek egyszerű beállításai

### <a name="bubble-layer-style-properties"></a>Buborék réteg stílusa tulajdonságai

Ha egy szolgáltatás egy `Point` vagy egy `MultiPoint`, és a szolgáltatás nem rendelkezik olyan `image` tulajdonsággal, amelyet egyéni ikonként használhat a pont szimbólumként való megjelenítéséhez, akkor a szolgáltatás `BubbleLayer`fog megjelenni.

| Réteg beállítása | Támogatott tulajdonságok neve (i) | Alapértelmezett érték |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup>, `marker-size`<sup>2</sup>, `scale`<sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] a `size` és `scale` értékek skaláris értéknek számítanak, és a rendszer megszorozza őket `8`

\[2\] ha a GitHub `marker-size` beállítás meg van adva, akkor a rendszer a következő értékeket fogja használni a RADIUS-hez.

| Jelölő mérete | RADIUS- |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

A fürtöket a buborék réteg is megjeleníti. Alapértelmezés szerint a fürt sugara `16`értékre van állítva. A fürt színe a fürtben lévő pontok számától függ, az alábbiakban meghatározottak szerint:

| pontok száma | Szín    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt; 10     | `green`  |

### <a name="symbol-style-properties"></a>Szimbólum stílusa tulajdonságai

Ha egy szolgáltatás egy `Point` vagy egy `MultiPoint`, és a szolgáltatásnak van egy `image` tulajdonsága, amely egyéni ikonként használható a pont szimbólumként való megjelenítéséhez, akkor a szolgáltatás `SymbolLayer`fog megjelenni.

| Réteg beállítása | Támogatott tulajdonságok neve (i) | Alapértelmezett érték |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size`<sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] ha a GitHub `marker-size` beállítás meg van adva, akkor a rendszer a következő értékeket fogja használni az ikon méretének beállításához.

| Jelölő mérete | Szimbólum mérete |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Ha a pont funkció egy fürt, a `point_count_abbreviated` tulajdonság szövegként jelenik meg. Egyetlen rendszerkép sem jelenik meg.

### <a name="line-style-properties"></a>Vonalstílus tulajdonságai

Ha a szolgáltatás `LineString`, `MultiLineString`, `Polygon`vagy `MultiPolygon`, akkor a szolgáltatás `LineLayer`fog megjelenni.

| Réteg beállítása | Támogatott tulajdonságok neve (i) | Alapértelmezett érték |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Sokszög stílusa tulajdonságai

Ha a szolgáltatás egy `Polygon` vagy egy `MultiPolygon`, és a szolgáltatás nem rendelkezik `height` tulajdonsággal, vagy a `height` tulajdonság értéke nulla, akkor a szolgáltatás `PolygonLayer`fog megjelenni.

| Réteg beállítása | Támogatott tulajdonságok neve (i) | Alapértelmezett érték |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, "`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Extrudált sokszög stílusának tulajdonságai

Ha a szolgáltatás egy `Polygon` vagy egy `MultiPolygon`, és a `height` tulajdonság értéke 0-nál nagyobb, akkor a szolgáltatás `PolygonExtrusionLayer`fog megjelenni.

| Réteg beállítása | Támogatott tulajdonságok neve (i) | Alapértelmezett érték |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="use-a-simple-data-layer"></a>Egyszerű adatréteg használata

A `SimpleDataLayer` osztály a többi renderelési réteghez hasonlóan használatos. Az alábbi kód bemutatja, hogyan használható egy egyszerű adatréteg a térképen:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Szolgáltatások hozzáadása az adatforráshoz. Ezután az egyszerű adatrétegből megtudhatja, hogyan jeleníti meg a legjobb funkciókat. Az egyes funkciók stílusa a funkció tulajdonságaiként állítható be. A következő kód egy GeoJSON pont funkciót mutat be, amelynek `color` tulajdonsága `red`ra van beállítva. 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

A következő kód a fenti pont funkciót jeleníti meg az egyszerű adatréteg használatával. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Az egyszerű adatréteg használata" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Lásd a tollat <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>az egyszerű adatréteget</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az egyszerű adatréteg valódi ereje a következőket tartalmazhatja:

- Az adatforrás számos különböző típusú funkciót tartalmaz; vagy
- Az adathalmaz funkciói több stílust is magukban foglalnak. vagy
- Nem biztos benne, hogy pontosan mit tartalmaz az adathalmaz.

Például az XML-adatcsatornák elemzésekor előfordulhat, hogy nem ismeri a funkciók pontos stílusait és geometriai típusait. A következő minta az egyszerű adatréteg erejét mutatja be egy KML-fájl funkcióinak megjelenítésével. Emellett az egyszerű adatréteg-osztály különböző lehetőségeit is bemutatja.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Az adatrétegek egyszerű beállításai" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"> A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti a toll <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>egyszerű adatrétegének beállításait</a> .
</iframe>


## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Térbeli információk olvasása és írása](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [OGC-Térkép réteg hozzáadása](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Kapcsolódás WFS szolgáltatáshoz](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Alapvető műveletek kihasználása](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Támogatott adatformátum részletei](spatial-io-supported-data-format-details.md)
