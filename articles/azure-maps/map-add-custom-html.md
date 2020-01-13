---
title: HTML-jelölő hozzáadása a térképhez | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá HTML-jelölőket térképekhez a Microsoft Azure Maps web SDK használatával.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5a15b3b9e51772d8c7e5312968fecb180069e2a7
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911249"
---
# <a name="add-html-markers-to-the-map"></a>HTML-jelölők hozzáadása a térképhez

Ez a cikk bemutatja, hogyan adhat hozzá egy egyéni HTML-fájlt, például egy képfájlt a térképhez HTML-jelölőként.

> [!NOTE]
> A HTML-jelölők nem kapcsolódnak az adatforrásokhoz. Ehelyett az elhelyezési adatok közvetlenül a jelölőhöz kerülnek, a jelölő pedig a Maps `markers` tulajdonsághoz kerül, amely egy [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> A Azure Maps webes vezérlő legtöbb rétegével ellentétben, amely WebGL használ a rendereléshez, a HTML-jelölők hagyományos DOM-elemeket használnak a rendereléshez. Így a további HTML-jelölők hozzáadott egy oldalt, annál több DOM-elem van. Néhány száz HTML-jelölő hozzáadását követően a teljesítmény csökkenhet. Nagyobb adatkészletek esetében érdemes lehet az adatait csoportosítani vagy szimbólumot vagy buborék réteget használni.

## <a name="add-an-html-marker"></a>HTML-jelölő hozzáadása

A [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) osztály alapértelmezett stílusú. Testreszabhatja a jelölőt a jelölő színének és szövegének megadásával. A HTML-jelölő osztály alapértelmezett stílusa egy SVG-sablon, amely `{color}` és `{text}` helyőrzővel rendelkezik. A gyors testreszabáshoz adja meg a szín és a szöveg tulajdonságokat a HTML-jelölő beállításai között. 

A következő kód egy HTML-jelölőt hoz létre, és a Color tulajdonságot "DodgerBlue" értékre állítja, a Text tulajdonság pedig "10". Az előugró ablak csatolva van a jelölőhöz, és `click` eseményt az előugró ablak láthatóságának váltására használjuk.

```javascript
//Create a HTML marker and add it to the map.
var marker = new atlas.HtmlMarker({
    color: 'DodgerBlue',
    text: '10',
    position: [0, 0],
    popup: new atlas.Popup({
        content: '<div style="padding:10px">Hello World</div>',
        pixelOffset: [0, -30]
    })
});

map.markers.add(marker);

//Add a click event to toggle the popup.
map.events.add('click',marker, () => {
    marker.togglePopup();
});
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='HTML-jelölő hozzáadása térképhez' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>egy HTML-jelölő hozzáadásával</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-svg-templated-html-marker"></a>SVG-sablon alapján létrehozott HTML-jelölő létrehozása

A HTML-jelölő alapértelmezett `htmlContent` egy SVG-sablon, amelyben a mappák `{color}` és `{text}`. Létrehozhat egyéni SVG-karakterláncokat, és felveheti ugyanezeket a helyőrzőket az SVG-be, hogy a jelölő `color` és `text` beállításait az SVG-ben frissítse ezeket a helyőrzőket.

<br/>

<iframe height='500' scrolling='no' title='HTML-jelölő egyéni SVG-sablonnal' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>HTML-jelölőjét egyéni SVG-sablonnal</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> A Azure Maps web SDK számos SVG-képsablont biztosít, amelyek HTML-jelölők használatával használhatók. További információ: a [képsablonokkal](how-to-use-image-templates-web-sdk.md) kapcsolatos dokumentum használata.

## <a name="add-a-css-styled-html-marker"></a>CSS stílusú HTML-jelölő hozzáadása

A HTML-jelölők egyik előnye, hogy a CSS használatával számos nagyszerű testreszabási lehetőség érhető el. Ebben a példában a HtmlMarker tartalma HTML és CSS, amely egy animált PIN-kódot hoz létre, amely a helyére és a impulzusokra esik.

<br/>

<iframe height='500' scrolling='no' title='HTML-adatforrás' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML-adatforrást</a> a <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>).
</iframe>

## <a name="draggable-html-markers"></a>Mozgatható HTML-jelölők

Ez a minta bemutatja, hogyan lehet egy HTML-jelölőt áthúzni. A HTML-jelölők támogatják `drag`, `dragstart`és `dragend` eseményeket.

<br/>

<iframe height='500' scrolling='no' title='Mozgatható HTML-jelölő' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollra <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>HÚZHATÓ HTML-jelölőt</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Egér-események hozzáadása HTML-jelölőhöz

Ezek a minták azt mutatják be, hogyan adhat hozzá egeret, és áthúzhatja az eseményeket egy HTML-jelölőbe.

<br/>

<iframe height='500' scrolling='no' title='Egér-események hozzáadása HTML-jelölőhöz' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat az <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>egérmutató hozzáadásával a HTML-jelölőket</a> a <a href='https://codepen.io'>CodePen</a>Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>).
</iframe>

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

A térképekhez hozzáadandó további példákat a következő cikkekben talál:

> [!div class="nextstepaction"]
> [A képsablonok használata](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Szimbólum réteg hozzáadása](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Buborék réteg hozzáadása](./map-add-bubble-layer.md)