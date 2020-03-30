---
title: Mozaikréteg hozzáadása térképhez | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan fedheti le a mozaikréteget a térképen a Microsoft Azure Maps Web SDK használatával. A mozaikrétegek lehetővé teszik a képek térképen való megjelenítését.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 61d7a11df499e6b740adb45968721b6a9bb1af22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988600"
---
# <a name="add-a-tile-layer-to-a-map"></a>Csemperéteg hozzáadása térképhez

Ez a cikk bemutatja, hogyan fedheti át a mozaikréteget a térképen. A mozaikrétegek lehetővé teszik, hogy képeket helyezhet az Azure Maps alaptérképcsempéire. Az Azure Maps csempézési rendszeréről a [Szintek és a csemperács nagyítása című témakörben](zoom-levels-and-tile-grid.md)talál további információt.

A mozaikréteg kiszolgálóról töltődik be a csempékbe. Ezek a képek előre renderelhetők vagy dinamikusan renderelhetők. Az előre renderelt képek ugyanúgy tárolódnak, mint bármely más kép a kiszolgálón, olyan elnevezési konvenció használatával, amelyet a csemperéteg megért. A dinamikusan renderelt képek szolgáltatás segítségével valós időben töltik be a képeket. Az Azure Maps [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) osztály a következő három különböző csempeszolgáltatás-elnevezési konvenciót támogat: 

* X, Y, Nagyítás jelölés - X az oszlop, Y a mozaikrács ban lévő mozaik sorpozíciója, a Nagyítás jelölés pedig a nagyítási szint alapján.
* Négykulcsos jelölés – Az x, y és a nagyítási adatok egyesítése egyetlen karakterlánc-értékben. Ez a karakterlánc-érték egyetlen csempe egyedi azonosítójává válik.
* Határolókeret – Adjon meg egy képet a `{west},{south},{east},{north}`Határolókeret koordinátái formátumban: . Ezt a formátumot gyakran használják a [Web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> A [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) nagyszerű módja annak, hogy nagy adatkészleteket jelenítsen meg a térképen. Nem csak egy mozaikréteg generálható képből, a vektoros adatok mozaikrétegként is renderelhetők. Ha a vektoradatokat mozaikrétegként rendereli, a térképvezérlésnek csak azokat a csempéket kell betöltenie, amelyek fájlméretük kisebb, mint az általuk képviselt vektoradatoknak. Ezt a technikát gyakran használják több millió sornyi adat renderelésére a térképen.

A csempe rétegnek átadott csempe URL-címének http-nek vagy https-URL-nek kell lennie egy TileJSON-erőforráshoz vagy egy csempe URL-sablonjának, amely a következő paramétereket használja: 

* `{x}`- X helyzetben a csempe. Is `{y}` szüksége `{z}`van, és .
* `{y}`- Y helyzetben a csempe. Is `{x}` szüksége `{z}`van, és .
* `{z}`- Zoom szinten a csempe. Is `{x}` szüksége `{y}`van, és .
* `{quadkey}`- Csempe négykulcsos azonosító alapján a Bing Maps csempe rendszer elnevezési konvenció.
* `{bbox-epsg-3857}`- Az EPSG 3857 térbeli referenciarendszer formátumú `{west},{south},{east},{north}` határolókeret-karakterlánca.
* `{subdomain}`- Az altartomány értékek helyőrzője, `subdomain` ha meg van adva, a függvény hozzáadódik.

## <a name="add-a-tile-layer"></a>Mozaikréteg hozzáadása

 Ez a minta bemutatja, hogyan hozhat létre mozaikréteget, amely mozaikkészletre mutat. Ez a minta az x, y, zoom csempézési rendszert használja. A forrás a csempe réteg egy időjárási radar overlay az [Iowa Környezetvédelmi Mesonet az Iowa State University](https://mesonet.agron.iastate.edu/ogc/). A radaradatok megtekintésekor ideális esetben a felhasználók tisztán látnák a városok címkéit, ahogy navigálnak a térképen. Ez a viselkedés úgy valósítható meg, `labels` hogy a mozaikréteget a réteg alá szúrja be.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Az alábbiakban a fenti funkciók teljes futókód-mintája látható.

<br/>

<iframe height='500' scrolling='no' title='Mozaikréteg X, Y és Z használatával' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Tollcsempe réteget X, Y és Z használatával</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) segítségével a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="customize-a-tile-layer"></a>Mozaikréteg testreszabása

A csemperéteg-osztály nak számos formázási lehetősége van. Itt van egy szerszám -hoz megpróbál őket ki.

<br/>

<iframe height='700' scrolling='no' title='Mozaikréteg beállításai' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Tollmozaik réteg beállításaiaz</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) segítségével a <a href='https://codepen.io'>CodePen oldalon</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Mozaikréteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

További kódmintákat a térképekhez további kódmintákért tekintse meg:

> [!div class="nextstepaction"]
> [Rendszerképréteg hozzáadása](./map-add-image-layer.md)
