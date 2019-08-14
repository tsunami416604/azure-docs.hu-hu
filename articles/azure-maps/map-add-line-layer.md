---
title: Sorok hozzáadása a Azure Mapshoz | Microsoft Docs
description: Soros réteg hozzáadása a Azure Maps web SDK-hoz.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f07e36d82c9044a212cda8173df9fe0a9544393a
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977325"
---
# <a name="add-a-line-layer-to-the-map"></a>Vonal rétegének hozzáadása a térképhez

A vonal réteggel görbékként és `LineString` `MultiLineString` útvonalakként jeleníthető meg a térképen. A sorok `Polygon` és `MultiPolygon` a szolgáltatások körvonalainak megjelenítéséhez egy sor réteg is használható. Az adatforrások egy vonal réteghez csatlakoznak, így biztosítva az adatmegjelenítést. 

> [!TIP]
> A sorok alapértelmezés szerint a sokszögek koordinátáit, valamint egy adatforrás sorait jelenítik meg. Ha korlátozni szeretné a réteget úgy, hogy az csak az LineString funkciókat `filter` jelenítse meg, állítsa `['==', ['geometry-type'], 'LineString']` be `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` a réteg tulajdonságát a (vagy) értékre, ha MultiLineString funkciókat is szeretne használni.

A következő kód bemutatja, hogyan hozhat létre egy vonalat, hogyan adhat hozzá egy adatforráshoz, és hogyan jelenítheti meg egy vonal réteggel a [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) osztály használatával.

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

A [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) stílusú és adatvezérelt [stílusú kifejezéseket](data-driven-style-expressions-web-sdk.md)használó vonalak lehetnek.

## <a name="add-symbols-along-a-line"></a>Szimbólumok felvétele a vonal mentén

Ez a minta bemutatja, hogyan adhat hozzá nyíl ikonokat a Térkép egyik sorához. Ha szimbólum réteget használ, állítsa a "elhelyezés" lehetőséget a "line" értékre, ez a szimbólum a vonal mentén jelenik meg, és elforgatja az ikonokat (0 fok = jobbra).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Nyíl megjelenítése a vonal mentén" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollat mutató nyilat a<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/drBJwX/'>vonal mentén</a> Azure Maps () alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> A Azure Maps web SDK számos testreszabható képsablont biztosít, amelyeket a szimbólum rétegben használhat. További információ: a képsablonokkal kapcsolatos dokumentum [használata](how-to-use-image-templates-web-sdk.md) .

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Körvonal átmenetének hozzáadása sorba

Amellett, hogy egyetlen ecsetvonási színt is alkalmazhat egy sorba, egy színátmenetes vonalat is kitölthet az egyik vonalszakasz és a következő közötti átmenet megjelenítéséhez. A vonalak közötti átmenetek például az idő és a távolság, illetve a csatlakoztatott objektumok különböző hőmérsékletei közötti változások ábrázolására használhatók. Ahhoz, hogy a funkciót egy sorba lehessen alkalmazni, az adatforrásnak `lineMetrics` igaz értékűnek kell lennie, majd egy színátmenetes kifejezés is átadható `strokeColor` a sor kapcsolójának. A stroke Gradient kifejezésnek hivatkoznia kell `['line-progress']` arra az adatkifejezésre, amely a számított vonal metrikáit a kifejezésre teszi elérhetővé.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ecsetvonás-átmenetes vonal" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
A <a href='https://codepen.io'>CodePen</a>- <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'></a> on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti a tollvonási átmenetet.
</iframe>

## <a name="customize-a-line-layer"></a>Vonal rétegének testreszabása

A vonal rétegének több stílusa is van. Itt látható egy eszköz, amellyel kipróbálhatja őket.

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
> [Felugró ablak hozzáadása](map-add-popup.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stílusú kifejezések használata](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [A képsablonok használata](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Sokszög réteg hozzáadása](map-add-shape.md)