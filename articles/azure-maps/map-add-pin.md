---
title: Szimbólum hozzáadása a Azure Mapshoz | Microsoft Docs
description: Szimbólumok hozzáadása a Azure Maps web SDK-hoz.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 10f6a7ef92bfd6558ed93e7fb40df9e48e1b92f5
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976175"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Szimbólum réteg hozzáadása térképhez

Egy szimbólum egy adatforráshoz is csatlakoztatható, és egy ikon és/vagy szöveg megjelenítésére szolgál egy adott ponton. A szimbólumok rétegei a WebGL használatával jelennek meg, és a térképeken nagy gyűjteményeket lehet megjeleníteni. Ez a réteg sokkal több pont-adat megjelenítését teszi lehetővé a térképen, jó teljesítménnyel, mint a HTML-jelölők használatával. A szimbólum réteg azonban nem támogatja a hagyományos CSS-és HTML-elemeket a stílushoz.  

> [!TIP]
> A szimbólumok alapértelmezés szerint az adatforrásban lévő összes geometriá koordinátáit fogják megjeleníteni. Ha korlátozni szeretné a réteget úgy, hogy az csak a pont geometriai `filter` funkcióit jelenítse meg `['==', ['geometry-type'], 'Point']` , `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` állítsa a réteg tulajdonságát a (vagy) értékre, ha a multipoint-szolgáltatásokat is szeretné használni.

## <a name="add-a-symbol-layer"></a>Szimbólumréteg hozzáadása

Ha szimbólum réteget szeretne hozzáadni a térképhez és az adatmegjelenítéshez, először létre kell hoznia egy adatforrást, és hozzá kell adnia a térképet. Ezután létrehozhatja és átadhat egy szimbólum réteget az adatforrásban az adatok lekéréséhez. Végül fel kell venni az adatforrásba az adatforrást, hogy meg lehessen jeleníteni valamit. A következő kód azt a kódot mutatja be, amelyet a térképhez kell hozzáadni, miután betöltötte azt, hogy a Térkép egyetlen pontját jeleníti meg egy szimbólum réteg használatával. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

A térképhez négy különböző típusú pont-adattípust lehet hozzáadni:

- GeoJSON pont geometriája – ez az objektum csak egy pont koordinátáit tartalmazza, semmi más. A `atlas.data.Point` segítő osztály használatával egyszerűen hozhatók létre ezek az objektumok.
- GeoJSON multipoint geometria – Ez az objektum több pont koordinátáit tartalmazza, de semmi mást nem. A `atlas.data.MultiPoint` segítő osztály használatával egyszerűen hozhatók létre ezek az objektumok.
- GeoJSON funkció – ez az objektum bármilyen GeoJSON geometriát és olyan tulajdonságokat tartalmaz, amelyek a geometriához társított metaadatokat tartalmaznak. A `atlas.data.Feature` segítő osztály használatával egyszerűen hozhatók létre ezek az objektumok.
- `atlas.Shape`az osztály hasonló a GeoJSON szolgáltatáshoz, amely egy GeoJSON geometriát és olyan tulajdonságokat tartalmaz, amelyek a geometriához társított metaadatokat tartalmaznak. Ha egy GeoJSON objektumot ad hozzá egy adatforráshoz, könnyen megjeleníthető egy rétegben, azonban ha a GeoJSON objektum koordináták tulajdonsága frissül, akkor az adatforrás és a leképezés nem változik, mert a JSON-objektumban nincs olyan mechanizmus, amely egy frissítést indít el. Az Shape osztály a benne foglalt adatok frissítésére, valamint a módosítás megváltozására szolgáló funkciókat biztosít, az adatforrást és a leképezést automatikusan értesíti és frissíti. 

Az alábbi mintakód egy GeoJSON pontot hoz létre, és átadja azt az `atlas.Shape` osztálynak, hogy könnyen frissíthető legyen. A Térkép középpontját kezdetben egy szimbólum megjelenítéséhez használjuk. A térképhez egy kattintást jelző eseményt adnak hozzá, amely azt jelzi, hogy az egérrel való kattintások koordinátáit a rendszer az alakzatok `setCoordinates` függvénnyel használja, amely frissíti a szimbólum helyét a térképen.

<br/>

<iframe height='500' scrolling='no' title='PIN-kód helyének váltása' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>switch PIN-kód helyét</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> A teljesítmény érdekében a szimbólum-rétegek alapértelmezés szerint az átfedésben lévő szimbólumok elrejtésével optimalizálja a szimbólumok megjelenítését. Ahogy a rejtett szimbólumok nagyítása láthatóvá válik. A funkció letiltásához és az összes szimbólum megjelenítéséhez állítsa be a `allowOverlap` `iconOptions` beállítások `true`tulajdonságát a következőre:.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Egyéni ikon hozzáadása egy szimbólum réteghez

A szimbólumok rétegei a WebGL használatával jelennek meg. Ennek megfelelően az összes erőforrást, például ikonokat be kell tölteni a WebGL-környezetbe. Ez a minta bemutatja, hogyan adhat hozzá egyéni ikont a Térkép erőforrásaihoz, majd használhatja azt a Térkép egyéni szimbólumának megjelenítéséhez. A szimbólum réteg tulajdonságának meg kell adni egy kifejezést. `textField` Ebben az esetben a hőmérséklet tulajdonságot szeretnénk megjeleníteni, de mivel ez egy szám, karakterlánccá kell alakítani. Emellett szeretnénk hozzáfűzni a "°F"-t is. Ezt a kifejezést használhatja; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Egyéni szimbólum képe ikon' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Egyéni szimbólumának</a> ikonját<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> A Azure Maps web SDK számos testreszabható képsablont biztosít, amelyeket a szimbólum rétegben használhat. További részletekért lásd a képsablonokkal kapcsolatos dokumentum [használatát](how-to-use-image-templates-web-sdk.md) ismertető témakört.

## <a name="customize-a-symbol-layer"></a>Szimbólum réteg testreszabása 

A szimbólum rétegben számos lehetőség áll rendelkezésre. Az alábbi eszköz segítségével tesztelheti ezeket a különböző stílusokat.

<br/>

<iframe height='700' scrolling='no' title='Szimbólum réteg beállításai' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll szimbólumának rétegbeli<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/PxVXje/'>beállításait</a> Azure Maps () alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Ha csak szimbólum réteggel kívánja megjeleníteni a szöveget, elrejtheti a ikont az ikon `image` `'none'`beállításainak tulajdonságának beállításával.

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Felugró ablak hozzáadása](map-add-popup.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stílusú kifejezések használata](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [A képsablonok használata](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Vonal rétegének hozzáadása](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszög réteg hozzáadása](map-add-shape.md)

> [!div class="nextstepaction"]
> [Buborék réteg hozzáadása](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [HTML-készítők hozzáadása](map-add-bubble-layer.md)
