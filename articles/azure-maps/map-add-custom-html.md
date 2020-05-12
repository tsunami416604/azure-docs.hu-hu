---
title: HTML-jelölő hozzáadása a térképhez | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá HTML-jelölőket térképekhez a Microsoft Azure Maps web SDK használatával.
author: Philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 30d9cf9848a8d99505bf9f9c97bef5aaa31065ee
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119282"
---
# <a name="add-html-markers-to-the-map"></a>HTML-jelölők hozzáadása a térképhez

Ez a cikk bemutatja, hogyan adhat hozzá egy egyéni HTML-fájlt, például egy képfájlt a térképhez HTML-jelölőként.

> [!NOTE]
> A HTML-jelölők nem kapcsolódnak az adatforrásokhoz. Ehelyett az elhelyezési adatok közvetlenül a jelölőhöz kerülnek, a jelölő pedig a Maps `markers` tulajdonsághoz kerül, amely egy [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> A Azure Maps webes vezérlő legtöbb rétegével ellentétben, amely WebGL használ a rendereléshez, a HTML-jelölők hagyományos DOM-elemeket használnak a rendereléshez. Így az oldalhoz hozzáadott HTML-jelölők, annál több DOM-elem van. Néhány száz HTML-jelölő hozzáadását követően a teljesítmény csökkenhet. Nagyobb adatkészletek esetében érdemes lehet az adatait csoportosítani vagy szimbólumot vagy buborék réteget használni.

## <a name="add-an-html-marker"></a>HTML-jelölő hozzáadása

A [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) osztály alapértelmezett stílusú. Testreszabhatja a jelölőt a jelölő színének és szövegének megadásával. A HTML-jelölő osztály alapértelmezett stílusa egy SVG-sablon, amely tartalmaz egy `{color}` és egy `{text}` helyőrzőt. A gyors testreszabáshoz adja meg a szín és a szöveg tulajdonságokat a HTML-jelölő beállításai között. 

A következő kód egy HTML-jelölőt hoz létre, és a Color tulajdonságot "DodgerBlue" értékre állítja, a Text tulajdonság pedig "10". Az előugró ablak a jelölőhöz van csatolva, és az `click` esemény a felugró ablak láthatóságának váltására szolgál.

```javascript
//Create an HTML marker and add it to the map.
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

<iframe height='500' scrolling='no' title='HTML-jelölő hozzáadása térképhez' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat egy <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>HTML-jelölő hozzáadásával</a> Azure Maps () használatával a <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-svg-templated-html-marker"></a>SVG-sablon alapján létrehozott HTML-jelölő létrehozása

A `htmlContent` HTML-jelölő alapértelmezett értéke egy SVG-sablon, amelyben a mappák `{color}` és `{text}` a benne található. Létrehozhat egyéni SVG-karakterláncokat, és felveheti ugyanezeket a helyőrzőket az SVG-be, így a `color` jelölő és a `text` beállítások is módosíthatják ezeket a helyőrzőket az SVG-ben.

<br/>

<iframe height='500' scrolling='no' title='HTML-jelölő egyéni SVG-sablonnal' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>HTML-jelölőjét egyéni SVG-sablonnal</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> A Azure Maps web SDK számos SVG-képsablont biztosít, amelyek HTML-jelölők használatával használhatók. További információ: a [képsablonokkal](how-to-use-image-templates-web-sdk.md) kapcsolatos dokumentum használata.

## <a name="add-a-css-styled-html-marker"></a>CSS stílusú HTML-jelölő hozzáadása

A HTML-jelölők egyik előnye, hogy a CSS használatával számos nagyszerű testreszabási lehetőség érhető el. Ebben a példában a HtmlMarker tartalma HTML és CSS, amely egy animált PIN-kódot hoz létre, amely a helyére és a impulzusokra esik.

<br/>

<iframe height='500' scrolling='no' title='HTML-adatforrás' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML-Adatforrást</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Mozgatható HTML-jelölők

Ez a minta bemutatja, hogyan lehet egy HTML-jelölőt áthúzni. A HTML-jelölők támogatása `drag` , `dragstart` és `dragend` események.

<br/>

<iframe height='500' scrolling='no' title='Mozgatható HTML-jelölő' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollra <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>HÚZHATÓ HTML-jelölőt</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Egér-események hozzáadása HTML-jelölőhöz

Ezek a minták azt mutatják be, hogyan adhat hozzá egeret, és áthúzhatja az eseményeket egy HTML-jelölőbe.

<br/>

<iframe height='500' scrolling='no' title='Egér-események hozzáadása HTML-jelölőhöz' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat az <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>egérmutató hozzáadásával a HTML-jelölőket</a> a következő helyen: Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) a <a href='https://codepen.io'>CodePen</a>.
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
> [Rendszerképsablonok használata](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](./map-add-bubble-layer.md)
