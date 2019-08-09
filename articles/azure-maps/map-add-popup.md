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
ms.openlocfilehash: caf661faf00d1d32664b7958a14a8719a37ab36e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882100"
---
# <a name="add-a-popup-to-the-map"></a>Felugró ablak hozzáadása a térképhez

Ez a cikk bemutatja, hogyan adhat hozzá egy előugró pontot egy térképen.

## <a name="understand-the-code"></a>A kód értelmezése

<a id="addAPopup"></a>

A következő kód egy szimbólum réteget használva hozzáadja a térképhez `description` egy pont funkciót, amely rendelkezik `name` a és a tulajdonsággal. Az [előugró osztály](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) egy példánya jön létre, de nem jelenik meg. Az egérmutató a szimbólum rétegbe kerül, és az előugró ablak megnyitására és bezárására szolgál, amikor az egérmutató a szimbólum jelölője fölé vagy lefelé mutat. Ha a jelölő szimbólum `position` látható, az előugró ablak tulajdonsága a jelölő pozíciójának megfelelően frissül, és a `content` beállítás frissül, és egy olyan `name` HTML-kódot tartalmaz, `description` amely betakarja az egérmutatót a pont funkció és tulajdonságai között. Ekkor megjelenik a felugró ablak a térképen a `open` függvény használatával.

<br/>

<iframe height='500' scrolling='no' title='Felugró ablak hozzáadása a Azure Maps használatával' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a következőt: <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Azure Maps</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Felugró ablak újrahasználata több ponttal

Ha nagy számú ponttal rendelkezik, és egyszerre csak egy előugró ablak jelenik meg, a legjobb módszer egy előugró ablak létrehozása és újbóli használata, mint az egyes pontokhoz tartozó előugró ablakok létrehozása. A felugró ablak újrafelhasználásával az alkalmazás által létrehozott DOM-elemek száma jelentősen csökkent, ami jobb teljesítményt nyújt. Az alábbi minta 3 pont funkciót hoz létre. Ha bármelyikre kattint, megjelenik egy előugró ablak, amely az adott pont funkciójának tartalmát jeleníti meg.

<br/>

<iframe height='500' scrolling='no' title='Felugró ablak újrahasználata több PIN-kód használatával' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) által <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>több PIN</a> -kód használatával.
</iframe>

## <a name="customizing-a-popup"></a>Felugró ablak testreszabása

Alapértelmezés szerint az előugró ablak fehér háttérrel, egy mutató nyilat tartalmaz az alján, valamint egy Bezárás gombot a jobb felső sarokban. A következő minta a háttérszínt fekete `fillColor` színnel változtatja meg a felugró ablak beállításával. A Bezárás gomb eltávolításához állítsa a `shoCloseButton` beállítást hamis értékre. A felugró ablak HTML-tartalma a felugró ablak széleitől számított 10 képpontot használ, és a szöveg fehér színűvé válik, így szépen jelenik meg a fekete háttéren.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Testreszabott előugró ablak" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollal <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>testreszabott előugró ablakokat</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Felugró események

Az előugró ablakok megnyithatók, lezárhatók és áthúzhatók. Az előugró osztály olyan eseményeket biztosít, amelyek segítségével a fejlesztők reagálni tudnak ezekre a műveletekre. Az alábbi példa kiemeli, hogy az előugró ablak megnyitásakor, zárásakor vagy húzásakor milyen események égetése következik be. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Felugró események" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll előugró <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>eseményeit</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
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