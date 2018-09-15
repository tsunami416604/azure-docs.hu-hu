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
ms.openlocfilehash: 0e292bfd38402d5a36be217746fd352ce4627177
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45633316"
---
# <a name="create-a-map"></a>Térkép létrehozása

Ez a cikk bemutatja, hogyan hozhat létre egy térképet.  

## <a name="understand-the-code"></a>A kód értelmezése

Kétféleképpen összeállíthatja a térképen. Állítsa be a térkép a kamerát a középpontjára megadásával és a nagyítási szint, vagy állítsa be a kamera határán kívül a térkép a Délnyugati régió alkalmazottai pont és a pont határoló északkelet határoló megadásával.

<a id="setCameraOptions"></a>

### <a name="setting-the-camera"></a>A kamera beállítása

<iframe height='310' scrolling='no' title='Keresztül CameraOptions térkép létrehozása' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>keresztül CameraOptions térkép létrehozásához</a> által az Azure LBS (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód egy [térkép objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) lett létrehozva `new atlas.Map()`. Térkép tulajdonságai középpont és nagyítás szint például részét képező [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraoptions?view=azure-iot-typescript-latest). CameraOptions definiálhatók, térkép a konstruktorban, vagy keresztül [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) függvény a térkép osztály.

<a id="setCameraBoundsOptions"></a>

### <a name="setting-the-camera-bounds"></a>A kamera határokon beállítása

<iframe height='310' scrolling='no' title='Keresztül CameraBoundsOptions térkép létrehozása' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>keresztül CameraBoundsOptions térkép létrehozásához</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód egy [térkép objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) használatával összeállított `new atlas.Map()`. Térkép tulajdonságai, például a határolókeret részét képező [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest). CameraBoundsOptions keresztül lehet definiálni [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) függvény a térkép osztály.

## <a name="try-out-the-code"></a>Próbálja ki a kódot

Vessen egy pillantást a fenti mintakód. Szerkesztheti a JavaScript-kódot, a bal oldali JS lapján, és tekintse meg a változások térképen megtekintése az eredmény lapon a jobb oldalon. Kattintson a "CodePen a Szerkesztés" gombra is, és a CodePen kód szerkesztése.

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
