---
title: Képréteg hozzáadása térképhez | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan fedheti le a képet a térképen a Microsoft Azure Maps Web SDK használatával.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 69bf41f9d88081b9a416b9bee91e8650a84f12c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209715"
---
# <a name="add-an-image-layer-to-a-map"></a>Képréteg hozzáadása térképhez

Ez a cikk bemutatja, hogyan fedheti át a képet egy rögzített koordinátakészletre. Íme néhány példa a különböző képtípusokra, amelyek a térképeken eltitkezhetők:

* Drónokról készített képek
* Építési alaprajzok
* Történelmi vagy más speciális térképképek
* Munkaterületek tervrajzai
* Időjárás radar képek

> [!TIP]
> Az [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) segítségével egyszerűen átfedhet egy képet a térképen. Ne feledje, hogy a böngészőknek nehézséget okoz egy nagy kép betöltése. Ebben az esetben fontolja meg a kép mozaikokra bontását, és betölti őket a térképre [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)néven.

A képréteg a következő képformátumokat támogatja:

- Jpeg
- PNG
- BMP
- GIF (animációk nélkül)

## <a name="add-an-image-layer"></a>Rendszerképréteg hozzáadása

A következő kód egy [1922-es newarki térkép](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) képét fedi le a térképen. Az [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) úgy jön létre, hogy url-t ad át `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`egy képnek, és koordinátáit a formátumban lévő négy sarokhoz.

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

Itt van a teljes futó kód minta az előző kód.

<br/>

<iframe height='500' scrolling='no' title='Egyszerű képréteg' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen Simple<a href='https://codepen.io/azuremaps'>@azuremaps</a>Image <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Layer</a> by Azure Maps () című témakört a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>KML-fájl importálása földalárendelt fedvényként

Ez a minta bemutatja, hogyan adhat hozzá KML földalapú átfedési információkat képrétegként a térképen. A KML földátfedések biztosítják az északi, déli, keleti és nyugati koordinátákat, valamint az óramutató járásával ellentétes irányú elforgatást. A képréteg azonban koordinátákat vár a kép minden sarkához. A KML földoverlay ebben a mintában a Chartres katedrális, és ez származik [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

A kód az `getCoordinatesFromEdges` [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) osztály statikus függvényét használja. Kiszámítja a kép négy sarkát a KML földátfedés észak, déli, keleti, nyugati és forgási információi alapján.

<br/>

<iframe height='500' scrolling='no' title='KML földelt átfedés képrétegként' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML ground overlay-t képrétegként</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) segítségével a <a href='https://codepen.io'>CodePen-en.</a>
</iframe>

## <a name="customize-an-image-layer"></a>Képréteg testreszabása

A képréteg számos formázási lehetőséggel rendelkezik. Itt egy eszköz, hogy próbálja ki őket.

<br/>

<iframe height='700' scrolling='no' title='Képréteg beállításai' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Tollkép réteg beállításait</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) segítségével a <a href='https://codepen.io'>CodePen-en.</a>
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [ImageLayer (Képréteg)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

További kódmintákat a térképekhez további kódmintákért tekintse meg:

> [!div class="nextstepaction"]
> [Mozaikréteg hozzáadása](./map-add-tile-layer.md)