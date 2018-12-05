---
title: Az Azure Maps előugró ablak hozzáadása |} A Microsoft Docs
description: Egy előugró ablak hozzáadása a Javascript-térkép
author: jingjing-z
ms.author: jinzh
ms.date: 11/09/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: a6c8a8aa954379036ce566a205b8cb4e97952727
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52887838"
---
# <a name="add-a-popup-to-the-map"></a>A térkép egy előugró ablak hozzáadása

Ez a cikk bemutatja, hogyan egy előugró ablak hozzáadása egy pont a térképen.

## <a name="understand-the-code"></a>A kód értelmezése

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Az Azure Maps pop hozzáadása' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>pop összeadódhatnak az Azure Maps</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást. Azt is létrehoz HTML tartalom megjeleníteni kívánt helyi belül.

A második kódblokkot létrehoz egy objektum használhatja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály. A pont egy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) , a [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) osztály. Ezután létrehozott és az adatforráshoz hozzáadandó egy pont egy név és leírás tulajdonsággal rendelkező objektumot.

A [szimbólum réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) használja a szöveges vagy ikonjai csomagolni szoftverfrissítésipont-alapú adatok megjelenítése a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) szimbólumra a térképen.  A harmadik kódblokkot egy szimbólum réteg jön létre. Az adatforrás fel van véve a szimbólum réteghez, majd a térkép felvett.

A negyedik kódblokkot létrehoz egy [előugró objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) keresztül `new atlas.Popup()`. Előugró ablak tulajdonságok, mint például a pozíció és pixelOffset részét képező [PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions). Előugró ablak a konstruktorban, vagy keresztül határozhatók PopupOptions [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) függvény az előugró osztály. A `mouseover` majd jön létre a szimbólum réteg eseményfigyelő.

A legutóbbi kódblokkot által aktivált függvény létrehozása a `mouseover` eseményfigyelő. Azt állítja be a tartalmát és tulajdonságait – előugró, és hozzáadja az előugró objektum a térképen.

## <a name="reusing-a-popup-with-multiple-points"></a>Egy előugró ablak, több pontok újbóli használata

Sok pontok, és csak egy előugró ablak megjelenítése egyszerre szeretné, a legjobb módszer esetén hozzon létre egy előugró ablak, és újra felhasználhatja azt létrehozása helyett egy előugró ablak az egyes pont szolgáltatásokhoz. Ezáltal a DOM-elemek az alkalmazás által létrehozott száma jelentősen csökken, amely jobb teljesítményt tud nyújtani. Ez a példa 3 pont szolgáltatásokat hoz létre. Ha ezek közül bármelyik kattint, a tartalom az adott pont szolgáltatás egy előugró ablak jelenik meg.

<br/>

<iframe height='500' scrolling='no' title='Újbóli felhasználása több PIN-kód az előugró ablak' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>újbóli felhasználása több PIN-kód az előugró</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [Helyi menü](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

Tekintse meg az alábbi nagyszerű teljes körű Kódminták cikkeket:

> [!div class="nextstepaction"]
> [Egy szimbólum réteg hozzáadása](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Egy HTML-mutató hozzáadása](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Alakzat hozzáadása](./map-add-shape.md)