---
title: Sokszög-kihúzási réteg hozzáadása térképhez | Microsoft Azure térképek
description: Sokszög-kihúzási réteg hozzáadása a Microsoft Azure Maps web SDK-hoz.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f01e07ea2bbfd0f6b3b0cc19dd219d71984a0d45
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988566"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Sokszög-kihúzási réteg hozzáadása a térképhez

Ebből a cikkből megtudhatja, hogyan használhatja a sokszög-kihúzási réteget a `Polygon` és a `MultiPolygon` szolgáltatásbeli geometriák kihúzott alakzatként való megjelenítéséhez. A Azure Maps web SDK támogatja a kör geometriáinak megjelenítését a [kiterjesztett GeoJSON sémában](extend-geojson.md#circle)meghatározottak szerint. Ezek a körök a térképen való megjelenítéskor sokszögekre alakíthatók. Az Atlas becsomagolásakor az összes funkció geometriája könnyen frissíthető [. Alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) osztálya

## <a name="use-a-polygon-extrusion-layer"></a>Sokszög kihúzási rétegének használata

A [sokszög kihúzási rétegének](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) összekötése egy adatforrással. Ezután betöltve a térképen. A sokszög kihúzási rétege lenyomott alakzatként jeleníti meg egy `Polygon` és `MultiPolygon` szolgáltatás területeit. A sokszög kihúzási rétegének `height` és `base` tulajdonságai határozzák meg az extrudált alakzat területének és magasságának alaptávolságát **méterben**. A következő kód bemutatja, hogyan hozható létre sokszög, hogyan adható hozzá egy adatforráshoz, és Hogyan jeleníthető meg a sokszög kihúzási rétegének osztálya alapján.

> [!Note]
> A sokszög-kihúzási rétegben definiált `base` érték nem lehet kisebb, mint a `height`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Extrudált sokszög" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>extrudált sokszögét</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-polygons"></a>Adatvezérelt sokszögek hozzáadása

A choropleth-leképezések a sokszög kihúzási réteg használatával állíthatók be. Állítsa be a kihúzási réteg `height` és `fillColor` tulajdonságait a `Polygon` és `MultiPolygon` szolgáltatás geometriájában található statisztikai változó mértékére. A következő mintakód egy extrudált choropleth-Térképet mutat be az U. S alapján, amely a populáció sűrűségének az állapot szerinti mérésén alapul.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Extrudált choropleth Térkép" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/eYYYNox'>extrudált choropleth térképét</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Kör hozzáadása a térképhez

Azure Maps a GeoJSON séma egy kiterjesztett verzióját használja, amely az [itt](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle)feljegyzett körök definícióját adja meg. Az extrudált kör megjeleníthető a térképen úgy, hogy létrehoz egy `point` funkciót, amelynek `subType` tulajdonsága `Circle`, és egy számozott `Radius` **tulajdonság, amely**a sugarat jelöli. Példa:

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

A Azure Maps web SDK ezeket a `Point` funkciókat a motorháztető alatti `Polygon` funkciókra konvertálja. Ezek a `Point` funkciók a térképen a sokszög-kihúzási réteg használatával jeleníthetők meg, ahogy az alábbi mintakód is mutatja.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="A drone-légtér sokszöge" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>drone-légtér sokszögét</a> .
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Sokszög kihúzási rétegének testreszabása

A sokszög kihúzási rétegének számos stílusa van. Itt látható egy eszköz, amellyel kipróbálhatja őket.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a toll <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [sokszög kihúzási rétege](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

További források:

> [!div class="nextstepaction"]
> [Azure Maps GeoJSON specifikációs bővítmény](extend-geojson.md#circle)
