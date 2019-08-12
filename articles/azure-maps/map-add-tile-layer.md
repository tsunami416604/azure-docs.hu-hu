---
title: Csempe réteg hozzáadása a Azure Mapshoz | Microsoft Docs
description: Csempe réteg hozzáadása a JavaScript-térképhez
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: d872cd78b3fd04512fcaee706e54bffa1cf9fcc1
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882091"
---
# <a name="add-a-tile-layer-to-a-map"></a>Csempe rétegének hozzáadása térképhez

Ez a cikk bemutatja, hogyan helyezhet át egy csempe réteget a térképen. A csempe rétegek lehetővé teszik, hogy az alapszintű Térkép csempék fölé írja a képeket Azure Maps. Azure Maps csempe rendszerével kapcsolatos további információkért tekintse meg a [nagyítási szintek és](zoom-levels-and-tile-grid.md) a csempék rácsának dokumentációját.

Egy csempe réteg tölti be a csempéket egy kiszolgálóról. Ezeket a lemezképeket előre megjelenítheti és tárolhatja, mint bármely más rendszerkép egy kiszolgálón a csempe rétege által értelmezett elnevezési konvencióval vagy olyan dinamikus szolgáltatással, amely menet közben hozza létre a képeket. Az Azure Maps [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) osztály három különböző csempe-szolgáltatási elnevezési konvenciót támogat: 

* X, Y, nagyítási jelölés – a nagyítási szint alapján az x az oszlop, az Y pedig a csempén lévő csempe sor pozíciója.
* Quadkey jelölés – x, y és nagyítási információ egyetlen karakterlánc-értékre, amely egy csempe egyedi azonosítója.
* A határolókerethoz tartozó határoló mező koordinátáit a [webes leképezési szolgáltatások (WMS)](https://www.opengeospatial.org/standards/wms)által gyakran `{west},{south},{east},{north}` használt formátumú rendszerképek megadására lehet használni.

> [!TIP]
> A [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) nagyszerű lehetőséget mutat a nagyméretű adathalmazok megjelenítésére a térképen. Nem csak a csempe réteg hozható létre egy képből, de a vektoros adatok csempe rétegként is megjeleníthető. A vektoros adattároló rétegként való megjelenítésével a Térkép vezérlőelemnek csak be kell töltenie a csempéket, ami sokkal kisebb lehet a fájlméretnél, mint az általuk képviselt adatmennyiség. Ezt a technikát sokan használják, akiknek több millió sornyi adatsort kell megjeleníteniük a térképen.

A csempe rétegbe átadott csempe URL-címének HTTP/HTTPS URL-címnek kell lennie egy TileJSON-erőforráshoz vagy egy csempe URL-sablonhoz, amely a következő paramétereket használja: 

* `{x}`-A csempe X pozíciója A és `{y}` a `{z}`is szükséges.
* `{y}`-A csempe Y pozíciója A és `{x}` a `{z}`is szükséges.
* `{z}`– A csempe nagyítási szintje A és `{x}` a `{y}`is szükséges.
* `{quadkey}`– Csempe quadkey-azonosítója a Bing Maps csempe rendszerelnevezési konvenciója alapján.
* `{bbox-epsg-3857}`– Egy határolókeret karakterlánca, amely a EPSG `{west},{south},{east},{north}` 3857 térbeli hivatkozási rendszer formátumát jelöli.
* `{subdomain}`-A megadott altartomány-értékeket tartalmazó helyőrzőt adja hozzá a rendszer.

## <a name="add-a-tile-layer"></a>Mozaikréteg hozzáadása

 Ez a minta bemutatja, hogyan hozhat létre egy csempe réteget, amely az x, y, zoom csempe rendszerű csempe-készletre mutat. Ennek a csempe rétegnek a forrása az [Iowa Állami Egyetem Iowa környezeti Mesonet](https://mesonet.agron.iastate.edu/ogc/)származó időjárási radar. 

<br/>

<iframe height='500' scrolling='no' title='Réteg csempe X, Y és Z használatával' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>csempe réteget X, Y és Z használatával</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) értékkel a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódban a kód első blokkja egy Térkép objektumot hoz létre. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A második blokkban a [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) egy formázott URL-cím átadása egy csempe-szolgáltatásba, a csempe méretére, valamint egy opacitásra, amely félig áttetszővé teszi. Emellett, amikor hozzáadja a csempe réteget a térképhez, a `labels` réteg alá kerül, hogy a feliratok továbbra is jól láthatóak legyenek.

## <a name="customize-a-tile-layer"></a>Csempe rétegének testreszabása

A csempe réteg osztályának számos stílusa van. Itt látható egy eszköz, amellyel kipróbálhatja őket.

<br/>

<iframe height='700' scrolling='no' title='Csempe rétegének beállításai' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>csempe rétegének beállításait</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Képréteg hozzáadása](./map-add-image-layer.md)
