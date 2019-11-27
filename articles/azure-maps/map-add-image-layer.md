---
title: Rendszerkép-réteg hozzáadása a Azure Mapshoz | Microsoft Docs
description: Rendszerkép réteg hozzáadása a Azure Maps web SDK-hoz.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: fadaaf7c64b11a6d6d94c68234f8288d1b3f8d07
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480496"
---
# <a name="add-an-image-layer-to-a-map"></a>Képréteg hozzáadása térképhez

Ebből a cikkből megtudhatja, hogyan helyezhet át egy képet a Térkép rögzített koordinátáiba. Számos olyan forgatókönyv létezik, amelyben a képek átfedésben vannak a térképen. Íme néhány példa arra, hogy milyen típusú képeket gyakran helyeznek el a térképeken;

* A herékből rögzített rendszerképek.
* Alaprajzok kialakítása.
* Korábbi vagy egyéb speciális térképi képek.
* A feladatok helyeinek tervrajzai.
* Időjárási radar-rendszerképek.

> [!TIP]
> A [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) egy egyszerű módja a képek térképre való átfedésének. Ha azonban a rendszerkép nagy méretű, a böngésző megküzdheti a betöltését. Ebben az esetben érdemes a képet csempévé feltörni, és a térképre betölteni a [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

A rendszerkép réteg a következő képformátumokat támogatja:

- JPEG
- PNG
- BMP
- GIF (nincs animáció)

## <a name="add-an-image-layer"></a>Rendszerképréteg hozzáadása

A következő kódrészletben egy, a térképen a [1922-es, Newark New Jersey-i Térkép](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) képe látható. Egy [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) úgy jön létre, hogy átadja a képhez tartozó URL-címet, és a négy sarkot a `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`formátumban adja meg.

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

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Egyszerű képréteg' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/eQodRo/'>egyszerű Képrétegét</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-ground-overlay"></a>KML-fedési átfedés importálása

Ez a minta bemutatja, hogyan fedi le a KML-alapú átfedési adatokat képrétegként a térképen. A KML-alapú átfedések Észak-, Dél-, Kelet-és Nyugat-koordinátákat biztosítanak, és a megegyező irányú rotációs, míg a képréteg a rendszerkép minden sarkánál koordinátákat vár. A példában szereplő KML-borító a Chartres-székesegyházból származik, és a [wikimediaből](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)származik.

A következő kód a [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) osztály statikus `getCoordinatesFromEdges` funkciójával számítja ki a rendszerkép négy sarkát az Észak-, Dél-, Kelet-, nyugati és rotációs adatokból a KML-terület átfedése alapján.

<br/>

<iframe height='500' scrolling='no' title='KML-terep átfedése képrétegként' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti a KML-alapú <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>helyszíni átfedést képrétegként</a> .
</iframe>

## <a name="customize-an-image-layer"></a>Képréteg testreszabása

A képréteg számos stílust tartalmaz. Itt látható egy eszköz, amellyel kipróbálhatja őket.

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