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
ms.openlocfilehash: 5b59bdc06d455c7bd0ec9cf889f5cfa382948467
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911185"
---
# <a name="add-a-line-layer-to-the-map"></a>Vonal rétegének hozzáadása a térképhez

A `LineString` és az `MultiLineString` szolgáltatások elérési útjaként és útvonalként is használhatók a térképen. Az `Polygon` és az `MultiPolygon` szolgáltatások körvonalainak megjelenítéséhez egy sor réteget is használhat. Az adatforrások egy vonal réteghez csatlakoznak, így biztosítva az adatmegjelenítést. 

> [!TIP]
> A sorok alapértelmezés szerint a sokszögek koordinátáit, valamint egy adatforrás sorait jelenítik meg. Ha korlátozni szeretné a réteget úgy, hogy az csak az LineString funkciókat jelenítse meg, állítsa a réteg `filter` tulajdonságát `['==', ['geometry-type'], 'LineString']`re vagy `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]`ra, ha MultiLineString-funkciókat is szeretne felvenni.

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

<iframe height='500' scrolling='no' title='Vonal hozzáadása térképhez' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Az <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti <a href='https://codepen.io/azuremaps/pen/qomaKv/'>, hogyan adhat hozzá egy sort egy térképhez</a> .
</iframe>

A [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) stílusú és [adatvezérelt stílusú kifejezéseket](data-driven-style-expressions-web-sdk.md)használó vonalak lehetnek.

## <a name="add-symbols-along-a-line"></a>Szimbólumok felvétele a vonal mentén

Ez a minta bemutatja, hogyan adhat hozzá nyíl ikonokat a Térkép egyik sorához. Ha szimbólum réteget használ, állítsa a "elhelyezés" lehetőséget a "line" értékre, ez a szimbólum a vonal mentén jelenik meg, és elforgatja az ikonokat (0 fok = jobbra).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Nyíl megjelenítése a vonal mentén" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollat <a href='https://codepen.io/azuremaps/pen/drBJwX/'>mutató nyilat a vonal mentén</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> A Azure Maps web SDK számos testreszabható képsablont biztosít, amelyeket a szimbólum rétegben használhat. További információ: a [képsablonokkal](how-to-use-image-templates-web-sdk.md) kapcsolatos dokumentum használata.

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Körvonal átmenetének hozzáadása sorba

Amellett, hogy egyetlen ecsetvonási színt is alkalmazhat egy sorba, egy színátmenetes vonalat is kitölthet az egyik vonalszakasz és a következő közötti átmenet megjelenítéséhez. A vonalak közötti átmenetek például az idő és a távolság, illetve a csatlakoztatott objektumok különböző hőmérsékletei közötti változások ábrázolására használhatók. Ahhoz, hogy a funkciót egy sorba lehessen alkalmazni, az adatforrásnak a `lineMetrics` beállítás értéke TRUE (igaz) kell legyen, és a színátmenetes kifejezés átadható a vonal `strokeColor` beállításnak. A stroke Gradient kifejezésnek a `['line-progress']` adatkifejezésre kell hivatkoznia, amely a számított vonal metrikáit a kifejezésre teszi elérhetővé.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ecsetvonás-átmenetes vonal" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti a tollvonási <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>átmenetet</a> .
</iframe>

## <a name="customize-a-line-layer"></a>Vonal rétegének testreszabása

A vonal rétegének több stílusa is van. Itt látható egy eszköz, amellyel kipróbálhatja őket.

<br/>

<iframe height='700' scrolling='no' title='Vonal rétegének beállításai' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti a toll <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>vonal rétegének beállításait</a> .
</iframe>

## <a name="next-steps"></a>Következő lépések

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