---
title: Képréteg hozzáadása térképhez | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan fedi le a képeket a térképeken a Microsoft Azure Maps web SDK használatával.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: d2260e5c9e4125be1c4b0fa5e9d7c39a8e03d43f
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911199"
---
# <a name="add-an-image-layer-to-a-map"></a>Képréteg hozzáadása térképhez

Ebből a cikkből megtudhatja, hogyan helyezhet át egy képet egy rögzített koordináta-készletbe a térképen. Íme néhány példa arra, hogy milyen típusú képeket gyakran helyez el a térképeken:

* A herékből rögzített képek
* Alaprajzok építése
* Korábbi vagy egyéb speciális térképi képek
* A feladatok webhelyeinek tervrajzai
* Időjárási radar-rendszerképek

> [!TIP]
> Az [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) egyszerű módot biztosít a képek térképre való átfedésére. Vegye figyelembe, hogy a böngészők nehezen tudnak betölteni egy nagyméretű képet. Ebben az esetben érdemes a képet csempére feltörni, és [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)betölteni őket a térképbe.

A rendszerkép réteg a következő képformátumokat támogatja:

- JPEG
- PNG
- BMP
- GIF (nincs animáció)

## <a name="add-an-image-layer"></a>Rendszerképréteg hozzáadása

A következő kód egy, a térképen található [Newark, New Jersey, 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) -as Térkép képét fedi le. Egy [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) úgy jön létre, hogy átadja egy rendszerkép URL-címét, és a `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`formátumban a négy sarok koordinátáit adja meg.

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

Itt látható az előző kód teljes futtatási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Egyszerű képréteg' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/eQodRo/'>egyszerű Képrétegét</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-ground-overlay"></a>KML-fedési átfedés importálása

Ez a következő minta bemutatja, hogyan fedi le a KML-alapú átfedési adatokat képrétegként a térképen. A KML-alapú átfedések Észak-, Dél-, Kelet-és Nyugat-koordinátákat biztosítanak, és a megegyező irányú rotációs, míg a képréteg a rendszerkép minden sarkánál koordinátákat vár. A példában szereplő KML-borító a Chartres-székesegyházból származik, és a [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)-ből származik.

A következő kód a [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) osztály statikus `getCoordinatesFromEdges` függvényét használja. Kiszámítja a rendszerkép négy sarkát az Észak-, Dél-, Kelet-és Nyugat-és rotációs adatokból a KML-terület átfedése alapján.

<br/>

<iframe height='500' scrolling='no' title='KML-terep átfedése képrétegként' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti a KML-alapú <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>helyszíni átfedést képrétegként</a> .
</iframe>

## <a name="customize-an-image-layer"></a>Képréteg testreszabása

A képréteg számos stílust tartalmaz. Az alábbi eszköz segítségével kipróbálhatja őket.

<br/>

<iframe height='700' scrolling='no' title='Képréteg beállításai' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a toll <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Képrétegének beállításait</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Csempe rétegének hozzáadása](./map-add-tile-layer.md)