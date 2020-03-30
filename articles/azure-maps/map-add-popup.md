---
title: Felugró ablak hozzáadása a térkép egy pontjához | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá felugró ablakot egy ponthoz a Microsoft Azure Maps Web SDK használatával.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cf6424d2a6cbcfb7c5052201b5a9190c81fddaff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055955"
---
# <a name="add-a-popup-to-the-map"></a>Előugró ablak hozzáadása a térképhez

Ez a cikk bemutatja, hogyan adhat felugró ablakot a térkép egy pontjához.

## <a name="understand-the-code"></a>A kód értelmezése

A következő kód egy pontjellemzőt `description` ad hozzá a térképhez, amely nek van `name` és tulajdonságai vannak egy szimbólumréteg használatával. Létrejön az [előugró osztály](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup) egy példánya, de nem jelenik meg. Az egéresemények hozzáadódnak a szimbólumréteghez, hogy elindítsák az előugró ablak megnyitását és bezárását. Amikor a jelölő szimbólum rámutat, a `position` felugró ablak tulajdonsága frissül `content` a jelölő pozíciójával, és `name` `description` a beállítás néhány HTML-kóddal frissül, amely az egérmutatót tartalmazó pontjellemző és tulajdonságainak körülfolyatása. A felugró ablak ezután a `open` funkciójával jelenik meg a térképen.

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

Az alábbiakban a fenti funkciók teljes futókód-mintája látható.

<br/>

<iframe height='500' scrolling='no' title='Előugró ablak hozzáadása az Azure Maps használatával' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Toll <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>hozzáadása előugró ablakot az Azure Maps</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) segítségével a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Felugró ablak újrafelhasználása több ponttal

Vannak olyan esetek, amikor a legjobb megközelítés az, hogy hozzon létre egy felugró és újra azt. Előfordulhat például, hogy nagy számú ponttal rendelkezik, és egyszerre csak egy felugró ablakot szeretne megjeleníteni. A felugró ablak újbóli felhasználásával az alkalmazás által létrehozott DOM-elemek száma jelentősen csökken, ami jobb teljesítményt nyújthat. A következő minta 3 pontos funkciókat hoz létre. Ha bármelyikre kattint, egy felugró ablak jelenik meg az adott pontfunkció tartalmával.

<br/>

<iframe height='500' scrolling='no' title='Felugró ablak újrafelhasználása több pinekkel' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>újrafelhasználása popup több pin</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Előugró ablak testreszabása

Alapértelmezés szerint a felugró ablak fehér háttérrel, alul mutatónyíllal, a jobb felső sarokban pedig bezárás gombbal rendelkezik. A következő minta a felugró `fillColor` ablak beállításával feketére változtatja a háttérszínt. A bezárás gomb a `CloseButton` beállítás hamis beállításával törlődik. A felugró ablak HTML-tartalma 10 képpontnyi párnázott at használ a felugró ablak szélétől. A szöveg fehér, így szépen megjelenik a fekete háttéren.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Testreszabott előugró ablak" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg az Azure Maps által<a href='https://codepen.io/azuremaps'>@azuremaps</a>testre szabott pen <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>testreszabott előugró ablakot</a> ( ) a <a href='https://codepen.io'>CodePen-en.</a>
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Előugró sablonok hozzáadása a térképhez

Az előugró sablonok megkönnyítik az adatalapú elrendezések létrehozását az előugró ablakokhoz. Az alábbi szakaszok bemutatják a különböző előugró sablonok használatát a formázott tartalom létrehozásához a szolgáltatások tulajdonságainak használatával.

> [!NOTE]
> Alapértelmezés szerint az előugró sablont használó összes tartalom biztonsági szolgáltatásként az iframe-en belül lesz. Vannak azonban korlátozások:
>
> - Minden parancsfájl, űrlap, mutatózárolás és felső navigációs funkció le van tiltva. A hivatkozások a kattintáskor új lapon nyílhatnak meg. 
> - Azok a régebbi böngészők, `srcdoc` amelyek nem támogatják a paramétert az iframe-eken, kis mennyiségű tartalom megjelenítésére korlátozódnak.
> 
> Ha megbízik a felugró ablakokba betöltött adatokban, és potenciálisan azt szeretné, hogy ezek a parancsfájlok előugró ablakokba töltődjenek be, akkor ezt letilthatja az előugró sablonok `sandboxContent` beállításfalsera állításával. 

### <a name="string-template"></a>Karakterláncsablon

A Karakterlánc sablon a helyőrzőket a jellemzőtulajdonságok értékeire cseréli. A szolgáltatás tulajdonságaihoz nem kell Karakterlánc típusú értéket rendelni. Például `value1` egy egész szám van. Ezeket az értékeket ezután `popupTemplate`átadják a tartalom tulajdonságának. 

A `numberFormat` beállítás a megjelenítendő szám formátumát adja meg. Ha `numberFormat` a nincs megadva, akkor a kód az előugró sablonok dátumformátumát fogja használni. A `numberFormat` beállítás a [Számok.toLocaleString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) függvénnyel formázza a számokat. Nagy számok formázásához használja `numberFormat` a beállítást a [NumberFormat.format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format)függvényekkel. Az alábbi `maximumFractionDigits` kódrészlet például a törtszámjegyek számát kettőre korlátozza.

> [!Note]
> A Karakterlánc-sablon csak egy módon jelenítheti meg a képeket. Először a Karakterlánc sablonban képcímkével kell rendelkeznie. A képcímkének átadott értéknek egy kép URL-címének kell lennie. Ezután a Karakterlánc sablonnak true értékre kell állítanom `isImage` a `HyperLinkFormatOptions`ban. A `isImage` beállítás azt adja meg, hogy a hivatkozás képhez van,és a hivatkozás betöltődik egy képcímkába. Amikor a hivatkozásra kattint, megnyílik a kép.

```javascript
var templateOptions = {
  content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6]
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="propertyinfo-template"></a>PropertyInfo sablon

A PropertyInfo sablon megjeleníti a szolgáltatás elérhető tulajdonságait. A `label` beállítás a felhasználó számára megjelenítendő szöveget adja meg. Ha `label` nincs megadva, akkor a hivatkozás jelenik meg. Ha pedig a hivatkozás kép, az "alt" címkéhez rendelt érték jelenik meg. A `dateFormat` megadott dátum formátuma, és ha a dátumformátum nincs megadva, akkor a dátum karakterláncként jelenik meg. A `hyperlinkFormat` lehetőség teszi kattintható linkek, `email` hasonlóképpen, a lehetőséget lehet használni, hogy kattintható e-mail címeket.

Mielőtt a PropertyInfo sablon megjelenítené a tulajdonságokat a végfelhasználó számára, rekurzív módon ellenőrzi, hogy a tulajdonságok valóban definiálva vannak-e az adott szolgáltatáshoz. Figyelmen kívül hagyja a stílus- és címtulajdonságok megjelenítését is. Például nem `color`jelenik meg, `anchor` `strokeOpacity` `visibility` `size`, , , és . Így ha a tulajdonságútvonal ellenőrzése befejeződött a háttérben, a PropertyInfo sablon táblaformátumban jeleníti meg a tartalmat.

```javascript
var templateOptions = {
  content: [
    {
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
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com'
}),

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="multiple-content-templates"></a>Több tartalomsablon

A szolgáltatás a Karakterlánc sablon és a PropertyInfo sablon kombinációjával is megjelenítheti a tartalmat. Ebben az esetben a Karakterlánc sablon fehér háttéren jeleníti meg a helyőrzők értékeit.  A PropertyInfo sablon pedig teljes szélességű képet jelenít meg egy táblázatban. A mintában szereplő tulajdonságok hasonlóak az előző mintákban ismertetett tulajdonságokhoz.

```javascript
var templateOptions = {
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
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg'
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="points-without-a-defined-template"></a>Meghatározott sablon nélküli pontok

Ha az előugró sablon nincs string sablonként, PropertyInfo sablonként vagy a kettő kombinációjában definiálva, akkor az alapértelmezett beállításokat használja. Ha `title` a `description` és az egyetlen hozzárendelt tulajdonság, az előugró sablon fehér hátteret, a jobb felső sarokban lévő bezárás gombot jeleníti meg. És a kis és közepes képernyőkön egy nyíl látható az alján. Az alapértelmezett beállítások a és a `title` `description`tulajdonságon kívül az összes tulajdonságtáblán belül jelennek meg. Még akkor is, ha visszaesik az alapértelmezett beállításokra, az előugró sablon továbbra is programozott módon módosítható. A felhasználók például kikapcsolhatják a hivatkozásészlelést, és az alapértelmezett beállítások továbbra is érvényesek lesznek más tulajdonságokra.

Kattintson a térképpontjaira a CodePen-ben. A következő előugró sablonok mindegyikéhez van egy pont a térképen: Karakterláncsablon, PropertyInfo sablon és Több tartalomsablon. Három pont ból is megtudhatja, hogyan jelennek meg a sablonok az alapértelmezett beállításokkal.

<br/>

<iframe height='500' scrolling='no' title='Előugró sablonok' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) című témakört a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="reuse-popup-template"></a>Előugró sablon újrafelhasználása

Az előugró ablakok újrafelhasználásához hasonlóan újra felhasználhatja az előugró sablonokat. Ez a megközelítés akkor hasznos, ha egyszerre csak egy előugró sablont szeretne megjeleníteni több ponthoz. Az előugró sablon újbóli felhasználásával csökken az alkalmazás által létrehozott DOM-elemek száma, ami ezután javítja az alkalmazás teljesítményét. A következő minta ugyanazt az előugró sablont használja három ponthoz. Ha bármelyikre kattint, egy felugró ablak jelenik meg az adott pontfunkció tartalmával.

<br/>

<iframe height='500' scrolling='no' title='ÚjrafelhasználásPopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplate</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) című témakört a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="popup-events"></a>Előugró események

Az előugró ablakok megnyithatók, bezárhatók és áthúzhatók. Az előugró osztály eseményeket biztosít, amelyek segítik a fejlesztőket az ilyen eseményekre való reagálásban. A következő minta kiemeli, hogy mely események indulnak el, amikor a felhasználó megnyitja, bezárja vagy húzza az előugró ablakot. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Előugró események" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg az Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>pen-előugró eseményeit</a> ( ) a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Felugró ablak](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [Előugró beállítások](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [Előugró sablon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popuptemplate)

A teljes kódmintákhoz lásd az alábbi nagyszerű cikkeket:

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](./map-add-pin.md)

> [!div class="nextstepaction"]
> [HTML-jelölő hozzáadása](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](map-add-shape.md)
