---
title: Az Azure Maps előugró ablak hozzáadása |} A Microsoft Docs
description: Egy előugró ablak hozzáadása a Javascript-térkép
author: jingjing-z
ms.author: jinzh
ms.date: 09/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 76a7e230491d5e524a1d73437a56d12594cfebe2
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127437"
---
# <a name="add-a-popup-to-the-map"></a>A térkép egy előugró ablak hozzáadása

Ez a cikk bemutatja, hogyan egy előugró ablak hozzáadása egy térképen.  

## <a name="understand-the-code"></a>A kód értelmezése

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Egy előugró ablak hozzáadása egy térkép' src='//codepen.io/azuremaps/embed/zRyKxj/?height=545&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/zRyKxj/'>egy előugró ablak hozzáadása egy térkép</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot egy PIN-kódot hoz létre, és adja hozzá a térképen. Látható [PIN-kód hozzáadása a térkép](./map-add-pin.md) útmutatást.

A harmadik kódblokkot egy előugró ablak jelenik meg a tartalom hoz létre. Helyi menü tartalma HTML-elemmel.

A negyedik kódblokkot létrehoz egy [előugró objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) keresztül `new atlas.Popup()`. Előugró ablak tulajdonságok, mint például a tartalmát és pozícióját részét képező [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.popupoptions?view=azure-iot-typescript-latest). Előugró ablak a konstruktorban, vagy keresztül határozhatók PopupOptions [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) függvény az előugró osztály.

A legutóbbi kódblokkot használ [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener) funkcióval figyelni egy rámutatásra esemény a PIN-kódokat, és használja a térkép osztály [nyissa meg a](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) funkciót a helyi menü megnyitásához, az esemény akkor fordul elő, ha az előugró osztály.

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
