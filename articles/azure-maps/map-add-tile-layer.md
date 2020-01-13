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
ms.openlocfilehash: 83e8f6d684d6d39102fd682653cd19816a9f7b10
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911096"
---
# <a name="add-a-tile-layer-to-a-map"></a>Csemperéteg hozzáadása térképhez

Ez a cikk bemutatja, hogyan helyezhet át egy csempe réteget a térképen. A csempe rétegek lehetővé teszik, hogy az alapszintű Térkép csempék fölé írja a képeket Azure Maps. Azure Maps csempe rendszerével kapcsolatos további információkért tekintse meg a [nagyítási szintek és a csempék rácsának](zoom-levels-and-tile-grid.md) dokumentációját.

Egy csempe réteg tölti be a csempéket egy kiszolgálóról. Ezeket a lemezképeket előre megjelenítheti és tárolhatja, mint bármely más rendszerkép egy kiszolgálón a csempe rétege által értelmezett elnevezési konvencióval vagy olyan dinamikus szolgáltatással, amely menet közben hozza létre a képeket. Az Azure Maps [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) osztály három különböző csempe-szolgáltatási elnevezési konvenciót támogat: 

* X, Y, nagyítási jelölés – a nagyítási szint alapján az x az oszlop, az Y pedig a csempén lévő csempe sor pozíciója.
* Quadkey jelölés – x, y és nagyítási információ egyetlen karakterlánc-értékre, amely egy csempe egyedi azonosítója.
* A határoló mezőhöz kötött koordináták a következő formátumban adhatók meg: `{west},{south},{east},{north}` amelyet általában a [web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms)használ.

> [!TIP]
> A [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) nagyszerű lehetőséget mutat a nagyméretű adathalmazok megjelenítésére a térképen. Nem csak a csempe réteg hozható létre egy képből, de a vektoros adatok csempe rétegként is megjeleníthető. A vektoros adattároló rétegként való megjelenítésével a Térkép vezérlőelemnek csak be kell töltenie a csempéket, ami sokkal kisebb lehet a fájlméretnél, mint az általuk képviselt adatmennyiség. Ezt a technikát sokan használják, akiknek több millió sornyi adatsort kell megjeleníteniük a térképen.

A csempe rétegbe átadott csempe URL-címének HTTP/HTTPS URL-címnek kell lennie egy TileJSON-erőforráshoz vagy egy csempe URL-sablonhoz, amely a következő paramétereket használja: 

* a csempe `{x}`-X pozíciója `{y}` és `{z}`is szükséges.
* a csempe `{y}` – Y pozíciója `{x}` és `{z}`is szükséges.
* `{z}` – a csempe nagyítási szintje. `{x}` és `{y}`is szükséges.
* `{quadkey}` csempe quadkey azonosítóját a Bing Maps csempe rendszer-elnevezési konvenciója alapján.
* `{bbox-epsg-3857}` – egy, a EPSG 3857 térbeli hivatkozási rendszerében `{west},{south},{east},{north}` formátumú határolókeret-karakterlánc.
* `{subdomain}` – itt adható meg az altartomány értékeit tartalmazó helyőrző.

## <a name="add-a-tile-layer"></a>Mozaikréteg hozzáadása

 Ez a minta bemutatja, hogyan hozhat létre egy csempe réteget, amely az x, y, zoom csempe rendszerű csempe-készletre mutat. Ennek a csempe rétegnek a forrása az [Iowa Állami Egyetem Iowa környezeti Mesonet](https://mesonet.agron.iastate.edu/ogc/)származó időjárási radar. A radar-információk ideális módon való megtekintésekor a felhasználók tisztán láthatják a városok feliratait, ahogy a térképen navigálnak, ami az `labels` réteg alatti csempe réteg beszúrásával hajtható végre.

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

<iframe height='500' scrolling='no' title='Réteg csempe X, Y és Z használatával' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>csempe réteget X, Y és Z használatával</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) értékkel a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Csempe rétegének testreszabása

A csempe réteg osztályának számos stílusa van. Itt látható egy eszköz, amellyel kipróbálhatja őket.

<br/>

<iframe height='700' scrolling='no' title='Csempe rétegének beállításai' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>csempe rétegének beállításait</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Képréteg hozzáadása](./map-add-image-layer.md)
