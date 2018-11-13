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
ms.openlocfilehash: 0be10c155398133887fadb1fe9954068f3afb9d9
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568114"
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

A negyedik kódblokkot létrehoz egy [előugró objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) keresztül `new atlas.Popup()`. Előugró ablak tulajdonságok, mint például a pozíció és pixelOffset részét képező [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.popupoptions?view=azure-iot-typescript-latest). Előugró ablak a konstruktorban, vagy keresztül határozhatók PopupOptions [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) függvény az előugró osztály. A `mouseover` majd jön létre a szimbólum réteg eseményfigyelő.

A legutóbbi kódblokkot által aktivált függvény létrehozása a `mouseover` eseményfigyelő. Azt állítja be a tartalmát és tulajdonságait – előugró, és hozzáadja az előugró objektum a térképen.

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Helyi menü](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Tekintse meg az alábbi nagyszerű teljes körű Kódminták cikkeket:

> [!div class="nextstepaction"]
> [Alakzat hozzáadása](./map-add-shape.md)

> [!div class="nextstepaction"]
> [Egyéni HTML hozzáadása](./map-add-custom-html.md)
