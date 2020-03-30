---
title: HTML-jelölő hozzáadása a leképezéshez | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá HTML-jelölőt egy térképhez a Microsoft Azure Maps Web SDK használatával.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 2700d42c25d58911fb275ad9ce6c5610cd22624d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536768"
---
# <a name="add-html-markers-to-the-map"></a>HTML-jelölők hozzáadása a térképhez

Ebből a cikkből megtudhatja, hogy miként vehet fel egyéni HTML-kódot, például egy képfájlt a térképre HTML-jelölőként.

> [!NOTE]
> A HTML-jelölők nem kapcsolódnak adatforrásokhoz. Ehelyett pozíció információkat ad nak közvetlenül a marker `markers` és a marker egészül ki a térképek tulajdonság, amely egy [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> Az Azure Maps webvezérlő legtöbb rétegével ellentétben, amelyek a WebGL-et használják a rendereléshez, a HTML-jelölők hagyományos DOM-elemeket használnak a rendereléshez. Mint ilyen, a több HTML markerek hozzá egy oldalt, annál TÖBB DOM elemek vannak. Néhány száz HTML-jelölő hozzáadása után a teljesítmény romolhat. Nagyobb adatkészletek esetén fontolja meg az adatok fürtözését, vagy egy szimbólum- vagy buborékréteg használatát.

## <a name="add-an-html-marker"></a>HTML-jelölő hozzáadása

A [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) osztály nak van egy alapértelmezett stílusa. A jelölő testreszabásához adja meg a jelölő szín- és szövegbeállításait. A HTML-jelölőosztály alapértelmezett stílusa egy SVG-sablon, amelynek helyőrzője `{color}` és `{text}` helye. A gyors testreszabásérdekében adja meg a HTML-jelölő beállításainak szín- és szövegtulajdonságait. 

A következő kód létrehoz egy HTML-jelölőt, és a színtulajdonságot "DodgerBlue"-ra, a szöveges tulajdonságot pedig "10"-re állítja. A jelölőhöz előugró `click` ablak van csatolva, és az esemény az előugró ablak láthatóságának váltására szolgál.

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

Az alábbiakban a fenti funkciók teljes futókód-mintája látható.

<br/>

<iframe height='500' scrolling='no' title='HTML-jelölő hozzáadása térképhez' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a Toll hozzáadása HTML marker egy<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>térképet</a> az Azure Maps ( ) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-svg-templated-html-marker"></a>SVG-sablonos HTML-jelölő létrehozása

A `htmlContent` Html-jelölő alapértelmezett beállítása és a `{color}` helymappák `{text}` alapértelmezett változata. Létrehozhat egyéni SVG-karakterláncokat, és hozzáadhatja ezeket a helyőrzőket az SVG-hez úgy, hogy a jelölő beállításai és `color` `text` beállításai frissítsék ezeket a helyőrzőket az SVG-ben.

<br/>

<iframe height='500' scrolling='no' title='HTML-jelölő egyéni SVG-sablonnal' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>HTML markeregyéni SVG sablon</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Az Azure Maps webes SDK számos SVG-lemezképsablont biztosít, amelyek HTML-jelölőkkel használhatók. További információt a [Képsablonok használata](how-to-use-image-templates-web-sdk.md) című dokumentumban talál.

## <a name="add-a-css-styled-html-marker"></a>CSS-stílusú HTML-jelölő hozzáadása

Az egyik előnye a HTML markerek, hogy sok nagy testreszabások, hogy lehet elérni a CSS. Ebben a példában a HtmlMarker tartalma HTML-ből és CSS-ből áll, amelyek animált tűt hoznak létre, amely a helyére esik és impulzusokat.

<br/>

<iframe height='500' scrolling='no' title='HTML-adatforrás' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen HTML<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>DataSource</a> by Azure Maps ( ) című témakört a <a href='https://codepen.io'>CodePen portálon.</a>
</iframe>

## <a name="draggable-html-markers"></a>Húzható HTML-jelölők

Ez a minta bemutatja, hogyan lehet a HTML-jelölők mozgatható. A HTML-jelölők támogatják `drag`a , `dragstart`és `dragend` az eseményeket.

<br/>

<iframe height='500' scrolling='no' title='Húzható HTML-jelölő' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a Pen <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>Draggable HTML</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>Marker by Azure Maps ( ) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Egéresemények hozzáadása HTML-jelölőkhöz

Ezek a minták azt mutatják be, hogyan adhat hozzá egeret, és hogyan húzhat eseményeket egy HTML-jelölőhöz.

<br/>

<iframe height='500' scrolling='no' title='Egéresemények hozzáadása HTML-jelölőkhöz' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg az <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>Egéresemények hozzáadása HTML-jelölőkhöz az</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tollas hozzáadását a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

A térképekhez hozzáadni rakandó további kódpéldákat az alábbi cikkekben talál:

> [!div class="nextstepaction"]
> [Rendszerképsablonok használata](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](./map-add-bubble-layer.md)
