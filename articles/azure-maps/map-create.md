---
title: Térkép létrehozása Azure Mapssal | Microsoft Docs
description: JavaScript-Térkép létrehozása
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8a4f67290e93d8b296added9023fe9b6947ba02c
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638684"
---
# <a name="create-a-map"></a>Térkép létrehozása

Ebből a cikkből megtudhatja, hogyan hozhat létre térképet és animálhat egy térképet.  

## <a name="understand-the-code"></a>A kód értelmezése

Kétféleképpen hozhat létre térképet. A Térkép kameráját beállíthatja a középpont és a nagyítás szintjének megadásával, vagy megadhatja a Térkép kamera-határait a délnyugati és északkeleti határoló pontok megadásával.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>A kamera beállítása

<iframe height='500' scrolling='no' title='Térkép létrehozása a CameraOptions használatával' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io'>CodePen</a>-on<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>keresztül `CameraOptions` </a>Azure Location based Services () használatával.
</iframe>

A fenti kódban a [Térkép objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) a-on keresztül `new atlas.Map()` jön létre, a középpont és a nagyítás pedig be van állítva. A Térkép tulajdonságai, például a középpont és a nagyítási szint a [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)részei.

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>A kamera határainak beállítása

<iframe height='500' scrolling='no' title='Térkép létrehozása a CameraBoundsOptions használatával' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io'>CodePen</a>-on<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>keresztül `CameraBoundsOptions` </a>Azure Maps () használatával.
</iframe>

A fenti kódban a [Térkép objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) a-on keresztül `new atlas.Map()`épül fel. Térképi tulajdonságok, `CameraBoundsOptions` például a Térkép osztály [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) függvényében definiálhatók. A határértékek és a kitöltés tulajdonságai a használatával `setCamera`állíthatók be.

### <a name="animate-map-view"></a>Animálási Térkép nézet

<iframe height='500' scrolling='no' title='Animálási Térkép nézet' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a toll <a href='https://codepen.io/azuremaps/pen/WayvbO/'>animálása Térkép nézetet</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódban az első kód blokk egy [Térkép objektumot](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) hoz létre a használatával `new atlas.Map()`. A Térkép tulajdonságai, például a középpont és a nagyítási szint a [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)részei. `CameraOptions`a Map konstruktorban vagy a Map osztály [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) függvényében adható meg. A [Térkép stílusa](https://docs.microsoft.com/azure/azure-maps/supported-map-styles) a következőre `road`van beállítva:.

A második kódrészlet egy animálási leképezési függvényt hoz létre, amely a [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions) a [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) függvénnyel való definiálásával animál a Térkép nézetben. A függvényt a "animálási Térkép" gomb aktiválja, hogy minden kattintás után véletlenszerű nagyítási szintet állítson elő.

## <a name="try-out-the-code"></a>A kód kipróbálása

Vessen egy pillantást a fenti mintakód. A JavaScript-kódot a bal oldalon található **js lapon** szerkesztheti, és a jobb oldalon látható **eredmények lapon** láthatja a Térkép nézet módosításait. Kattintson a **Szerkesztés CodePen** gombra, és szerkessze a kódot a CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Az alkalmazás funkcióinak hozzáadásához tekintse meg a kód példáit:

> [!div class="nextstepaction"]
> [Válasszon egy leképezési stílust](choose-map-style.md)

> [!div class="nextstepaction"]
> [Leképezési vezérlők hozzáadása](map-add-controls.md)
