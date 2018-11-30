---
title: Térkép létrehozása az Azure Maps |} A Microsoft Docs
description: A Javascript-térkép létrehozása
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 97b94cf54454a83510c5be2cf0d71281dbf5b004
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424239"
---
# <a name="create-a-map"></a>Térkép létrehozása

Ez a cikk bemutatja a térkép létrehozásához és a egy térkép animálása módszereket.  

## <a name="understand-the-code"></a>A kód értelmezése

Kétféleképpen összeállíthatja a térképen. A térkép a kamerát a középpontjára megadásával állíthatja, és nagyítási szintjét, vagy a térkép kamera meze állíthatja a Délnyugati régió alkalmazottai megadásával, és északkelet határoló pontokat.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Állítsa be a kamerához

<iframe height='500' scrolling='no' title='Keresztül CameraOptions térkép létrehozása' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>keresztül térkép létrehozásához `CameraOptions` </a>által az Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód egy [térkép objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) lett létrehozva `new atlas.Map()` és a középpont és nagyítás vannak beállítva. Térkép tulajdonságai középpont és nagyítás szint például részét képező [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>A kamera határokon beállítása

<iframe height='500' scrolling='no' title='Keresztül CameraBoundsOptions térkép létrehozása' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>keresztül térkép létrehozásához `CameraBoundsOptions` </a>által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód egy [térkép objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) használatával összeállított `new atlas.Map()`. Térkép tulajdonságai például `CameraBoundsOptions` keresztül lehet definiálni [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) függvény a térkép osztály. Korlátok és kitöltési tulajdonságok vannak beállítva, használatával `setCamera`.

### <a name="animate-map-view"></a>Térképnézet animálása

<iframe height='500' scrolling='no' title='Térképnézet animálása' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Térképnézet animálása</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot hoz létre a fenti kód egy [térkép objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) keresztül `new atlas.Map()`. Térkép tulajdonságai középpont és nagyítás szint például részét képező [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions). `CameraOptions` a térkép a konstruktorban, vagy keresztül határozhatók [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) függvény a térkép osztály. A [stílus leképezése](https://docs.microsoft.com/azure/azure-maps/supported-map-styles) értékre van állítva `road`.

A második kódblokk létrehoz egy térkép egyenkénti animálását függvény, amely térképnézet változása animálja definiálásával [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions) keresztül [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) függvény. A függvény akkor aktiválódik, a térkép animálása gomb létrehozásához minden kattintás után egy véletlenszerű nagyítási szint szerint.

## <a name="try-out-the-code"></a>Próbálja ki a kódot

Vessen egy pillantást a fenti mintakód. Szerkesztheti a JavaScript-kódot a a **JS lapon** a bal oldalon, megváltozik a térképnézet lásd a **eredménye lapot** a jobb oldalon. A is kattinthat a **Szerkesztés a CodePen** gombra, és a CodePen kód szerkesztése.

<a id="relatedReference"></a>

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Funkciók hozzáadása az alkalmazáshoz való kód példa látható:

> [!div class="nextstepaction"]
> [Térkép stílus kiválasztása](choose-map-style.md)

> [!div class="nextstepaction"]
> [Térkép vezérlők hozzáadása](map-add-controls.md)
