---
title: Egyszerű adatréteg hozzáadása | Microsoft Azure Maps
description: Ismerje meg, hogyan adhat hozzá egy egyszerű adatréteget az Azure Maps Web SDK által biztosított térbeli I/o-modul használatával.
author: philmea
ms.author: philmea
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8862c33b7660b8130f692dc4beea89a7b6b5f5ad
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804486"
---
# <a name="add-a-simple-data-layer"></a>Egyszerű adatréteg hozzáadása

A térbeli IO `SimpleDataLayer` modul egy osztályt biztosít. Ez az osztály megkönnyíti a stílusú funkciók renderelését a térképen. Még olyan adatkészleteket is renderelhet, amelyek stílustulajdonságokkal és vegyes geometriatípusokat tartalmazó adatkészleteket tartalmaznak. Az egyszerű adatréteg ezt a funkciót több renderelési réteg tördelésével és stíluskifejezések használatával éri el. A stíluskifejezések a burkolt rétegeken belüli jellemzők közös stílustulajdonságait keresik. A `atlas.io.read` függvény `atlas.io.write` és a függvény ezeket a tulajdonságokat használja a stílusok támogatott fájlformátumba olvasásához és írásához. Miután hozzáadja a tulajdonságokat egy támogatott fájlformátumhoz, a fájl különböző célokra használható. A fájl például a stílusú funkciók megjelenítésére használható a térképen.

A stílusfunkciók mellett `SimpleDataLayer` a beépített előugró funkció egy felugró sablonnal is rendelkezik. A felugró ablak akkor jelenik meg, amikor egy funkcióra kattintanak. Az alapértelmezett előugró szolgáltatás szükség esetén letiltható. Ez a réteg a fürtözött adatokat is támogatja. Amikor egy fürtre kattintanak, a térkép ráközelít a fürtre, és kibontja azt az egyes pontokra és alhalmazokra.

Az `SimpleDataLayer` osztály nagy adatkészleteken való használatra készült, sok geometriatípussal és számos stílussal a jellemzőkön. Használat esetén ez az osztály hat rétegből áll, amelyek stíluskifejezéseket tartalmaznak. Tehát vannak olyan esetek, amikor hatékonyabb a mag renderelési rétegek használata. Például egy magréteg segítségével renderelhet néhány geometriatípust és néhány stílust egy jellemzőn

## <a name="use-a-simple-data-layer"></a>Egyszerű adatréteg használata

Az `SimpleDataLayer` osztály ugyanúgy használatos, mint a többi renderelési réteg. Az alábbi kód bemutatja, hogyan kell használni egy egyszerű adatréteget a térképen:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Szolgáltatások hozzáadása az adatforráshoz. Ezután az egyszerű adatréteg kitalálja, hogyan lehet a legjobban megjeleníteni a funkciókat. Az egyes szolgáltatások stílusai a szolgáltatás tulajdonságaiként állíthatók be. A következő kód egy GeoJSON-pontjellemzőt mutat be, `color` amelynek tulajdonsága a. `red` 

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

A következő kód a fenti pontjellemzőt az egyszerű adatréteg használatával jeleníti meg. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Az Egyszerű adatréteg használata" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true"> Lásd: A Toll használata az Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps egyszerű <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>adatrétege</a> ( ) segítségével a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

Az egyszerű adatréteg valódi ereje akkor jön létre, amikor:

- Az adatforrások számos különböző típusú szolgáltatást tartalmaznak; Vagy
- Az adatkészlet szolgáltatásainak több stílustulajdonsága van külön-külön beállítva; Vagy
- Nem biztos benne, hogy pontosan mit tartalmaz az adatkészlet.

Xml-adatcsatornák elemzésekor például előfordulhat, hogy nem ismeri a jellemzők pontos stílusait és geometriatípusait. A következő minta az egyszerű adatréteg erejét mutatja be a KML-fájl funkcióinak renderelésével. Azt is bemutatja, hogy az egyszerű adatréteg-osztály különböző lehetőségeket biztosít.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Egyszerű adatréteg-beállítások" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>Simple adatréteg beállításait</a> az Azure Maps ben (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen-en.</a>
</iframe>


> [!NOTE]
> Ez az egyszerű adatréteg az [előugró sablonosztályt](map-add-popup.md#add-popup-templates-to-the-map) használja a KML-buborékok vagy szolgáltatástulajdonságok táblázatként való megjelenítéséhez. Alapértelmezés szerint a felugró ablakban megjelenített összes tartalom biztonsági szolgáltatásként az iframe-en belül lesz. Vannak azonban korlátozások:
>
> - Minden parancsfájl, űrlap, mutatózárolás és felső navigációs funkció le van tiltva. A hivatkozások a kattintáskor új lapon nyílhatnak meg. 
> - Azok a régebbi böngészők, `srcdoc` amelyek nem támogatják a paramétert az iframe-eken, kis mennyiségű tartalom megjelenítésére korlátozódnak.
> 
> Ha megbízik a felugró ablakokba betöltött adatokban, és potenciálisan azt szeretné, hogy ezek a parancsfájlok előugró ablakokba töltődjenek be, akkor ezt letilthatja az előugró sablonok `sandboxContent` beállításfalsera állításával. 

## <a name="default-supported-style-properties"></a>Alapértelmezett támogatott stílustulajdonságok

Ahogy korábban említettük, az egyszerű adatréteg több alapvető renderelési réteget is körbeölel: buborékot, szimbólumot, vonalat, sokszöget és extrudált sokszöget. Ezután kifejezéseket használ az egyes szolgáltatások érvényes stílustulajdonságainak kereséséhez.

Az Azure Maps és a GitHub-stílus tulajdonságai a támogatott tulajdonságnevek két fő készlete. A különböző azure-leképezések legtöbb tulajdonságneve támogatja az egyszerű adatréteg jellemzőinek stílustulajdonságait. Kifejezések et adtak hozzá néhány rétegbeállításhoz a GitHub által gyakran használt stílustulajdonság-nevek támogatásához. Ezeket a tulajdonságneveket a [GitHub GeoJSON térképtámogatása](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)határozza meg, és a platformon tárolt és renderelt GeoJSON-fájlok stílusához használják őket. A GitHub összes formázási tulajdonsága támogatott az egyszerű `marker-symbol` adatrétegben, kivéve a formázási tulajdonságokat.

Ha az olvasó egy kevésbé gyakori stílustulajdonsággal találkozik, akkor azt a legközelebbi Azure Maps-stílustulajdonsággá alakítja át. Ezenkívül az alapértelmezett stíluskifejezések felülbírálhatók az `getLayers` egyszerű adatréteg funkciójának használatával, és frissíthetik a beállításokat bármelyik rétegen.

A következő szakaszok az egyszerű adatréteg által támogatott alapértelmezett stílustulajdonságok részleteit ismertetik. A támogatott tulajdonságnév sorrendje is a tulajdonság prioritása. Ha ugyanahhoz a rétegbeállításhoz két stílustulajdonság van definiálva, akkor a lista első elemének magasabb az elsőbbsége.

### <a name="bubble-layer-style-properties"></a>Buborékréteg-stílus tulajdonságai

Ha egy szolgáltatás `Point` a `MultiPoint`vagy a , és `image` a funkció nem rendelkezik olyan tulajdonsággal, amelyet egyéni ikonként használna a pont szimbólumként való megjelenítéséhez, akkor a szolgáltatás egy `BubbleLayer`.

| Réteg beállítás | Támogatott tulajdonságnév(ek) | Alapértelmezett érték |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1,</sup> `marker-size` <sup>2,</sup> `scale` <sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] `size` A `scale` és az értékek skaláris értékeknek számítanak, és szorozni`8`

\[2\] Ha a `marker-size` GitHub beállítás meg van adva, akkor a következő értékeket fogja használni a sugárhoz.

| Jelölő mérete | Sugár |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

A fürtök a buborékréteg használatával is renderelnek. Alapértelmezés szerint a fürt sugara `16`. A fürt színe a fürtben az alábbi pontok számától függően változik:

| Pontok száma | Color    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt;10     | `green`  |

### <a name="symbol-style-properties"></a>Szimbólumstílus tulajdonságai

Ha egy szolgáltatás `Point` a `MultiPoint`vagy a , `image` és a funkció, és van egy tulajdonság, amely et kell használni, mint `SymbolLayer`egy egyéni ikon, hogy a pont, mint egy szimbólum, akkor a szolgáltatás jelenik meg a .

| Réteg beállítás | Támogatott tulajdonságnév(ek) | Alapértelmezett érték |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size` <sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] Ha a `marker-size` GitHub beállítás meg van adva, akkor a következő értékeket fogja használni az ikon méretbeállításhoz.

| Jelölő mérete | Szimbólumméret |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Ha a pontszolgáltatás fürt, `point_count_abbreviated` a tulajdonság szöveges címkeként jelenik meg. A program nem jelenít meg képet.

### <a name="line-style-properties"></a>Vonalstílus tulajdonságai

Ha a szolgáltatás `LineString` `MultiLineString`a `Polygon`, `MultiPolygon`, , vagy , akkor `LineLayer`a szolgáltatás egy .

| Réteg beállítás | Támogatott tulajdonságnév(ek) | Alapértelmezett érték |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Sokszög stílustulajdonságai

Ha a szolgáltatás `Polygon` a `MultiPolygon`vagy a , és a `height` szolgáltatás `height` vagy nem rendelkezik tulajdonsággal, vagy `PolygonLayer`a tulajdonság nulla, akkor a szolgáltatás a .

| Réteg beállítás | Támogatott tulajdonságnév(ek) | Alapértelmezett érték |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Kihúzta sokszög stílusának tulajdonságai

Ha a szolgáltatás `Polygon` a `MultiPolygon`vagy a `height` , és 0-nál nagyobb értékű tulajdonsággal `PolygonExtrusionLayer`rendelkezik, a szolgáltatás a .

| Réteg beállítás | Támogatott tulajdonságnév(ek) | Alapértelmezett érték |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

További kódmintákat a térképekhez további kódmintákért tekintse meg:

> [!div class="nextstepaction"]
> [Téradatok olvasása és írása](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [OGC-térképréteg hozzáadása](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Csatlakozás WFS-szolgáltatáshoz](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Az alapvető műveletek kihasználása](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Támogatott adatformátum részletei](spatial-io-supported-data-format-details.md)
