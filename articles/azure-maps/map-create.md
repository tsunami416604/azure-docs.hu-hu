---
title: Térkép létrehozása az Azure Maps |} A Microsoft Docs
description: A Javascript-térkép létrehozása
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b86f29e4d3faa1382ac3a79ed828855a5d9f6d7f
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42747219"
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
* [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds)
    
A maps hozzá további kódot példák a következő cikkekben talál: 
* [A PIN-kód hozzáadása](./map-add-pin.md)
* [Egy előugró ablak hozzáadása](map-add-popup.md)
    

