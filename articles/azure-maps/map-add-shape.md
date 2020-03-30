---
title: Sokszögréteg hozzáadása térképhez | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan jeleníthet meg sokszöget és több sokszöget a Microsoft Azure Maps Web SDK-ban egy térképen.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9191f054ca3c7374bcbc7bec46573289a512612c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535052"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Sokszögréteg hozzáadása a térképhez

Ez a cikk bemutatja, `Polygon` hogyan `MultiPolygon` jelenítheti meg a térkép geometriáinak és jellemzőjeit egy sokszögréteg használatával. Az Azure Maps Web SDK támogatja a körgeometriák létrehozását is a [kiterjesztett GeoJSON-sémában meghatározottak](extend-geojson.md#circle)szerint. Ezek a körök a térképen való rendereléskor sokszögekké alakulnak át. Az atlaszba tekerve minden jellemző geometria könnyen [frissíthető. Alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) osztály.

## <a name="use-a-polygon-layer"></a>Sokszögréteg használata 

Ha egy sokszögréteg adatforráshoz csatlakozik, és betöltődik a térképre, akkor a terület `Polygon` és a `MultiPolygon` szolgáltatások jelennek meg. Sokszög létrehozásához adja hozzá egy adatforráshoz, és a [Sokszögréteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) használatával adja meg egy sokszögréteggel.

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

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

Az alábbiakban a fenti kód teljes és futó mintája látható.

<br/>

<iframe height='500' scrolling='no' title='Sokszög hozzáadása térképhez ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a Toll <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>sokszög hozzáadása</a> az Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps ( ) térképéhez a CodePen alkalmazásban című <a href='https://codepen.io'>tollat.</a>
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Sokszög és vonalréteg együttes használata

A vonalréteg a sokszögek körvonalának renderelésére szolgál. A következő kódminta az előző példához hasonlóan poligont jelenít meg, de most hozzáad egy vonalréteget. Ez a vonalréteg az adatforráshoz kapcsolódó második réteg.  

<iframe height='500' scrolling='no' title='Sokszög és vonalréteg sokszög hozzáadásához' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>A CodePen alkalmazásban a <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Tollsokszög és a vonalréteg</a> ből a<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>Sokszög</a>az Azure Maps ( ) segítségével adhat hozzá.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Sokszög kitöltése mintával

A sokszög színnel való kitöltésén kívül a sokszöget is használhatja a sokszög kitöltéséhez. Töltsön be egy képmintát a térképkép sprite `fillPattern` erőforrásaiba, majd hivatkozzon erre a képre a sokszögréteg tulajdonságával.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Sokszög kitöltési mintázata" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Toll <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>sokszög kitöltési mintáját</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) szerint a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>


> [!TIP]
> Az Azure Maps webes SDK számos testreszabható képsablont biztosít, amelyeket kitöltési mintaként használhat. További információt a [Képsablonok használata](how-to-use-image-templates-web-sdk.md) című dokumentumban talál.

## <a name="customize-a-polygon-layer"></a>Sokszögréteg testreszabása

A Sokszög rétegnek csak néhány formázási lehetősége van. Itt van egy szerszám -hoz megpróbál őket ki.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg között' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) című témakört a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Kör hozzáadása a térképhez

Az Azure Maps a GeoJSON-séma egy kiterjesztett verzióját használja, amely a körök definícióját tartalmazza, ahogy [az itt](extend-geojson.md#circle)is említettük. Egy kör jelenik meg a `Point` térképen egy funkció létrehozásával. Ez `Point` egy `subType` tulajdonság, `"Circle"` amelynek `radius` értéke és egy olyan tulajdonsága van, amelynek száma a sugarat méterben jelöli. 

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

Az Azure Maps Web SDK `Point` `Polygon` ezeket a funkciókat szolgáltatásokká alakítja. Ezután ezek a jellemzők a térképen sokszög és vonalrétegek használatával jelennek meg, ahogy az a következő kódmintában látható.

<br/>

<iframe height='500' scrolling='no' title='Kör hozzáadása térképhez' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a Toll hozzáadása kör az Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps ( ) <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>térképéhez</a> a <a href='https://codepen.io'>CodePen</a>webhelyen.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>A geometria frissítésének egyszerűvé tétele

Az `Shape` osztály körbefolyatja [a geometriát](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) vagy [a jellemzőt,](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) és megkönnyíti ezeknek a szolgáltatásoknak a frissítését és karbantartását. Alakzatváltozó példányosítani szeretné az alakzatváltozókat, adja át a geometriát vagy a tulajdonságok egy készletét az alakzatkonstrátornak.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

A következő kódminta bemutatja, hogyan kell körbefolyatni egy kör GeoJSON objektumot egy alakzatosztállyal. Ahogy a sugár értéke megváltozik az alakzatban, a kör automatikusan megjelenik a térképen.

<br/>

<iframe height='500' scrolling='no' title='Alakzattulajdonságainak frissítése' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Tollfrissítés</a> alakzat<a href='https://codepen.io/azuremaps'>@azuremaps</a>tulajdonságait az Azure Maps ( ) szerint a <a href='https://codepen.io'>CodePen-en.</a>
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Sokszög](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Sokszögréteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Sokszögréteg beállításai](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

A térképekhez hozzáadni rakandó további kódpéldákat az alábbi cikkekben talál:

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Előugró ablak hozzáadása](map-add-popup.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Rendszerképsablonok használata](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](map-add-line-layer.md)

További források:

> [!div class="nextstepaction"]
> [Az Azure Maps GeoJSON specifikációbővítménye](extend-geojson.md#circle)