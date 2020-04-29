---
title: Szimbólum-réteg hozzáadása térképhez | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan szabhatja testre a szimbólumokat a Symbol réteggel, és hogyan adhat hozzá szimbólumokat a térképen a Microsoft Azure Maps web SDK használatával.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b8d131dcc798fb2fe1d4bb650cd5b0a68903381b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77209698"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Szimbólum réteg hozzáadása térképhez

Csatlakoztasson egy szimbólumot egy adatforráshoz, és használja egy ikon vagy szöveg megjelenítésére egy adott ponton. 

A szimbólumok rétegei a WebGL használatával jelennek meg. A térképen a pontok nagy gyűjteményének megjelenítéséhez használjon szimbólum réteget. A HTML-jelölőhöz képest a szimbólum réteg nagy számú pontot jelenít meg a térképen a jobb teljesítmény érdekében. A szimbólum réteg azonban nem támogatja a hagyományos CSS-és HTML-elemeket a stílushoz.  

> [!TIP]
> A szimbólumok alapértelmezés szerint az adatforrásban lévő összes geometriá koordinátáit fogják megjeleníteni. Ha korlátozni szeretné a réteget úgy, hogy az csak a pont geometriai `filter` funkcióit állítsa be a `['==', ['geometry-type'], 'Point']` réteg `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` tulajdonságára, vagy ha kívánja, a multipoint-funkciókat is felveheti.

A Maps Image sprite Manager betölti a szimbólum réteg által használt egyéni lemezképeket. A következő képformátumokat támogatja:

- JPEG
- PNG
- SVG
- BMP
- GIF (nincs animáció)

## <a name="add-a-symbol-layer"></a>Szimbólumréteg hozzáadása

Ahhoz, hogy hozzá lehessen adni egy szimbólum réteget a térképhez, néhány lépést végre kell hajtania. Először hozzon létre egy adatforrást, és adja hozzá a térképhez. Hozzon létre egy szimbólum réteget. Ezután továbbítsa az adatforrást a szimbólum rétegbe az adatoknak az adatforrásból való lekéréséhez. Végül vegyen fel egy adatforrást az adatforrásba, hogy valami legyen megjelenítve. 

Az alábbi kód azt mutatja be, hogy mit kell hozzáadni a térképhez a betöltését követően. Ez a minta egy pontot jelenít meg a térképen egy szimbólum réteg használatával. 

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
- GeoJSON multipoint geometria – Ez az objektum több pont koordinátáit tartalmazza, és semmi mást nem. A `atlas.data.MultiPoint` segítő osztály használatával egyszerűen hozhatók létre ezek az objektumok.
- GeoJSON funkció – ez az objektum bármilyen GeoJSON geometriát és olyan tulajdonságokat tartalmaz, amelyek a geometriához társított metaadatokat tartalmaznak. A `atlas.data.Feature` segítő osztály használatával egyszerűen hozhatók létre ezek az objektumok.
- `atlas.Shape`az osztály hasonló a GeoJSON szolgáltatáshoz. Mindkettő egy GeoJSON geometriából és a geometriai metaadatokat tartalmazó készletből áll. Ha egy GeoJSON objektumot ad hozzá egy adatforráshoz, akkor könnyen megjeleníthető egy rétegben. Ha azonban a GeoJSON objektum koordináták tulajdonsága frissül, az adatforrás és a leképezés nem változik. Ennek az az oka, hogy a JSON-objektumban nincs olyan mechanizmus, amely egy frissítést indít el. Az Shape osztály a benne található adatok frissítésére szolgáló függvényeket biztosít. Módosítás esetén az adatforrás és a Térkép automatikusan értesítést kap és frissül. 

Az alábbi mintakód egy GeoJSON pontot hoz létre, és átadja azt az `atlas.Shape` osztálynak, hogy könnyen frissíthető legyen. A Térkép középpontja kezdetben egy szimbólum megjelenítésére szolgál. A térképhez egy kattintást jelző eseményt adnak hozzá, amely az egér koordinátáit használja a SHAPES `setCoordinates` függvénnyel. Az egér koordinátáit a click esemény időpontjában rögzítjük. Ezután `setCoordinates` frissíti a szimbólum helyét a térképen.

<br/>

<iframe height='500' scrolling='no' title='PIN-kód helyének váltása' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>switch PIN-kód helyét</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Alapértelmezés szerint a szimbólum-rétegek az átfedésben lévő szimbólumok elrejtésével optimalizálja a szimbólumok megjelenítését. A nagyításhoz a rejtett szimbólumok láthatóvá válnak. A funkció letiltásához és az összes szimbólum megjelenítéséhez állítsa be a `allowOverlap` `iconOptions` beállítások tulajdonságát a következőre: `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Egyéni ikon hozzáadása egy szimbólum réteghez

A szimbólumok rétegei a WebGL használatával jelennek meg. Ennek megfelelően az összes erőforrást, például ikonokat be kell tölteni a WebGL-környezetbe. Ez a minta bemutatja, hogyan adhat hozzá egyéni ikont a Térkép erőforrásaihoz. Ezt az ikont ezután a rendszer a Térkép egyéni szimbólumával jeleníti meg. A `textField` szimbólum réteg tulajdonságának meg kell adni egy kifejezést. Ebben az esetben a hőmérséklet tulajdonságot szeretnénk megjeleníteni. Mivel a hőmérséklet egy szám, azt karakterlánccá kell konvertálni. Emellett szeretnénk hozzáfűzni a "°F" kifejezést. Ehhez az összefűzéshez kifejezés használható. `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Egyéni szimbólum képe ikon' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Egyéni szimbólumának ikonját</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> A Azure Maps web SDK számos testreszabható képsablont biztosít, amelyeket a szimbólum rétegben használhat. További információ: a [képsablonokkal](how-to-use-image-templates-web-sdk.md) kapcsolatos dokumentum használata.

## <a name="customize-a-symbol-layer"></a>Szimbólum réteg testreszabása 

A szimbólum rétegben számos lehetőség áll rendelkezésre. Az alábbi eszköz segítségével tesztelheti ezeket a különböző stílusokat.

<br/>

<iframe height='700' scrolling='no' title='Szimbólum réteg beállításai' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/PxVXje/'>szimbólumának rétegbeli beállításait</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Ha csak szimbólum réteget szeretne megjeleníteni, elrejtheti a ikont az ikon beállításainak `image` tulajdonságának beállításával. `'none'`

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
> [Előugró ablak hozzáadása](map-add-popup.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Rendszerképsablonok használata](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](map-add-shape.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [HTML-készítők hozzáadása](map-add-bubble-layer.md)
