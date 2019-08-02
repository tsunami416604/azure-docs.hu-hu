---
title: Felugró ablak hozzáadása a Azure Mapshoz | Microsoft Docs
description: Felugró ablak hozzáadása a JavaScript-térképhez
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 92d44ef3d0db8e93d4babd7441238c7fa105dbd5
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639002"
---
# <a name="add-a-popup-to-the-map"></a>Felugró ablak hozzáadása a térképhez

Ez a cikk bemutatja, hogyan adhat hozzá egy előugró pontot egy térképen.

## <a name="understand-the-code"></a>A kód értelmezése

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Felugró ablak hozzáadása a Azure Maps használatával' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a következőt: <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Azure Maps</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódban a kód első blokkja egy Térkép objektumot hoz létre. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat. Emellett az előugró ablakban megjelenő HTML-tartalmat is létrehoz.

A kód második blokkja egy adatforrás-objektumot hoz létre az [adatforrás osztály használatával](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . A pont a [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) osztály egyik [funkciója](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) . Ekkor létrejön egy név és egy leírás tulajdonsággal rendelkező pont objektum, amely hozzá lesz adva az adatforráshoz.

A [szimbólum-réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) szöveggel vagy ikonokkal jeleníti meg a pont-alapú adatforrásokat az adatforrásban szimbólumként a térképen. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)  A rendszer létrehoz egy szimbólum réteget a kód harmadik blokkjában. A rendszer hozzáadja az adatforrást a szimbólum réteghez, amelyet aztán hozzáad a térképhez.

A kód negyedik blokkja egy előugró [objektumot](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) hoz `new atlas.Popup()`létre a használatával. Az előugró tulajdonságok, például a pozíció és a pixelOffset a [PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions)részei. A PopupOptions megadható a felugró ablakban vagy az előugró osztály [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) funkciójával. Ekkor `mouseover` létrejön egy esemény-figyelő a szimbólum réteghez.

A kód utolsó blokkja létrehoz egy függvényt, amelyet az `mouseover` esemény-figyelő aktivál. Beállítja az előugró ablak tartalmát és tulajdonságait, és hozzáadja a felugró objektumot a térképhez.

## <a name="reusing-a-popup-with-multiple-points"></a>Felugró ablak újrahasználata több ponttal

Ha sok ponttal rendelkezik, és egyszerre csak egy előugró ablak jelenik meg, akkor a legjobb megoldás egy előugró ablak létrehozása és újbóli használata ahelyett, hogy előugró ablakokat hozzon létre minden egyes pont szolgáltatáshoz. Ezzel az alkalmazás által létrehozott DOM-elemek száma jelentősen csökkent, ami jobb teljesítményt biztosít. Ez a minta 3 pont funkciót hoz létre. Ha bármelyikre kattint, megjelenik egy előugró ablak, amely az adott pont funkciójának tartalmát jeleníti meg.

<br/>

<iframe height='500' scrolling='no' title='Felugró ablak újrahasználata több PIN-kód használatával' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) által <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>több PIN</a> -kód használatával.
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Lakosság](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

A következő nagyszerű cikkekből megtudhatja a kódok teljes mintáit:

> [!div class="nextstepaction"]
> [Szimbólum réteg hozzáadása](./map-add-pin.md)

> [!div class="nextstepaction"]
> [HTML-jelölő hozzáadása](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Alakzat hozzáadása](./map-add-shape.md)