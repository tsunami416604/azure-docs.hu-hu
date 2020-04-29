---
title: Vonal rétegének hozzáadása térképhez | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá egy vonal réteget egy térképhez a Microsoft Azure Maps web SDK használatával.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c473be25907eb3a761fbccd598bb9b732e5be5b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80802348"
---
# <a name="add-a-line-layer-to-the-map"></a>Vonal rétegének hozzáadása a térképhez

A vonal réteggel görbékként és `LineString` `MultiLineString` útvonalakként jeleníthető meg a térképen. A sorok `Polygon` és `MultiPolygon` a szolgáltatások körvonalainak megjelenítéséhez egy sor réteg is használható. Az adatforrások egy vonal réteghez csatlakoznak, így biztosítva a rendereléshez szükséges adatforrásokat. 

> [!TIP]
> A sorok alapértelmezés szerint a sokszögek koordinátáit, valamint egy adatforrás sorait jelenítik meg. Ha korlátozni szeretné a réteget úgy, hogy az csak az LineString funkciókat `filter` jelenítse meg, állítsa `['==', ['geometry-type'], 'LineString']` be `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` a réteg tulajdonságát a (vagy) értékre, ha MultiLineString funkciókat is szeretne használni.

A következő kód bemutatja, hogyan lehet sort létrehozni. Adja hozzá a sort egy adatforráshoz, majd jelenítse meg egy vonal réteggel a [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) osztály használatával.

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

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Vonal hozzáadása térképhez' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io'>CodePen</a>-on lévő Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() <a href='https://codepen.io/azuremaps/pen/qomaKv/'>, és adjon hozzá egy sort</a> .
</iframe>

A [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) stílusú és [adatvezérelt stílusú kifejezéseket](data-driven-style-expressions-web-sdk.md)használó vonalak lehetnek.

## <a name="add-symbols-along-a-line"></a>Szimbólumok felvétele a vonal mentén

Ez a minta bemutatja, hogyan adhat hozzá nyíl ikonokat a Térkép egyik sorához. Ha szimbólum réteget használ, állítsa a "elhelyezés" lehetőséget a "line" értékre. Ez a beállítás megjeleníti a jeleket a vonal mentén, és elforgatja az ikonokat (0 fok = jobb).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Nyíl megjelenítése a vonal mentén" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollat <a href='https://codepen.io/azuremaps/pen/drBJwX/'>mutató nyilat a vonal mentén</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> A Azure Maps web SDK számos testreszabható képsablont biztosít, amelyeket a szimbólum rétegben használhat. További információ: a [képsablonokkal](how-to-use-image-templates-web-sdk.md) kapcsolatos dokumentum használata.

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Körvonal átmenetének hozzáadása sorba

Egyetlen ecsetvonás színét is alkalmazhatja egy sorba. A színátmenetes vonalakat is kitöltheti úgy, hogy az egyik vonalszakaszból a következő sorra váltson átmenetet. A vonalak közötti átmenetek például az idő és a távolság, illetve a csatlakoztatott objektumok különböző hőmérsékletei közötti változások ábrázolására használhatók. Ahhoz, hogy a funkciót egy sorba lehessen alkalmazni, az adatforrásnak `lineMetrics` igaz értékűnek kell lennie, majd egy színátmenetes kifejezés is átadható a `strokeColor` sor kapcsolójának. A stroke Gradient kifejezésnek hivatkoznia kell `['line-progress']` arra az adatkifejezésre, amely a számított vonal metrikáit a kifejezésre teszi elérhetővé.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ecsetvonás-átmenetes vonal" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti a tollvonási <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>átmenetet</a> .
</iframe>

## <a name="customize-a-line-layer"></a>Vonal rétegének testreszabása

A vonal rétegének számos stílusa van. Itt látható egy eszköz, amellyel kipróbálhatja őket.

<br/>

<iframe height='700' scrolling='no' title='Vonal rétegének beállításai' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>parancssori rétegbeli beállításait</a> a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

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
