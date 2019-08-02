---
title: HTML-jelölő hozzáadása a Azure Mapshoz | Microsoft Docs
description: HTML-jelölő hozzáadása a JavaScript-térképhez
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 65777a87cd042a4d8b3b14255dbf99241cd42e18
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638746"
---
# <a name="add-html-markers-to-the-map"></a>HTML-jelölők hozzáadása a térképhez

Ez a cikk bemutatja, hogyan adhat hozzá egy egyéni HTML-fájlt, például egy képfájlt a térképhez HTML-jelölőként.

> [!NOTE]
> A HTML-jelölők nem kapcsolódnak az adatforrásokhoz. Ehelyett az elhelyezési adatok közvetlenül a jelölőhöz kerülnek, a jelölő pedig a Maps `markers` tulajdonsághoz kerül, amely egy [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> A Azure Maps webes vezérlő legtöbb rétegével ellentétben, amely WebGL használ a rendereléshez, a HTML-jelölők hagyományos DOM-elemeket használnak a rendereléshez. Így a további HTML-jelölők hozzáadott egy oldalt, annál több DOM-elem van. Néhány száz HTML-jelölő hozzáadását követően a teljesítmény csökkenhet. Nagyobb adatkészletek esetében érdemes lehet az adatait csoportosítani vagy szimbólumot vagy buborék réteget használni.

## <a name="add-an-html-marker"></a>HTML-jelölő hozzáadása

A HtmlMarker osztály alapértelmezett stílusú. Testreszabhatja a jelölőt a jelölő színének és szövegének megadásával. A HtmlMarker osztály alapértelmezett stílusa egy SVG-sablon, amely színes és szöveges helyőrzővel rendelkezik. A HtmlMarker beállításokban adja meg a szín és a szöveg tulajdonságokat a gyors testreszabáshoz. 

<br/>

<iframe height='500' scrolling='no' title='HTML-jelölő hozzáadása térképhez' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat egy <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>HTML-jelölő hozzáadásával</a> Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódban a kód első blokkja egy Térkép objektumot hoz létre. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkja egy [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) helyez el a térképhez a [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) osztály [markerek](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) tulajdonságával. A rendszer hozzáadja a HtmlMarker a térképhez az [esemény-figyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) függvényben annak érdekében, hogy a Térkép teljes terhelése után megjelenjen.

## <a name="create-svg-templated-html-marker"></a>SVG-sablon alapján létrehozott HTML-jelölő létrehozása

A HTML `htmlContent` -jelölő alapértelmezett értéke egy SVG-sablon, amelyben a `{color}` mappák `{text}` és a benne található. Létrehozhat egyéni SVG-karakterláncokat, és felveheti ugyanezeket a helyőrzőket az SVG `color` -be, így a jelölő és `text` a beállítások is módosíthatják ezeket a helyőrzőket az SVG-ben.

<br/>

<iframe height='500' scrolling='no' title='HTML-jelölő egyéni SVG-sablonnal' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>HTML-jelölőjét egyéni SVG</a> -<a href='https://codepen.io/azuremaps'>@azuremaps</a>sablonnal Azure Maps () használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-css-styled-html-marker"></a>CSS stílusú HTML-jelölő hozzáadása

A HTML-jelölők egyik előnye, hogy a CSS használatával számos nagyszerű testreszabási lehetőség érhető el. Ebben a példában a HtmlMarker tartalma HTML és CSS, amely egy animált PIN-kódot hoz létre, amely a helyére és a impulzusokra esik.

<br/>

<iframe height='500' scrolling='no' title='HTML-adatforrás' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML</a> -adatforrást Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Mozgatható HTML-jelölők

Ez a minta bemutatja, hogyan lehet egy HTML-jelölőt áthúzni. HTML `dragstart` -jelölők `drag`támogatása és `dragend` eseményei.

<br/>

<iframe height='500' scrolling='no' title='Draggable HTML Marker' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollra <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>húzható HTML</a> -jelölőt Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Egér-események hozzáadása HTML-jelölőhöz

Ezek a minták azt mutatják be, hogyan adhat hozzá egeret, és áthúzhatja az eseményeket egy HTML-jelölőbe.

<br/>

<iframe height='500' scrolling='no' title='Egér-események hozzáadása HTML-jelölőhöz' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat az <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>egérmutató hozzáadásával a HTML-jelölőket</a> a következő helyen: Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

A térképekhez hozzáadandó további példákat a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Szimbólum réteg hozzáadása](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Buborék réteg hozzáadása](./map-add-bubble-layer.md)