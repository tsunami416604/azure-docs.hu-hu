---
title: Vonalréteg hozzáadása térképhez | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá vonalréteget egy térképhez a Microsoft Azure Maps Web SDK használatával.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8503b12be628fe7d5651221c9d0379bee3e292bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76933467"
---
# <a name="add-a-line-layer-to-the-map"></a>Vonalréteg hozzáadása a térképhez

A vonalréteg használható a `LineString` `MultiLineString` térképen elérési utakként vagy útvonalakként való renderelésre és jellemzőkre. A vonalréteg a körvonal `Polygon` és `MultiPolygon` a jellemzők megjelenítésére is használható. Az adatforrás egy vonalréteghez van csatlakoztatva, hogy a rendereléshez szükséges adatokat biztosítsa. 

> [!TIP]
> A vonalrétegek alapértelmezés szerint a sokszögek koordinátáit és az adatforrás sorait jelenítik meg. A réteg korlátozásához, hogy csak a LineString funkciókat jelenítse meg, a `filter` réteg tulajdonságát a MultiLineString szolgáltatásokra `['==', ['geometry-type'], 'LineString']` állítja, vagy `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` ha a MultiLineString szolgáltatásokat is fel szeretné venni.

A következő kód bemutatja, hogyan lehet sort létrehozni. Adja hozzá a vonalat egy adatforráshoz, majd a [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) osztály használatával egy vonalréteggel renderelje le.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

Az alábbiakban a fenti funkciók teljes futókód-mintája látható.

<br/>

<iframe height='500' scrolling='no' title='Vonal hozzáadása térképhez' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Toll: Vonal hozzáadása</a> az<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) térképéhez a <a href='https://codepen.io'>CodePen alkalmazásban.</a>
</iframe>

A vonalrétegek a [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) és [az Adatalapú stíluskifejezések használatával](data-driven-style-expressions-web-sdk.md)stildikáltak.

## <a name="add-symbols-along-a-line"></a>Szimbólumok hozzáadása vonal mentén

Ez a minta bemutatja, hogyan lehet nyílikonokat hozzáadni a térkép egy vonala mentén. Ha szimbólumréteget használ, állítsa az "elhelyezés" opciót "vonal" beállításra. Ez a beállítás a szimbólumokat a vonal mentén jeleníti meg, és elforgatja az ikonokat (0 fokkal = jobbra).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Nyíl megjelenítése a vonal mentén" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a <a href='https://codepen.io/azuremaps/pen/drBJwX/'>Tollmegjelenítése nyilat</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) vonala mentén a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

> [!TIP]
> Az Azure Maps webes SDK számos testreszabható képsablont biztosít, amelyeket a szimbólumréteggel használhat. További információt a [Képsablonok használata](how-to-use-image-templates-web-sdk.md) című dokumentumban talál.

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Körvonal színátmenet hozzáadása vonalhoz

Egy vonalra egy körvonalszínt alkalmazhat. A vonalat színátmenettel is kitöltheti, így az egyik vonalszakaszról a következő vonalszakaszra való átmenet jelenik meg. A vonalszínátmenetek például az idő beli és távolságbeli változások, illetve a csatlakoztatott objektumvonal különböző hőmérsékleteinek ábrázolására használhatók. Ahhoz, hogy ezt a szolgáltatást egy sorra `lineMetrics` alkalmazhassa, az adatforrásnak igaz beállítással `strokeColor` kell rendelkeznie, majd színátmenet-kifejezés adható át a vonal beállításának. A körvonal színátmenet-kifejezésnek `['line-progress']` hivatkoznia kell arra az adatkifejezésre, amely a számított vonalmérőszámokat a kifejezésnek teszi ki.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vonal körvonal színátmenettel" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>Tollvonal körvonalszínátmenetet</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Vonalréteg testreszabása

A Vonal réteg számos formázási lehetőséggel rendelkezik. Itt van egy szerszám -hoz megpróbál őket ki.

<br/>

<iframe height='700' scrolling='no' title='Vonalréteg beállításai' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen Line<a href='https://codepen.io/azuremaps'>@azuremaps</a>Layer <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Options</a> by Azure Maps ( ) című témakört a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [LineLayer között](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

További kódmintákat a térképekhez további kódmintákért tekintse meg:

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Előugró ablak hozzáadása](map-add-popup.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Rendszerképsablonok használata](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](map-add-shape.md)
