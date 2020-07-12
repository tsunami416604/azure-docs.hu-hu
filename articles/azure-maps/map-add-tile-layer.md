---
title: Csempe réteg hozzáadása térképhez | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan fedi le egy csempét a térképeken a Microsoft Azure Maps web SDK használatával. A csempe rétegek lehetővé teszik képek megjelenítését egy térképen.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 6bb3cfa7266688ac8973bd3838d0d03e9efe8d50
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86242304"
---
# <a name="add-a-tile-layer-to-a-map"></a>Csemperéteg hozzáadása térképhez

Ez a cikk bemutatja, hogyan fedi le a csempéket a térképre. A csempe rétegek lehetővé teszik, hogy az alapszintű Térkép csempék fölé írja a képeket Azure Maps. A Azure Maps csempe rendszerével kapcsolatos további információkért lásd: [nagyítási szintek és csempék rácsa](zoom-levels-and-tile-grid.md).

Egy csempe réteg tölti be a csempéket egy kiszolgálóról. Ezeket a lemezképeket lehet előre megjeleníteni vagy dinamikusan megjeleníteni. Az előre megjelenített lemezképek, mint a kiszolgálók bármely más lemezképe, egy elnevezési konvenció használatával, amely a csempe rétegének ismerete. A dinamikusan renderelt képek egy szolgáltatás használatával töltik be a képeket valós időben. Az Azure Maps [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) osztály három különböző csempe-szolgáltatási elnevezési konvenciót támogat: 

* X, Y, nagyítás jelölése – az X az oszlop, az Y a csempe rácsában lévő csempe sora, a nagyítási szint pedig a nagyítási szint alapján van megadva.
* Quadkey-jelölés – az x, y és nagyítási adatokat egyetlen karakterlánc-értékre kombinálja. Ez a karakterlánc-érték egyetlen csempe egyedi azonosítója lesz.
* Határolókeret – a határolókeret koordinátáit tartalmazó képet ad meg: `{west},{south},{east},{north}` . Ezt a formátumot általában a [webes leképezési szolgáltatások (WMS)](https://www.opengeospatial.org/standards/wms)használják.

> [!TIP]
> A [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) nagyszerű lehetőséget mutat a nagyméretű adathalmazok megjelenítésére a térképen. Nem csak a csempe réteg hozható létre egy képből, a vektoros adatok csempe rétegként is megjeleníthető. Ha a vektoros adatmegjelenítést csempe rétegként jeleníti meg, a Térkép vezérlőelemnek csak az általuk képviselt adatmennyiségnél kisebb méretű csempéket kell betöltenie. Ezt a technikát általában több millió adatsor megjelenítésére használják a térképen.

A csempe rétegbe átadott csempe URL-címnek http vagy HTTPS URL-címnek kell lennie egy TileJSON-erőforráshoz vagy egy csempe URL-sablonhoz, amely a következő paramétereket használja: 

* `{x}`-A csempe X pozíciója `{y}`A és a is szükséges `{z}` .
* `{y}`-A csempe Y pozíciója `{x}`A és a is szükséges `{z}` .
* `{z}`– A csempe nagyítási szintje `{x}`A és a is szükséges `{y}` .
* `{quadkey}`– Csempe quadkey-azonosítója a Bing Maps csempe rendszerelnevezési konvenciója alapján.
* `{bbox-epsg-3857}`– Egy határolókeret karakterlánca, amely a `{west},{south},{east},{north}` EPSG 3857 térbeli hivatkozási rendszer formátumát jelöli.
* `{subdomain}`– Az altartomány értékeinek helyőrzője, ha meg van adva, a `subdomain` hozzá lesz adva.
* `{azMapsDomain}`– A Térkép által használt értékekkel összehangolja a tartományt és a csempe-kérelmek hitelesítését.

## <a name="add-a-tile-layer"></a>Mozaikréteg hozzáadása

 Ez a minta bemutatja, hogyan hozhat létre csempéket tartalmazó csempe réteget. Ez a példa az x, y, zoom csempe rendszerét használja. Ennek a csempe rétegnek a forrása az [Iowa Állami Egyetem Iowa környezeti Mesonet](https://mesonet.agron.iastate.edu/ogc/)származó időjárási radar. A radar-információk megtekintésekor ideális esetben a felhasználók egyértelműen megtekinthetik a városok feliratait, ahogy azok a térképen navigálnak. Ez a viselkedés úgy valósítható meg, ha beszúrja a csempe réteget a `labels` réteg alá.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Réteg csempe X, Y és Z használatával' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>csempe réteget X, Y és Z használatával</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) értékkel a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Csempe rétegének testreszabása

A csempe réteg osztályának számos stílusa van. Itt látható egy eszköz, amellyel kipróbálhatja őket.

<br/>

<iframe height='700' scrolling='no' title='Csempe rétegének beállításai' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>csempe rétegének beállításait</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Rendszerképréteg hozzáadása](./map-add-image-layer.md)
