---
title: Buborékréteg hozzáadása térképhez | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá buborékréteget egy térképhez a Microsoft Azure Maps Web SDK használatával.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7ae11734eb804715f3eb1b5edcb02fc328dafec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77208556"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Buborékréteg hozzáadása térképhez

Ez a cikk bemutatja, hogyan jelenítheti meg az adatforrásból származó pontadatokat buborékrétegként a térképen. A buborékrétegek a pontokat körökként renderelik a térképen, rögzített képpontsugarat tartalmazva. 

> [!TIP]
> A buborékrétegek alapértelmezés szerint az adatforrás összes geometriájának koordinátáit jelenítik meg. Ha úgy szeretné korlátozni a réteget, hogy `filter` az csak a `['==', ['geometry-type'], 'Point']` `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` pontgeometriai jellemzőket jelenítse meg, a réteg tulajdonságát a MultiPoint-szolgáltatásokra vagy -e a MultiPoint-szolgáltatásokra is be szeretné állítani.

## <a name="add-a-bubble-layer"></a>Buborékréteg hozzáadása

A következő kód pontok tömbjét tölti be egy adatforrásba. Ezután összeköti az adatpontokat egy [buborékréteggel.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) A buborékréteg minden buborék sugarát öt képponttal és fehér kitöltési színnel jeleníti meg. És egy kék körvonalszín, és egy hat képpont szélességű körvonalszélesség. 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

Az alábbiakban a fenti funkciók teljes futókód-mintája látható.

<br/>

<iframe height='500' scrolling='no' title='BubbleLayer adatforrás' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Címkék megjelenítése buborékréteggel

Ez a kód bemutatja, hogyan használhat buborékréteget egy pont renderelésére a térképen. És hogyan kell használni a szimbólum réteget, hogy egy címkét. A szimbólumréteg ikonjának elrejtéséhez állítsa `image` az `'none'`ikon beállításainak tulajdonságát a számára.

<br/>

<iframe height='500' scrolling='no' title='Többrétegű adatforrás' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-bubble-layer"></a>Buborékréteg testreszabása

A Buborék rétegnek csak néhány formázási lehetősége van. Itt van egy szerszám -hoz megpróbál őket ki.

<br/>

<iframe height='700' scrolling='no' title='Buborékréteg beállításai' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Tollbuborék réteg beállításait</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) segítségével a <a href='https://codepen.io'>CodePen-en.</a>
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Buborékréteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions (Buborékréteg opciók)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

További kódmintákat a térképekhez további kódmintákért tekintse meg:

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Kódminták](https://docs.microsoft.com/samples/browse/?products=azure-maps)
