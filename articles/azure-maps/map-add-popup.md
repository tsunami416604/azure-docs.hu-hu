---
title: Az Azure Maps előugró ablak hozzáadása |} A Microsoft Docs
description: Egy előugró ablak hozzáadása a Javascript-térkép
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0f86578e33e5c6a2d6528e2deb1c8068a0c94d01
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42747105"
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

* [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener)
* [Helyi menü](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [Nyissa meg a](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)
    * [Zárja be](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#close)
    
A maps hozzá további kódot példák a következő cikkekben talál: 
* [Alakzat hozzáadása](./map-add-shape.md)
* [Egyéni HTML hozzáadása](./map-add-custom-html.md)
