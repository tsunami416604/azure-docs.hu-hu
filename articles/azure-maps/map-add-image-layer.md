---
title: Rendszerkép-réteg hozzáadása a Azure Mapshoz | Microsoft Docs
description: Képréteg hozzáadása a JavaScript-térképhez
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7ea0f37e307196af4b27fd3f8fb1aa0d42443dfa
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638735"
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

## <a name="add-an-image-layer"></a>Rendszerképréteg hozzáadása

Ez a minta azt mutatja be, hogyan lehet átfedő képet létrehozni [Newark New Jersey-ról](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) a térképen a 1922-es térképről.

<br/>

<iframe height='500' scrolling='no' title='Egyszerű képréteg' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/eQodRo/'>egyszerű</a> képrétegét<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódban a kód első blokkja egy Térkép objektumot hoz létre. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkjában létrejön egy [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) , amely egy URL-címet továbbít egy képhez, és koordinálja a négy sarkot a formátumban `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

## <a name="import-a-kml-ground-overlay"></a>KML-fedési átfedés importálása

Ez a minta bemutatja, hogyan fedi le a KML-alapú átfedési adatokat képrétegként a térképen. A KML-alapú átfedések Észak-, Dél-, Kelet-és Nyugat-koordinátákat biztosítanak, és az óramutató járásával megegyező irányú rotációs, ahol a képréteg a rendszerkép minden sarkánál koordinátákat vár. A példában szereplő KML-borító a Chartres-székesegyházból származik, és a [wikimediaből](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)származik.

<br/>

<iframe height='500' scrolling='no' title='KML-terep átfedése képrétegként' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Az <a href='https://codepen.io'>CodePen</a>- <a href='https://codepen.io/azuremaps/pen/EOJgpj/'></a> on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) képrétegként tekintse meg a KML-terepen látható tollat.
</iframe>

A fenti kód a [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) osztály `getCoordinatesFromEdges` statikus funkciójával kiszámítja a rendszerkép négy sarkát az Észak-, Dél-, Kelet-, nyugati és rotációs adatokból a KML-terület átfedése alapján.


## <a name="customize-an-image-layer"></a>Képréteg testreszabása

A képréteg számos stílust tartalmaz. Itt látható egy eszköz, amellyel kipróbálhatja őket.

<br/>

<iframe height='700' scrolling='no' title='Képréteg beállításai' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg <a href='https://codepen.io/azuremaps/pen/RqOGzx/'></a> a toll képrétegének beállításait<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Csempe rétegének hozzáadása](./map-add-tile-layer.md)