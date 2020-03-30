---
title: Szimbólumréteg hozzáadása térképhez | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan szabhatja testre a Szimbólum réteget egy szimbólum testreszabásához, és hogyan adhat hozzá szimbólumokat a térképhez a Microsoft Azure Maps Web SDK használatával.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b8d131dcc798fb2fe1d4bb650cd5b0a68903381b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209698"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Szimbólumréteg hozzáadása térképhez

Szimbólum csatlakoztatása adatforráshoz, és egy ikon vagy szöveg megjelenítésére használható egy adott ponton. 

A szimbólumrétegek a WebGL segítségével jelennek meg. Szimbólumréteg használatával nagy pontgyűjteményeket jeleníthet meg a térképen. A HTML-jelölőhöz képest a szimbólumréteg nagyszámú pontadatot jelenít meg a térképen, jobb teljesítménnyel. A szimbólumréteg azonban nem támogatja a hagyományos CSS és HTML elemeket a formázáshoz.  

> [!TIP]
> A szimbólumrétegek alapértelmezés szerint az adatforrás összes geometriájának koordinátáit jelenítik meg. Ha úgy szeretné korlátozni a réteget, hogy `filter` az csak a `['==', ['geometry-type'], 'Point']` `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` pontgeometriai jellemzőket jelenítse meg, a réteg tulajdonságát a réteg revagy ra állíthatja be, multipoint funkciókat is megadhat.

A térképek kép sprite manager betölti az egyéni képek által használt szimbólum réteg. A következő képformátumokat támogatja:

- Jpeg
- PNG
- Svg
- BMP
- GIF (animációk nélkül)

## <a name="add-a-symbol-layer"></a>Szimbólumréteg hozzáadása

Mielőtt szimbólumréteget adhatna a térképhez, néhány lépést kell tennie. Először hozzon létre egy adatforrást, és adja hozzá a térképhez. Hozzon létre egy szimbólumréteget. Ezután adja át az adatforrást a szimbólumrétegnek, hogy lekérje az adatokat az adatforrásból. Végül adjon hozzá adatokat az adatforráshoz, hogy legyen valami renderelésre. 

Az alábbi kód bemutatja, hogy mit kell hozzáadni a térképhez a betöltés után. Ez a minta egyetlen pontot jelenít meg a térképen egy szimbólumréteg használatával. 

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

A térképhez négy különböző típusú pontadat adható hozzá:

- GeoJSON-pont geometriája – Ez az objektum csak egy pont koordinátáját tartalmazza, semmi mást. A `atlas.data.Point` segítő osztály segítségével könnyen létre ezeket az objektumokat.
- GeoJSON MultiPoint geometria – Ez az objektum több pont koordinátáit tartalmazza, semmi mást. A `atlas.data.MultiPoint` segítő osztály segítségével könnyen létre ezeket az objektumokat.
- GeoJSON-szolgáltatás – Ez az objektum bármely GeoJSON geometriából és a geometriához társított metaadatokat tartalmazó tulajdonságokból áll. A `atlas.data.Feature` segítő osztály segítségével könnyen létre ezeket az objektumokat.
- `atlas.Shape`osztály hasonló a GeoJSON funkcióhoz. Mindkettő GeoJSON geometriából és a geometriához társított metaadatokat tartalmazó tulajdonságokból áll. Ha egy GeoJSON-objektumot hozzáad egy adatforráshoz, könnyen renderelhető egy rétegben. Ha azonban a GeoJSON objektum koordináta-tulajdonsága frissül, az adatforrás és a térkép nem változik. Ez azért van, mert nincs mechanizmus a JSON objektumban a frissítés indításához. Az alakzatosztály funkciókat biztosít a benne lévő adatok frissítéséhez. A módosítás kor a rendszer automatikusan értesíti és frissíti az adatforrást és a térképet. 

A következő kódminta létrehoz egy GeoJSON-pont `atlas.Shape` geometriát, és átadja azt az osztálynak, hogy megkönnyítse a frissítést. A térkép közepét kezdetben egy szimbólum megjelenítésére használják. A térképhez kattintási esemény kerül a térképre, így az egér koordinátái az alakzatok `setCoordinates` funkcióval lesznek használatba. Az egér koordinátáit a rendszer a kattintási esemény időpontjában rögzíti. Ezután `setCoordinates` a frissítések helyét a szimbólum a térképen.

<br/>

<iframe height='500' scrolling='no' title='Pin helyének váltása' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Tollváltó</a> pin<a href='https://codepen.io/azuremaps'>@azuremaps</a>helyét az Azure Maps ( ) szerint a <a href='https://codepen.io'>CodePen-en.</a>
</iframe>

> [!TIP]
> Alapértelmezés szerint a szimbólumrétegek optimalizálják a szimbólumok megjelenítését az átfedő szimbólumok elrejtésével. Nagyítás közben a rejtett szimbólumok láthatóvá válnak. Ha le szeretné tiltani ezt a funkciót, `allowOverlap` és mindig `iconOptions` az `true`összes szimbólumot meg szeretné jelenni, állítsa a beállítások tulajdonságát a-ra.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Egyéni ikon hozzáadása szimbólumréteghez

A szimbólumrétegek a WebGL segítségével jelennek meg. Így minden erőforrást, például az ikonképeket be kell tölteni a WebGL környezetbe. Ez a minta bemutatja, hogyan adhat hozzá egyéni ikont a térképerőforrásokhoz. Ezt az ikont használja a pontadatok renderelése egyéni szimbólummal a térképen. A `textField` szimbólumréteg tulajdonsága kifejezés megírásához szükséges. Ebben az esetben szeretnénk, hogy a hőmérséklet tulajdonság. Mivel a hőmérséklet egy szám, azt karakterláncsá kell alakítani. Továbbá azt akarjuk, hogy csatolja "° F" hozzá. Az összefűzéshez kifejezés használható; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Egyéni szimbólum képikonja' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Egyéni szimbólumkép ikonját</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) segítségével a <a href='https://codepen.io'>CodePen-en.</a>
</iframe>

> [!TIP]
> Az Azure Maps webes SDK számos testreszabható képsablont biztosít, amelyeket a szimbólumréteggel használhat. További információt a [Képsablonok használata](how-to-use-image-templates-web-sdk.md) című dokumentumban talál.

## <a name="customize-a-symbol-layer"></a>Szimbólumréteg testreszabása 

A szimbólumréteg számos formázási lehetőséggel rendelkezik. Itt van egy eszköz, hogy teszteljék ezeket a különböző stílus lehetőségeket.

<br/>

<iframe height='700' scrolling='no' title='Szimbólumréteg beállításai' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Toll <a href='https://codepen.io/azuremaps/pen/PxVXje/'>szimbólumréteg beállításait</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) segítségével a <a href='https://codepen.io'>CodePen-en.</a>
</iframe>

> [!TIP]
> Ha csak a szimbólumréteget tartalmazó szöveget szeretné megjeleníteni, `image` elrejtheti az ikont, ha az ikon beállítások tulajdonságát a számára állítja be. `'none'`

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Szimbólumréteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Ikonopciók](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TextOptions (Szövegbeállításai)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

További kódmintákat a térképekhez további kódmintákért tekintse meg:

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
