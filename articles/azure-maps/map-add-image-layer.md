---
title: Egy rendszerkép réteg hozzáadása az Azure Maps |} A Microsoft Docs
description: A Javascript-leképezés egy kép réteg hozzáadása
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5396fefca3a60dea7a503f8b4e84cc575753ea30
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229115"
---
# <a name="add-an-image-layer-to-a-map"></a>A térkép kép réteg hozzáadása

Ez a cikk bemutatja, hogyan lehet keverni a koordináták a térképen rögzített számú kép. Nincsenek számos forgatókönyv, amelyben rajzolhat, amelyek a képet a térképen történik. Íme néhány példa a lemezképek térképeken; gyakran átfedésben típusú

* Rögzített drónok képeket.
* Épület alaprajzokat.
* Előzmények vagy más specializált térképi képeit.
* Tervezetek feladat helyek.
* Időjárási a mérlegeli képek.

> [!TIP]
> Egy [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) gyors van egy egyszerű módja annak átfedi a képet a térképen. Azonban ha a kép túl nagy, a böngészőben is kihívást jelent a betölti azt. Ebben az esetben érdemes lehet ossza a rendszerképet csempék és betöltenie őket a térképen, mint egy [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

## <a name="add-an-image-layer"></a>Rendszerképréteg hozzáadása

Ez a példa bemutatja, hogyan átfedő képe egy [1922 zártuk New Jersey leképezésből](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) a térképen.

<br/>

<iframe height='500' scrolling='no' title='Az egyszerű réteg' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/eQodRo/'>az egyszerű réteg</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, az egy [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) jön létre egy URL-címet egy kép és a következő formátumban négy sarkánál koordináták átadásával `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

## <a name="import-a-kml-ground-overlay"></a>Egy KML földön területre importálása

Ez a példa bemutatja, hogyan átfedi egy kép rétegként KML földön területre információkat a térképen. Keleti régiójában, és a Nyugat-India koordináták és a egy óramutató járásával ellentétes irányban forgatását, ahol kiindulási lemezképeként réteg azt várja a kép minden egyes sarok koordináták KML földön átfedések Észak, déli régiója, biztosítanak. Ebben a példában a KML földön területre van, a Chartres cathedral és forráskóddal a [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

<br/>

<iframe height='500' scrolling='no' title='Kép rétegként KML földön területre' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML szabad területre kép rétegként</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód használ a static `getCoordinatesFromEdges` funkcióját a [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) osztály alapján számítja ki a négy biztos lehet benne, északi a lemezképet, India, kelet Nyugat-India és váltogatásának adatait a KML szabad területre.


## <a name="customize-an-image-layer"></a>Egy rendszerkép réteg testreszabása

A kép réteg stílusának számos lehetőség van. Itt egy olyan eszköz, próbálja ki őket.

<br/>

<iframe height='700' scrolling='no' title='Kép réteg beállításai' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>rendszerkép-réteg lehetőségek</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Tekintse meg a további Kódminták a maps hozzá az alábbi cikkeket:

> [!div class="nextstepaction"]
> [Egy mozaikréteg hozzáadása](./map-add-tile-layer.md)