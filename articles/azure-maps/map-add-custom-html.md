---
title: Az Azure Maps ad hozzá egy HTML-jelölő |} A Microsoft Docs
description: A Javascript-leképezés egy HTML-jelölő hozzáadása
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 1c812a77429e13ea39b2f4946043c13e10aaf097
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993742"
---
# <a name="add-html-markers-to-the-map"></a>A térkép HTML jelölők hozzáadása

Ez a cikk bemutatja, hogyan például egy képfájlt egy egyéni HTML hozzáadása egy HTML-jelölő, a térképen.

> [!NOTE]
> HTML jelölők nem csatlakozhat adatforrásokhoz. Ehelyett pozíció adatokat közvetlenül a jelölő kerül, és a jelölő bekerül a maps `markers` tulajdonság, amely egy [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> Ellentétben a legtöbb rétegeinek az Azure Maps webes vezérlő vagy a Web GL használó renderelést HTML jelölők használata a hagyományos DOM-elemek Renderelés. Mint ilyen a további HTML jelölők hozzáadva egy oldal, a további DOM-elemek vannak. Néhány száz HTML jelölők hozzáadása után a teljesítmény csökken. Nagyobb adatkészletekhez fontolja meg az adatok fürtszolgáltatás, vagy egy szimbólum vagy egy körre réteg használatával.

## <a name="add-an-html-marker"></a>Egy HTML-mutató hozzáadása

A HtmlMarker osztálya rendelkezik egy alapértelmezett stílusát. A jelölő a és a szöveg színe beállításainak testre szabhatja a jelölő. Az alapértelmezett stílusát HtmlMarker osztály egy SVG-sablont, amely rendelkezik és a szöveg színe helyőrző. A gyors testreszabást HtmlMarker beállításait és a szöveg színe tulajdonságainak beállítása. 

<br/>

<iframe height='500' scrolling='no' title='Adjon hozzá egy HTML-jelölő térképre' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>adjon hozzá egy HTML-jelölő térképre</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot hozzáad egy [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) a térkép történő a [jelölők](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) tulajdonságát a [térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) osztály. A térkép belül a HtmlMarker bekerül a [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) győződjön meg arról, hogy teljes mértékben betölti a térkép után megjelenik a függvény.

## <a name="create-svg-templated-html-marker"></a>SVG-sablonalapú HTML jelölő létrehozása

Az alapértelmezett `htmlContent` egy HTML-jelölő egy helyen mappák SVG-sablonnal `{color}` és `{text}` benne. Létrehozhat egyéni SVG-karakterláncok és az SVG adja hozzá ezeket a helyőrzőket úgy, hogy a beállítás a `color` és `text` a jelölő beállítások frissítése az SVG a helyőrzőket.

<br/>

<iframe height='500' scrolling='no' title='HTML jelölő egyéni SVG-sablonnal' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>HTML jelölő egyéni SVG-sablonnal</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-css-styled-html-marker"></a>Egy CSS HTML jelölő stílusának hozzáadása

HTML jelölők előnyeit egyik célja, hogy nincsenek-e számos nagyszerű testreszabásokat CSS használatával érhető el. Ebben a példában a HtmlMarker tartalmának áll HTML és CSS által létrehozott animált PIN-kód, amely a hely és néhányszor csökken.

<br/>

<iframe height='500' scrolling='no' title='HTML-adatforrás' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Húzható HTML jelölők

Ez a példa bemutatja, hogyan javíthatja egy HTML-jelölő húzható. Támogatja a HTML-jelölők `drag`, `dragstart` és `dragend` eseményeket.

<br/>

<iframe height='500' scrolling='no' title='Draggable HTML Marker' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>húzható HTML jelölő</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>HTML jelölők egér események felvétele

Ezek a minták ismertetik, hogyan adhat hozzá az egér, és húzza az eseményeket egy HTML-jelölő.

<br/>

<iframe height='500' scrolling='no' title='Egér HTML jelölők való felvétele' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>egér HTML jelölők való felvétele</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

A maps hozzá további kódot példák a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Egy szimbólum réteg hozzáadása](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Egy buborék réteg hozzáadása](./map-add-bubble-layer.md)