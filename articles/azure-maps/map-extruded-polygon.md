---
title: Sokszögkihúzási réteg hozzáadása a térképhez | Microsoft Azure Maps
description: Többszögkihúzási réteg hozzáadása a Microsoft Azure Maps Web SDK-hoz.
author: philmea
ms.author: philmea
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7405098bd4924333aafcd1c285eb2f37bb1d4f75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334544"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Sokszögkihúzási réteg hozzáadása a térképhez

Ez a cikk bemutatja, hogyan használhatja a sokszögkihúzási réteget a geometriák területeinek `Polygon` extrudált alakzatként való renderelésére és `MultiPolygon` megjelenítésére. Az Azure Maps Web SDK támogatja a Kör geometriák megjelenítését a [kiterjesztett GeoJSON-sémában meghatározottak](extend-geojson.md#circle)szerint. Ezek a körök a térképen való rendereléskor sokszögekké alakíthatók. Az atlaszba tekerve minden jellemző geometria könnyen [frissíthető. Alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) osztály.

## <a name="use-a-polygon-extrusion-layer"></a>Sokszögkihúzási réteg használata

Kapcsolja össze a [sokszögkihúzási réteget](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) egy adatforrással. Aztán felraktam a térképre. A sokszögkihúzási réteg a területeket `Polygon` `MultiPolygon` és jellemzőket extrudált alakzatként jeleníti meg. A `height` `base` sokszögextrudálási réteg és tulajdonságai **méterben**határozzák meg az extrudált alakzat talajtól való alaptávolságát és magasságát . A következő kód bemutatja, hogyan hozhat létre sokszöget, hogyan adhat hozzá egy adatforráshoz, és hogyan jelenítheti meg azt a Sokszög kihúzási réteg osztályával.

> [!Note]
> A `base` sokszögkihúzási rétegben meghatározott értéknek kisebbnek vagy egyenlőnek `height`kell lennie a értékével.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Kihúzta sokszög" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>Extruded sokszöget</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) segítségével a <a href='https://codepen.io'>CodePen webhelyen.</a></iframe>


## <a name="add-data-driven-polygons"></a>Adatvezérelt sokszögek hozzáadása

A choropleth térkép a sokszög extrudálási réteg használatával renderelhető. Állítsa `height` be `fillColor` az extrudálási réteg és tulajdonságainak `Polygon` mérését a statisztikai változó és `MultiPolygon` a jellemző geometriákban. A következő kódminta egy extrudált choropleth térképet mutat az Egyesült Államokból, a népsűrűség államonkénti mérése alapján.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Extrudált keplezőtérkép" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg az Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) Pen <a href='https://codepen.io/azuremaps/pen/eYYYNox'>Extruded choropleth térképét</a> a <a href='https://codepen.io'>CodePen oldalon.</a>
</iframe>

## <a name="add-a-circle-to-the-map"></a>Kör hozzáadása a térképhez

Az Azure Maps a GeoJSON-séma egy kiterjesztett verzióját használja, amely a körök definícióját tartalmazza [az itt](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle)jelzettek szerint. Az extrudált kör renderelhető a `point` térképen `subType` egy `Circle` olyan tulajdonsággal `Radius` és egy számozott tulajdonsággal, amely a sugarat **jelöli méterben**. Példa:

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

Az Azure Maps Web SDK `Point` `Polygon` ezeket a funkciókat a motorháztető alatti funkciókká alakítja. Ezek `Point` a funkciók a térképen a sokszögextrudálási réteg használatával renderelhetők, ahogy az a következő kódmintában látható.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Drone légtér sokszög" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Pen Drone légtér<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>sokszög</a> az Azure Maps ( ) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Sokszögkihúzási réteg testreszabása

A Sokszög extrudálási réteg számos formázási lehetőséggel rendelkezik. Itt van egy szerszám -hoz megpróbál őket ki.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ között' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alkalmazást a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Sokszög](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [sokszögkihúzási réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

További források:

> [!div class="nextstepaction"]
> [Az Azure Maps GeoJSON specifikációbővítménye](extend-geojson.md#circle)
