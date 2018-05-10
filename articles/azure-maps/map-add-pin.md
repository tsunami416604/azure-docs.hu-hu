---
title: Adja hozzá az Azure-leképezések PIN-kód |} Microsoft Docs
description: A PIN-kód hozzáadása a Javascript-leképezés
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
ms.openlocfilehash: 189b30ff0b5c47d0a4bd3181c8eb2143213ad6cd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="add-pins-to-the-map"></a>PIN-kód hozzáadása a térkép

Ez a cikk bemutatja, hogyan PIN-kód felvétele a térképre.  

## <a name="understand-the-code"></a>A kód ismertetése

<iframe height='500' scrolling='no' title='A PIN-kód felvétele a térképre' src='//codepen.io/azuremaps/embed/ZrVpEa/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/ZrVpEa/'>PIN-kód felvétele a térképre</a> Azure leképezésekhez (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódot az első kódblokkot egy térkép objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) utasításokat.

A második kódblokkot, a PIN-kód létrehozza és hozzáadja a leképezés. A PIN kód egy [szolgáltatás](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) a [pont](https://docs.microsoft.com/javascript/api/azure-maps-javascript/point?view=azure-iot-typescript-latest) rendelkező [PinProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/pinproperties?view=azure-iot-typescript-latest) , a szolgáltatás tulajdonsága. Használjon `new atlas.data.Feature(new atlas.data.Point())` PIN-kód létrehozásához, és adja meg a tulajdonságait. A PIN-kód réteg: a PIN-kódok bájttömb. Használjon [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins) függvény a térkép osztály egy PIN-kód réteg hozzáadása a térkép, és a PIN-kód réteg tulajdonságainak definiálásához. A tulajdonságok a PIN-kód réteg [PinLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/pinlayeroptions?view=azure-iot-typescript-latest). 

## <a name="next-steps"></a>További lépések

További tudnivalók az osztályok és az ebben a cikkben használt módszerek: 
* [térkép](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
