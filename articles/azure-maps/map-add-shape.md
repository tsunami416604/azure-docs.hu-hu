---
title: Sokszög réteg hozzáadása térképhez | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg a sokszögeket és a több sokszöget a Microsoft Azure Maps web SDK-ban.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 77d952666be12d7dea780b3aa8f094cf5f70f2d3
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911128"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Sokszög réteg hozzáadása a térképhez

Ebből a cikkből megtudhatja, hogyan jelenítheti meg `Polygon` és `MultiPolygon` szolgáltatás geometriáit a térképen a sokszög réteg használatával. A Azure Maps web SDK támogatja a kör alakú geometriák létrehozását is a [kiterjesztett GeoJSON sémában](extend-geojson.md#circle)definiált módon. Ezeket a köröket a rendszer sokszögekre alakítja át a térképen való megjelenítéskor. Az Atlas használatával az összes funkció geometriája is könnyen frissíthető [. Alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) osztálya

## <a name="use-a-polygon-layer"></a>Sokszög réteg használata 

Amikor egy sokszög réteg csatlakozik egy adatforráshoz, és betöltődik a térképen, a `Polygon` és `MultiPolygon` funkciók területét jeleníti meg. A következő kód bemutatja, hogyan hozhat létre egy sokszöget, hogyan adhat hozzá egy adatforráshoz, és hogyan jelenítheti meg egy sokszög réteggel a [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) osztály használatával.

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

<iframe height='500' scrolling='no' title='Sokszög hozzáadása térképhez ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>sokszög hozzáadása a térképhez</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Sokszög és vonal rétegek együttes használata

A sokszögek körvonalának megjelenítéséhez egy vonal réteget lehet használni. A következő mintakód az előző példához hasonló sokszöget jelenít meg, de most egy sor réteget helyez el az adatforráshoz csatlakozó második rétegként.  

<iframe height='500' scrolling='no' title='Sokszög és vonal réteg a sokszög hozzáadásához' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>A sokszög <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>és a vonal rétegének</a> megjelenítéséhez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával adja hozzá a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Sokszög kitöltése mintázattal

A sokszögek színnel való kitöltése mellett egy képmintázat is használható. Helyezzen be egy képmintázatot a Maps-rendszerkép sprite-erőforrásaiba, majd hivatkozzon erre a képre a sokszög réteg `fillPattern` tulajdonságával.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Sokszög kitöltési mintája" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>sokszögének kitöltési mintáját</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> A Azure Maps web SDK számos testreszabható képsablont biztosít, amelyeket kitöltési mintázatként használhat. További információ: a [képsablonokkal](how-to-use-image-templates-web-sdk.md) kapcsolatos dokumentum használata.

## <a name="customize-a-polygon-layer"></a>Sokszög-réteg testreszabása

A sokszög rétegnek csak néhány stílusa van. Itt látható egy eszköz, amellyel kipróbálhatja őket.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a toll <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Kör hozzáadása a térképhez

Azure Maps a GeoJSON séma egy kiterjesztett verzióját használja, amely az [itt](extend-geojson.md#circle)feljegyzett körök definícióját adja meg. A térképen megjeleníthető egy olyan `Point`-szolgáltatás, amelynek `subType` tulajdonsága `"Circle"` értékkel rendelkezik, és egy `radius` tulajdonság, amely egy olyan szám, amely a sugár mértékegységét jelöli. Példa:

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

A Azure Maps web SDK ezeket a `Point` funkciókat `Polygon` funkciókba konvertálja a borítók alatt, és a térképen a sokszög és a vonal rétegek használatával jeleníthető meg, ahogy az alábbi mintakód is mutatja.

<br/>

<iframe height='500' scrolling='no' title='Kör hozzáadása térképhez' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Az <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>, hogyan adhat hozzá kört a térképhez</a> .
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Geometria egyszerű frissítése

A `Shape` osztály egy [geometriát](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) vagy [szolgáltatást](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) csomagol, és megkönnyíti a frissítését és karbantartását. Alakzat hozható létre a geometriában és a tulajdonságok készletében, vagy egy funkciónak a következő kódban látható módon történő átadásával.

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

## <a name="next-steps"></a>Következő lépések

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

További források:

> [!div class="nextstepaction"]
> [Azure Maps GeoJSON specifikációs bővítmény](extend-geojson.md#circle)