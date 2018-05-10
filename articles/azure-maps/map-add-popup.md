---
title: Adja hozzá az Azure-leképezések egy előugró ablak |} Microsoft Docs
description: Javascript-leképezés egy előugró ablak hozzáadása
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: 7425081597bfa9379594597277555ee30809c4e6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="add-a-popup-to-the-map"></a>A térkép egy előugró ablak hozzáadása

Ez a cikk bemutatja, hogyan egy előugró ablak felvétele a térképre.  

## <a name="understand-the-code"></a>A kód ismertetése

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Olyan térképet egy előugró ablak hozzáadása' src='//codepen.io/azuremaps/embed/zRyKxj/?height=545&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/zRyKxj/'>egy előugró ablak felvétele a térképre</a> Azure leképezésekhez (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódot az első kódblokkot egy térkép objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) utasításokat.

A második kódblokkot egy PIN-kódot hoz létre, és adja hozzá a térképen. Látható [PIN-kód hozzáadása a térkép](./map-add-pin.md) utasításokat.

A harmadik kódblokkot hoz létre egy helyi belül megjelenő tartalmat. Előugró HTML-elem tartalma. 

A negyedik kódblokkot létrehoz egy [előugró objektum](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) keresztül `new atlas.Popup()`. Előugró tulajdonságait, például tartalom megjelenítése és elhelyezése részét képező [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popupoptions?view=azure-iot-typescript-latest). Előugró konstruktor vagy segítségével meghatározása PopupOptions [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions) funkció a helyi menü osztály.

Az utolsó kódblokkot használ [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener) figyeljen gombállapotokat esemény a PIN-kódokat, és használja a térkép osztály függvény [nyissa meg a](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open) funkció a helyi menü osztály a helyi menü megnyitásához, ha az esemény akkor következik be.


## <a name="next-steps"></a>További lépések

További tudnivalók az osztályok és az ebben a cikkben használt módszerek: 

* [térkép](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener)
* [Helyi menü](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [Nyissa meg](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [Zárja be](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)
