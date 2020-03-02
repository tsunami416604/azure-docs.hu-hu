---
title: Felugró ablak hozzáadása egy ponthoz a térképen | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá egy előugró pontot egy ponthoz a Microsoft Azure Maps web SDK használatával.
author: jingjing-z
ms.author: jinzh
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 588de08666930937c3ad965b2609f8e207b75eca
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78208848"
---
# <a name="add-a-popup-to-the-map"></a>Felugró ablak hozzáadása a térképhez

Ez a cikk bemutatja, hogyan adhat hozzá egy előugró pontot egy térképen.

## <a name="understand-the-code"></a>A kód értelmezése

A következő kód egy pont funkciót helyez el, amely `name` és `description` tulajdonságokkal rendelkezik a térképhez egy szimbólum réteg használatával. Az [előugró osztály](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) egy példánya jön létre, de nem jelenik meg. Az Egérgombok az előugró ablak megnyitásának és bezárásának elindításához lesznek hozzáadva a szimbólum réteghez. Ha a jelölő szimbólum látható, az előugró ablak `position` tulajdonsága a jelölő pozíciójának megfelelően frissül, és a `content` lehetőség frissül, és egy olyan HTML-kódot tartalmaz, amely betakarja a pont szolgáltatás `name` és `description` tulajdonságait. Ekkor megjelenik az előugró ablak a térképen a `open` függvény használatával.

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

<iframe height='500' scrolling='no' title='Felugró ablak hozzáadása a Azure Maps használatával' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io'>CodePen</a>Azure Maps Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>használatával</a> .
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Felugró ablak újrahasználata több ponttal

Vannak olyan esetek, amikor a legjobb módszer egy előugró ablak létrehozása és újbóli felhasználása. Előfordulhat például, hogy nagy számú ponttal rendelkezik, és egyszerre csak egy előugró ablak jelenik meg. A felugró ablak újrafelhasználásával jelentősen csökken az alkalmazás által létrehozott DOM-elemek száma, ami jobb teljesítményt nyújt. Az alábbi minta 3 pontos funkciókat hoz létre. Ha bármelyikre kattint, megjelenik egy előugró ablak, amely az adott pont funkciójának tartalmát jeleníti meg.

<br/>

<iframe height='500' scrolling='no' title='Felugró ablak újrahasználata több PIN-kód használatával' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti a tollat a <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>felugró ablak újrafelhasználásával több PIN</a> -kódra.
</iframe>

## <a name="customizing-a-popup"></a>Felugró ablak testreszabása

Alapértelmezés szerint az előugró ablak fehér háttérrel, a lenti mutató nyílra, valamint a jobb felső sarokban található Bezárás gombra mutat. Az alábbi minta a háttérszínt feketére módosítja a felugró ablak `fillColor` lehetőségével. A Bezárás gomb a `CloseButton` beállítás hamis értékre állításával távolítható el. Az előugró ablak HTML-tartalma 10 képpont kitöltését használja az előugró ablak széleiből. A szöveg fehér színű, így szépen megjelenik a fekete háttéren.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Testreszabott előugró ablak" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) által a <a href='https://codepen.io'>CodePen</a> <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>testreszabott előugró ablakát</a> .
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Felugró sablonok hozzáadása a térképhez

Az előugró sablonok segítségével egyszerűen hozhat létre adatvezérelt elrendezéseket az előugró ablakokhoz. Az alábbi szakaszokban számos előugró sablon használható a formázott tartalom létrehozásához a szolgáltatások tulajdonságai segítségével.

### <a name="string-template"></a>Karakterlánc-sablon

A karakterlánc-sablon a szolgáltatás tulajdonságainak értékeivel helyettesíti a helyőrzőket. A szolgáltatás tulajdonságait nem kell karakterlánc típusú értékhez rendelni. `value1` például egy egész számot tárol. A rendszer ezeket az értékeket továbbítja a `popupTemplate`Content (tartalom) tulajdonságához. 

A `numberFormat` beállítás a megjelenítendő szám formátumát adja meg. Ha a `numberFormat` nincs megadva, akkor a kód az előugró sablonok dátumának formátumát fogja használni. A `numberFormat` lehetőség a [Number. toLocaleString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) függvény használatával formázza a számokat. Nagy számok formázásához érdemes lehet a [NumberFormat. Format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format)függvényeket használni a `numberFormat` kapcsolóval. Például az alábbi kódrészlet `maximumFractionDigits`t használ a tört számjegyek számának két értékre való korlátozásához.

> [!Note]
> A karakterlánc-sablonnak csak egy módja lehet képek megjelenítésére. Először is a karakterlánc-sablonnak rendelkeznie kell egy képcímkével. A képcímkének átadott értéknek a rendszerkép URL-címének kell lennie. Ezután a karakterlánc-sablonnak `isImage` True értékre kell állítania a `HyperLinkFormatOptions`ban. A `isImage` beállítással megadhatja, hogy a hiperhivatkozás egy képhez legyen betöltve, és a hiperhivatkozás betöltődik egy képcímkébe. Ha a hiperhivatkozásra kattint, megnyílik a rendszerkép.

```javascript
new atlas.data.Feature(new atlas.data.Point([-20, -20]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    popupTemplate: {
        content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
        numberFormat: {
            maximumFractionDigits: 2
        }
    }
}),
```

### <a name="propertyinfo-template"></a>PropertyInfo-sablon

A PropertyInfo sablon a szolgáltatás elérhető tulajdonságait jeleníti meg. A `label` beállítás megadja a felhasználónak megjelenítendő szöveget. Ha `label` nincs megadva, a hiperhivatkozás megjelenik. Ha pedig a hiperhivatkozás egy rendszerkép, az "ALT" címkéhez rendelt érték jelenik meg. A `dateFormat` megadja a dátum formátumát, és ha nincs megadva a dátumformátum, a dátum karakterláncként jelenik meg. A `hyperlinkFormat` lehetőség a kattintható hivatkozásokat jeleníti meg, hasonlóan a `email` lehetőség is használható a kattintható e-mail-címek megjelenítéséhez.

Mielőtt a PropertyInfo sablon megjeleníti a tulajdonságokat a végfelhasználónak, rekurzív módon ellenőrzi, hogy a tulajdonságok valóban meg vannak-e adva a szolgáltatáshoz. A stílus és a cím tulajdonságainak megjelenítését is figyelmen kívül hagyja. Például nem fog megjelenni `color`, `size`, `anchor`, `strokeOpacity`és `visibility`. Tehát ha a tulajdonságok elérési útjának ellenőrzése befejeződött a háttérben, a PropertyInfo sablon táblázatos formátumban jeleníti meg a tartalmat.

```javascript
new atlas.data.Feature(new atlas.data.Point([20, -20]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com',
    popupTemplate: {
        content: [{
    propertyPath: 'createDate',
    label: 'Created Date'
    },
    {
    propertyPath: 'dateNumber',
    label: 'Formatted date from number',
    dateFormat: {
        weekday: 'long',
        year: 'numeric',
        month: 'long',
        day: 'numeric',
        timeZone: 'UTC',
        timeZoneName: 'short'
    }
    },
    {
    propertyPath: 'url',
    label: 'Code samples',
    hideLabel: true,
    hyperlinkFormat: {
        lable: 'Go to code samples!',
        target: '_blank'
    }
    },
    {
    propertyPath: 'email',
    label: 'Email us',
    hideLabel: true,
    hyperlinkFormat: {
        target: '_blank',
        scheme: 'mailto:'
        }
    }
        ]
    }
}),

```

### <a name="multiple-content-templates"></a>Több tartalmi sablon

A szolgáltatás a karakterlánc-sablon és a PropertyInfo-sablon együttes használatával is megjelenítheti a tartalmakat. Ebben az esetben a karakterlánc-sablon a helyőrző értékeket fehér háttérként jeleníti meg.  A és a PropertyInfo-sablon egy teljes szélességű képet jelenít meg egy táblában. A példában szereplő tulajdonságok hasonlóak az előző mintákban ismertetett tulajdonságokhoz.

```javascript
new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg',
    popupTemplate: {
    content: [
      'This template has two pieces of content; a string template with placeholders and a array of property info which renders a full width image.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
      [{
        propertyPath: 'imageLink',
        label: 'Image',
        hideImageLabel: true,
        hyperlinkFormat: {
          isImage: true
        }
      }]
    ],
    numberFormat: {
      maximumFractionDigits: 2
    }
    }
    }),
]);
```

### <a name="points-without-a-defined-template"></a>Meghatározott sablon nélküli pontok

Ha az előugró sablon nincs definiálva karakterlánc-sablonként, PropertyInfo-sablonként vagy mindkettő kombinációja, akkor az alapértelmezett beállításokat használja. Ha a `title` és a `description` az egyetlen hozzárendelt tulajdonság, a felugró sablon fehér hátteret, a jobb felső sarokban található bezárás gombot jeleníti meg. A kis-és közepes képernyőkön pedig az alján egy nyíl látható. Az alapértelmezett beállítások egy táblában szerepelnek a `title` és a `description`tól eltérő összes tulajdonsághoz. Még ha az alapértelmezett beállításokra is visszaesik, az előugró sablon programozott módon is kezelhető. Például a felhasználók kikapcsolhatják a hiperhivatkozások észlelését, és az alapértelmezett beállítások továbbra is érvényesek lesznek a többi tulajdonságra.

Kattintson a pontokra a térképen a CodePen. A térképen a következő felugró sablonok mindegyike látható: karakterlánc-sablon, PropertyInfo-sablon és több tartalom sablon. A sablonok az alapértelmezett beállításokkal való megjelenítésének módját is három pont mutatja.

<br/>

<iframe height='500' scrolling='no' title='PopupTemplates' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a toll <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reuse-popup-template"></a>Felbukkanó sablon újbóli felhasználása

A felugró ablak újrafelhasználásához hasonlóan felhasználhatja a felugró sablonokat is. Ez a megközelítés akkor lehet hasznos, ha egyszerre csak egy előugró sablont szeretne megjeleníteni, több pontra. Az előugró sablon újrafelhasználásával csökken az alkalmazás által létrehozott DOM-elemek száma, ami javítja az alkalmazás teljesítményét. A következő minta ugyanazt a felugró sablont használja három pontra. Ha bármelyikre kattint, megjelenik egy előugró ablak, amely az adott pont funkciójának tartalmát jeleníti meg.

<br/>

<iframe height='500' scrolling='no' title='ReusePopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a toll <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplate</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Felugró események

Az előugró ablakok megnyithatók, lezárhatók és áthúzhatók. Az előugró osztály olyan eseményeket biztosít, amelyek segítségével a fejlesztők reagálni tudnak ezekre az eseményekre. Az alábbi példa azt mutatja be, hogy a felhasználó mikor nyitja meg, zárja be vagy húzza az előugró ablakokat. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Felugró események" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>felugró eseményeit</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Lakosság](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

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
