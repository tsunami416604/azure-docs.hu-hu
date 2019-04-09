---
title: A szimbólum réteg hozzáadása az Azure Maps |} A Microsoft Docs
description: A Javascript-térkép szimbólumok hozzáadása
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 2580f1177bf9e6e3a92934f88a5d8ab51894e8d9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269486"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Vegyen fel egy szimbólum réteget a térkép

Ez a cikk bemutatja, hogyan renderelni a pont adatokat egy adatforrásból egy szimbólum rétegként a térképen. Szimbólum rétegek vannak leképezve, vagy a Web GL használatával és támogatja a HTML jelölők, mint a pontok sokkal nagyobb csoportokat, de nem támogatja a hagyományos CSS- és HTML-elemek stílusának számára.  

> [!TIP]
> Symbol rétegek alapértelmezés szerint egy adatforrásban lévő összes geometriája koordinátáit jelenik meg. A réteg korlátozása úgy, hogy a vártak csak pont geometriai szolgáltatások beállítása az `filter` tulajdonság a réteg `['==', ['geometry-type'], 'Point']` vagy `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` Ha fel szeretne venni, valamint a MultiPoint szolgáltatások.

## <a name="add-a-symbol-layer"></a>Szimbólumréteg hozzáadása

<iframe height='500' scrolling='no' title='Kapcsoló PIN-kód helyét' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>kapcsoló PIN-kód helyét</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, egy adatforrás-objektum létrejött, használja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály. [A szolgáltatás], amely tartalmazza a [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) geometriai van burkolt be, a [alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) osztály könnyebb frissíteni, majd létrehozott és az adatforrás hozzá.

A harmadik kódblokkot létrehoz egy [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) és a frissítések, egér, a pont koordináták kattintson az alakzat osztállyal [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) metódust.

A [szimbólum réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) használja a szöveges vagy ikonjai csomagolni szoftverfrissítésipont-alapú adatok megjelenítése a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) szimbólumra a térképen.  Az adatforrás az kattintson eseményfigyelő és a szimbólum réteg létrehozása és belül a térképhez hozzáadni a `ready` [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) biztosítására a pont után betöltve, és készen áll a érhető el a térkép jelenik meg.

> [!TIP]
> Alapértelmezés szerint a teljesítmény szimbólum rétegek optimalizálása a renderelési szimbólumok a szimbólumok, amely átfedésben. Ahogy a rejtett szimbólumok nagyítás láthatóvá válhat. Tiltsa le ezt a szolgáltatást, és minden szimbólumok leképezéséhez mindenkor, állítsa be a `allowOverlap` tulajdonságát a `iconOptions` megadhatja, hogy `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Egy szimbólum réteget ad hozzá egy egyéni ikon

Symbol rétegek használatával vagy a Web GL vannak leképezve. Az ilyen összes erőforrások, például képek ikonra kell lennie tölti be a vagy a Web GL környezetet. Ez a minta bemutatja a térkép erőforrásokat ad hozzá egy egyéni ikon és, amellyel jelennek meg a térképen egy egyéni szimbólummal pont adatait. A `textField` a szimbólum réteg tulajdonság szükséges egy kifejezést lehet megadni. Ebben az esetben szeretnénk jelennek meg a hőmérséklet-tulajdonság, de mivel ez egy számot, alakítandó karakterláncot kell. Emellett szeretnénk a "° F" hozzáfűzése. Egy kifejezés használható ehhez; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Egyéni szimbólum lemezkép ikon' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>egyéni szimbólum lemezkép ikon</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-symbol-layer"></a>A szimbólum réteg testreszabása 

A szimbólum réteg rendelkezésre álló számos stílusának lehetőséggel rendelkezik. Itt egy olyan eszköz teszteléséhez ezek különböző stílusának lehetőségeit.

<br/>

<iframe height='700' scrolling='no' title='Symbol réteg beállításai' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/PxVXje/'>réteg beállításai</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TexTOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Tekintse meg a további Kódminták a maps hozzá az alábbi cikkeket:

> [!div class="nextstepaction"]
> [Előugró ablak hozzáadása](map-add-popup.md)

> [!div class="nextstepaction"]
> [Az adatvezérelt stílus kifejezések használata](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Alakzat hozzáadása](map-add-shape.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adja hozzá a HTML-számára](map-add-bubble-layer.md)
