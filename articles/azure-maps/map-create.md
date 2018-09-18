---
title: Térkép létrehozása az Azure Maps |} A Microsoft Docs
description: A Javascript-térkép létrehozása
author: jingjing-z
ms.author: jinzh
ms.date: 09/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9759c4149c6b026837e550dcf3ab0a0156bbb736
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730009"
---
# <a name="create-a-map"></a>Térkép létrehozása

Ez a cikk bemutatja, hogyan hozhat létre egy térképet.  

## <a name="understand-the-code"></a>A kód értelmezése

Kétféleképpen összeállíthatja a térképen. Állítsa be a térkép a kamerát a középpontjára megadásával és a nagyítás szintjének. Állítsa be a térkép a kamera meze a Délnyugati régió alkalmazottai pont határoló és északkelet határoló pont megadása.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Állítsa be a kamerához

<iframe height='310' scrolling='no' title='Keresztül CameraOptions térkép létrehozása' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>keresztül térkép létrehozásához `CameraOptions` </a>által az Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód egy [térkép objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) lett létrehozva `new atlas.Map()`. Térkép tulajdonságai középpont és nagyítás szint például részét képező [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraoptions?view=azure-iot-typescript-latest). `CameraOptions` a térkép a konstruktorban, vagy keresztül határozhatók [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) függvény a térkép osztály.

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>A kamera határokon beállítása

<iframe height='310' scrolling='no' title='Keresztül CameraBoundsOptions térkép létrehozása' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>keresztül térkép létrehozásához `CameraBoundsOptions` </a>által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód egy [térkép objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) használatával összeállított `new atlas.Map()`. Térkép tulajdonságai, például a határolókeret részét képező [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest). `CameraBoundsOptions` keresztül lehet definiálni [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) függvény a térkép osztály.

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
