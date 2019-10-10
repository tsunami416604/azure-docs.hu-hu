---
title: Kihúzási sokszög réteg hozzáadása a Azure Mapshoz | Microsoft Docs
description: Kihúzási sokszög réteg hozzáadása a Azure Maps web SDK-hoz.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: fdb5e2b78d9e5817c5a5d139cdf0b34744ed011f
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170710"
---
# <a name="add-an-extrusion-polygon-layer-to-the-map"></a>Kihúzási sokszög réteg hozzáadása a térképhez

Ebből a cikkből megtudhatja, hogyan használhatja a sokszög-kihúzási réteget a `Polygon` és a `MultiPolygon` funkciók területének megjelenítéséhez a Térkép kihúzott alakzataiként. A Azure Maps web SDK támogatja a kör alakú geometriák létrehozását is a [kiterjesztett GeoJSON sémában](extend-geojson.md#circle)definiált módon. Ezeket a köröket a rendszer sokszögekre alakítja át a térképen való megjelenítéskor. Az Atlas használatával az összes funkció geometriája is könnyen frissíthető [. Alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) osztálya


## <a name="use-a-polygon-extrusion-layer"></a>Sokszög kihúzási rétegének használata

Ha egy [sokszög-kihúzási réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) csatlakozik az adatforráshoz, és betöltődik a térképen, a `Polygon` és a `MultiPolygon` funkciók területét kihúzott alakzatként jeleníti meg. A sokszög kihúzási rétegének `height` és `base` tulajdonságai határozzák meg az extrudált alakzat talajszinttől és magasságtól mért távolságát **méterben**. A következő kód bemutatja, hogyan hozhat létre egy sokszöget, hogyan adhat hozzá egy adatforráshoz, és hogyan jelenítheti meg a sokszög kihúzási réteg osztályának használatával.

> [!Note]
> A sokszög-kihúzási rétegben definiált @no__t – 0 érték nem lehet kisebb, mint a `height`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Extrudált sokszög" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
A toll <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>extrudált sokszögét</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával tekintheti meg a <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-multipolygons"></a>Adatvezérelt többsokszögű tárolók hozzáadása

A choropleth-leképezés a sokszög-kihúzási réteg használatával jeleníthető meg, ha a `height` és a `fillColor` tulajdonságokat a `Polygon` és a `MultiPolygon` funkció geometriájában lévő statisztikai változó mértékével arányosan állítja be. A következő mintakód egy extrudált choropleth-Térképet mutat be az U. S alapján, amely a populáció sűrűségének az állapot szerinti mérésén alapul.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Extrudált choropleth Térkép" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/eYYYNox'>extrudált choropleth térképét</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Kör hozzáadása a térképhez

Azure Maps a GeoJSON séma egy kiterjesztett verzióját használja, amely az [itt](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle)feljegyzett körök definícióját adja meg. Az extrudált kör megjeleníthető a térképen úgy, hogy létrehoz egy `point` funkciót a `Circle` `subType` tulajdonságával, valamint egy számozott `Radius` tulajdonságot, amely a sugarat **méterben**jelöli. Példa:

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

A Azure Maps web SDK ezeket a `Point` funkciókat a motorháztető alatt lévő `Polygon` funkciókra konvertálja, és a térképen a sokszög kihúzási rétegének használatával jeleníthető meg, ahogy az alábbi mintakód is mutatja.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="A drone-légtér sokszöge" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Az <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>drone légtérben</a> .
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
