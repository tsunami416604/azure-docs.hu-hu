---
title: Egy csempe réteg hozzáadása az Azure Maps |} A Microsoft Docs
description: A Javascript-térkép réteg csempe hozzáadása
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 57cde41bf65c370f86ebc57a39e917b95721ca81
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892308"
---
# <a name="add-a-tile-layer-to-a-map"></a>Térkép egy mozaikréteg hozzáadása

Ez a cikk bemutatja, hogyan lehet keverni egy mozaikréteg a térképen. Csempe rétegek szélt lemezképek felül az Azure Maps alaptérképet csempék teszik lehetővé. További információ az Azure Maps mozaik rendszeren megtalálható a [nagyítási szintek és csemperács](zoom-levels-and-tile-grid.md) dokumentációját.

Egy csempe réteg betöltése a csempék a kiszolgálóról. Ezek a lemezképek is vagy előre megjelenített és tárolt, mint bármely egyéb olyan kép egy kiszolgálón egy elnevezési konvenciója, a csempe réteg tisztában van azzal, vagy egy dinamikus szolgáltatás, amely létrehozza a lemezképeket menet közben. Nincsenek a három különböző csempe támogatja az Azure Maps szolgáltatás elnevezési konvenciók [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) osztály; 

* X, Y, nagyítás jelöléssel - nagyítási szint alapján, x az oszlop, y pedig sor pozícióját a csempe a csempe rácsban.
* Quadkey jelöléssel - kombináció x, y, nagyítás információkból egy egyetlen karakterláncérték, amely egy csempéhez egyedi azonosítója.
* Határolókeret – határolókeret koordinátái használható a következő formátumban adja meg a kép `{west},{south},{east},{north}` által gyakran használt [webes leképezési szolgáltatások (WMS)](http://www.opengeospatial.org/standards/wms).

> [!TIP]
> A [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) kiválóan alkalmas nagy méretű adatkészleteket a térképen megjelenítése. Nem csak egy mozaikréteg létrehozható egy rendszerképből, de vektoros adatait is megjeleníthetők-e egy csempe rétegként túl. A térkép vezérlőelem szerint egy csempe rétegként vektoros adatait renderelése, csak kell betöltenie a csempéket, amelynek sokkal kisebb lehet a fájlméret, mint a vektoros adatait képviselik. Számos jelennek meg a térképen adatsorokat millons kell ezt a módszert használja.

A csempe URL-cím egy mozaikréteg átadott kell lennie egy http/https URL-CÍMÉT egy TileJSON erőforrás vagy egy csempe URL-cím sablon, amely a következő paramétereket használja: 

* `{x}` -X pozíció a csempe. Azt is el kell `{y}` és `{z}`.
* `{y}` -A csempe Y pozíciója. Azt is el kell `{x}` és `{z}`.
* `{z}` -A csempe nagyítási szintjét. Azt is el kell `{x}` és `{y}`.
* `{quadkey}` – Csempe quadkey azonosítója alapján a csempe a Bing Maps elnevezési konvenciót.
* `{bbox-epsg-3857}` -A határoló box karakterlánc a következő formátumban `{west},{south},{east},{north}` a rendszerben EPSG 3857 térbeli hivatkozást.
* `{subdomain}` -A helyőrző, ahova a rendszer hozzáadja az altartomány értékeket, ha meg van adva.

## <a name="add-a-tile-layer"></a>Egy mozaikréteg hozzáadása

 Ez a minta bemutatja, hogyan hozzon létre egy csempe réteget, amely az csempék jelennek meg az x, y, nagyítás mozaik rendszert használó mutat. Ez a csempe réteg forrását egy időjárási ilyesmire területre a a [Iowa környezeti Mesonet, Iowa State Universityn](http://mesonet.agron.iastate.edu/ogc/).

<br/>

<iframe height='500' scrolling='no' title='Csempe réteg használatával X, Y és Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Mozaikréteg használatával X, Y és Z</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, az egy [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) létrejön egy csempe szolgáltatás, a csempe méretéhez és a egy átlátszatlanság legyen a félig átlátszók egy formázott URL-címet adja át. Ezenkívül amikor az mozaikréteg hozzáadása a térképen, megjelenik alább a `labels` , hogy a címkékhez továbbra is a fókuszjelzőknek jól láthatóknak réteg.

## <a name="customize-a-tile-layer"></a>Egy csempe réteg testreszabása

A csempe réteg csak egy stílusának számos lehetőség van. Itt egy olyan eszköz, próbálja ki őket.

<br/>

<iframe height='700' scrolling='no' title='Mozaikréteg beállításainak' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>réteg Mozaikbeállítások</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayeroptions?view=azure-iot-typescript-latest)

Tekintse meg a további Kódminták a maps hozzá az alábbi cikkeket:

> [!div class="nextstepaction"]
> [A kép réteg hozzáadása](./map-add-image-layer.md)