---
title: Térkép létrehozásához az Azure-leképezések |} Microsoft Docs
description: A Javascript-leképezés létrehozása
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
ms.openlocfilehash: 9a7c611860a6d32f82d6714d945c62e6c562f91f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="create-a-map"></a>Térkép létrehozásához

Ez a cikk bemutatja, hogyan térkép létrehozásához.  

## <a name="understand-the-code"></a>A kód ismertetése

Kétféleképpen, hogyan hozhat létre olyan térképet. A térkép a kamera beállítása a középpontjának megadásával és nagyítási szint, vagy állítsa be a kamera a térkép határainak pont és a határolókeret pont szerepel határolókeret délnyugaton megadásával.

<a id="setCameraOptions"></a>

### <a name="setting-the-camera"></a>A kamera beállítása

<iframe height='310' scrolling='no' title='CameraOptions keresztül térkép létrehozásához' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>keresztül CameraOptions térkép létrehozásához</a> által Azure LBS (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti, a kódot egy [térkép objektum](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest) segítségével létrehozott `new atlas.Map()`. Szolgáltatástérkép tulajdonságai, például a center és a nagyítási szint részét képező [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraoptions?view=azure-iot-typescript-latest). Térkép konstruktor vagy segítségével meghatározása CameraOptions [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera) függvény a térkép osztály.

<a id="setCameraBoundsOptions"></a>

### <a name="setting-the-camera-bounds"></a>A kamera határokon beállítása

<iframe height='310' scrolling='no' title='CameraBoundsOptions keresztül térkép létrehozásához' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>keresztül CameraBoundsOptions térkép létrehozásához</a> Azure leképezésekhez (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti, a kódot egy [térkép objektum](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest) keresztül összeállított `new atlas.Map()`. Szolgáltatástérkép tulajdonságai, például a határolókeret részét képező [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraboundsoptions?view=azure-iot-typescript-latest). CameraBoundsOptions definiálható keresztül [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) függvény a térkép osztály.

## <a name="try-out-the-code"></a>Próbálja ki a kódot 

Tekintse meg a fenti mintakód. A JavaScript-kód bal oldalon található JS lapon szerkesztheti, és a változtatások térkép megjelenítése az eredmények lapon kattintson a jobb. Kattintson a "CodePen a módosítása" gombra is, és a kód CodePen szerkesztése. 

<a id="relatedReference"></a>

## <a name="next-steps"></a>További lépések

További tudnivalók az osztályok és az ebben a cikkben használt módszerek: 
* [térkép](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)

