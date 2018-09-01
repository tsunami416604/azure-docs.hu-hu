---
title: Adjon hozzá egy PIN-kód használatát az Azure Maps |} A Microsoft Docs
description: A PIN-kód hozzáadása egy Javascript-térkép
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0dafb09e1704e8e446b034975f0c25a740050599
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382584"
---
# <a name="add-pins-to-the-map"></a>PIN-kódok a térkép hozzáadása

Ez a cikk bemutatja, hogyan PIN-kód hozzáadása a térképen.  

## <a name="understand-the-code"></a>A kód értelmezése

<iframe height='500' scrolling='no' title='A PIN-kód hozzáadása egy térkép' src='//codepen.io/azuremaps/embed/ZrVpEa/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ZrVpEa/'>PIN-kód hozzáadása egy térkép</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, a PIN-kód létrehozása és a térképhez hozzáadni. PIN-kódra van egy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) , [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) a [PinProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/models.pinproperties?view=azure-iot-typescript-latest) , a szolgáltatás tulajdonsága. Használat `new atlas.data.Feature(new atlas.data.Point())` PIN-kód létrehozását, és adja meg a tulajdonságait. A PIN-kód réteg PIN-kódok tömbjét. Használat [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) függvény a térkép osztály egy PIN-kód réteget a térkép hozzáadása és a PIN-kód réteg tulajdonságainak definiálásához. Tekintse meg a PIN-kód réteg tulajdonságainak [PinLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.pinlayeroptions?view=azure-iot-typescript-latest). 

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja: 
* [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
A maps hozzá további kódot példák a következő cikkekben talál: 
* [Egy előugró ablak hozzáadása](./map-add-popup.md)
* [Alakzat hozzáadása](./map-add-shape.md)

