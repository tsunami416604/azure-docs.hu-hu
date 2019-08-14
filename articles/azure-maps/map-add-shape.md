---
title: Sokszög réteg hozzáadása a Azure Mapshoz | Microsoft Docs
description: Sokszög réteg hozzáadása a Azure Maps web SDK-hoz.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ca6c0f5e6fde5a31655ed17f4a016bf44216643f
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976142"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Sokszög réteg hozzáadása a térképhez

Ez a cikk bemutatja, hogyan jelenítheti meg a `Polygon` Térkép `MultiPolygon` területeit és funkcióit a térképen a sokszög réteg használatával. A Azure Maps web SDK támogatja a kör alakú geometriák létrehozását is a [kiterjesztett GeoJSON sémában](extend-geojson.md#circle)definiált módon. Ezeket a köröket a rendszer sokszögekre alakítja át a térképen való megjelenítéskor. Az Atlas használatával az összes funkció geometriája is könnyen frissíthető [. Alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) osztálya

## <a name="use-a-polygon-layer"></a>Sokszög réteg használata 

Ha egy sokszög réteg egy adatforráshoz csatlakozik, és betöltődik a térképen, az megjeleníti a `Polygon` és `MultiPolygon` a funkciók területét. A következő kód bemutatja, hogyan hozhat létre egy sokszöget, hogyan adhat hozzá egy adatforráshoz, és hogyan jelenítheti meg egy sokszög réteggel a [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) osztály használatával.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    opacaty: 0.5
}));
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Sokszög hozzáadása térképhez ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>sokszög hozzáadása</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Sokszög és vonal rétegek együttes használata

A sokszögek körvonalának megjelenítéséhez egy vonal réteget lehet használni. A következő mintakód az előző példához hasonló sokszöget jelenít meg, de most egy sor réteget helyez el az adatforráshoz csatlakozó második rétegként.  

<iframe height='500' scrolling='no' title='Sokszög és vonal réteg a sokszög hozzáadásához' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Az <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával vegye fel a sokszöget a toll <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>és a vonal rétegben</a> .
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Sokszög kitöltése mintázattal

A sokszögek színnel való kitöltése mellett egy képmintázat is használható. Helyezzen be egy képmintázatot a Maps-rendszerkép sprite-erőforrásaiba, majd hivatkozzon `fillPattern` erre a képre a sokszög rétegének tulajdonságával.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Sokszög kitöltési mintája" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll sokszögének kitöltésére szolgáló <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>mintát</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> A Azure Maps web SDK számos testreszabható képsablont biztosít, amelyeket kitöltési mintázatként használhat. További információ: a képsablonokkal kapcsolatos dokumentum [használata](how-to-use-image-templates-web-sdk.md) .

## <a name="customize-a-polygon-layer"></a>Sokszög-réteg testreszabása

A sokszög rétegnek csak néhány stílusa van. Itt látható egy eszköz, amellyel kipróbálhatja őket.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg <a href='https://codepen.io/azuremaps/pen/LXvxpg/'></a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) által LXvxpg tollat a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Kör hozzáadása a térképhez

Azure Maps a GeoJSON séma egy kiterjesztett verzióját használja, amely az [itt](extend-geojson.md#circle)feljegyzett körök definícióját adja meg. Egy kör megjeleníthető a térképen egy `Point` olyan szolgáltatás létrehozásával, amely egy értékkel rendelkező `subType` `"Circle"` tulajdonságot tartalmaz, valamint egy `radius` olyan tulajdonságot, amely egy szám, amely a sugarat jelöli a mérőórákban. Példa:

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

A Azure Maps web SDK ezeket `Point` a `Polygon` funkciókat a borítók alatt lévő funkciókba konvertálja, és a térképen a sokszög és a vonal rétegek használatával jeleníthető meg, ahogy az alábbi mintakód is mutatja.

<br/>

<iframe height='500' scrolling='no' title='Kör hozzáadása térképhez' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io'>CodePen</a>-on lévő Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>, és adjon hozzá egy kört egy térképhez</a> .
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Geometria egyszerű frissítése

Egy `Shape` osztály egy geometriát [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) vagy [szolgáltatást](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) csomagol, és megkönnyíti a frissítését és karbantartását. Alakzat hozható létre a geometriában és a tulajdonságok készletében, vagy egy funkciónak a következő kódban látható módon történő átadásával.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

A következő mintakód bemutatja, hogyan lehet egy kör alakú GeoJSON objektumot egy Shape osztályba becsomagolni, és egyszerűen frissíteni a RADIUS-tulajdonságot egy csúszka használatával. Ahogy a sugár értéke megváltozik az alakzatban, a kör renderelése automatikusan frissül a térképen.

<br/>

<iframe height='500' scrolling='no' title='Alakzat tulajdonságainak frissítése' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>frissítési alakzatának tulajdonságait</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

A térképekhez hozzáadandó további példákat a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Felugró ablak hozzáadása](map-add-popup.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stílusú kifejezések használata](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [A képsablonok használata](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Vonal rétegének hozzáadása](map-add-line-layer.md)

További erőforrások:

> [!div class="nextstepaction"]
> [Azure Maps GeoJSON specifikációs bővítmény](extend-geojson.md#circle)