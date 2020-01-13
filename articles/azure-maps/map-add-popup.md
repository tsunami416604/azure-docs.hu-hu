---
title: Felugró ablak hozzáadása egy ponthoz a térképen | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá egy előugró pontot egy ponthoz a Microsoft Azure Maps web SDK használatával.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 79bafb331cb7ad38ea7cad9e510b22886b647764
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911154"
---
# <a name="add-a-popup-to-the-map"></a>Felugró ablak hozzáadása a térképhez

Ez a cikk bemutatja, hogyan adhat hozzá egy előugró pontot egy térképen.

## <a name="understand-the-code"></a>A kód értelmezése

A következő kód egy pont funkciót helyez el, amely `name` és `description` tulajdonságokkal rendelkezik a térképhez egy szimbólum réteg használatával. Az [előugró osztály](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) egy példánya jön létre, de nem jelenik meg. Az egérmutató a szimbólum rétegbe kerül, és az előugró ablak megnyitására és bezárására szolgál, amikor az egérmutató a szimbólum jelölője fölé vagy lefelé mutat. Ha a jelölő szimbólum látható, akkor a rendszer az előugró ablak `position` tulajdonságát a jelölő pozíciójának megfelelően frissíti, és a `content` lehetőség frissül, és egy olyan HTML-kódot tartalmaz, amely betakarja a pont szolgáltatás `name` és `description` tulajdonságait. Ekkor megjelenik az előugró ablak a térképen a `open` függvény használatával.

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Felugró ablak hozzáadása a Azure Maps használatával' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io'>CodePen</a>Azure Maps Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>használatával</a> .
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Felugró ablak újrahasználata több ponttal

Ha nagy számú ponttal rendelkezik, és egyszerre csak egy előugró ablak jelenik meg, a legjobb módszer egy előugró ablak létrehozása és újbóli használata, mint az egyes pontokhoz tartozó előugró ablakok létrehozása. A felugró ablak újrafelhasználásával az alkalmazás által létrehozott DOM-elemek száma jelentősen csökkent, ami jobb teljesítményt nyújt. Az alábbi minta 3 pontos funkciókat hoz létre. Ha bármelyikre kattint, megjelenik egy előugró ablak, amely az adott pont funkciójának tartalmát jeleníti meg.

<br/>

<iframe height='500' scrolling='no' title='Felugró ablak újrahasználata több PIN-kód használatával' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti a tollat a <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>felugró ablak újrafelhasználásával több PIN</a> -kódra.
</iframe>

## <a name="customizing-a-popup"></a>Felugró ablak testreszabása

Alapértelmezés szerint az előugró ablak fehér háttérrel, egy mutató nyilat tartalmaz az alján, valamint egy Bezárás gombot a jobb felső sarokban. Az alábbi minta a háttérszínt feketére módosítja a felugró ablak `fillColor` lehetőségével. A Bezárás gomb a `shoCloseButton` beállítás hamis értékre állításával távolítható el. A felugró ablak HTML-tartalma a felugró ablak széleitől számított 10 képpontot használ, és a szöveg fehér színűvé válik, így szépen jelenik meg a fekete háttéren.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Testreszabott előugró ablak" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) által a <a href='https://codepen.io'>CodePen</a> <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>testreszabott előugró ablakát</a> .
</iframe>

## <a name="popup-events"></a>Felugró események

Az előugró ablakok megnyithatók, lezárhatók és áthúzhatók. Az előugró osztály olyan eseményeket biztosít, amelyek segítségével a fejlesztők reagálni tudnak ezekre a műveletekre. Az alábbi példa kiemeli, hogy az előugró ablak megnyitásakor, zárásakor vagy húzásakor milyen események égetése következik be. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Felugró események" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>felugró eseményeit</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

A következő nagyszerű cikkekből megtudhatja a kódok teljes mintáit:

> [!div class="nextstepaction"]
> [Szimbólum réteg hozzáadása](./map-add-pin.md)

> [!div class="nextstepaction"]
> [HTML-jelölő hozzáadása](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Vonal rétegének hozzáadása](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszög réteg hozzáadása](map-add-shape.md)