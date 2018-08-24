---
title: Az Azure Maps-útvonal megjelenítése |} A Microsoft Docs
description: Javascript a térképen két hely közötti kétirányú megjelenítése
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 52462c1c5a2a1a9698a2b51708e63b1bb1664f6e
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42745536"
---
# <a name="show-directions-from-a-to-b"></a>Útvonal megjelenítése A-ból B-be 

Ez a cikk bemutatja, hogyan indítson egy útvonalat, és az útvonal megjelenítése a térképen. 

## <a name="understand-the-code"></a>A kód értelmezése

<iframe height='500' scrolling='no' title='Útvonal megjelenítése a-b a térképen' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>útvonal megjelenítése a-b egy térképen</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot hoz létre, és hozzáadja a PIN-kódok, amelyek a kezdő és záró pontot, az útvonal a térképen. Látható [PIN-kód hozzáadása a térképen](map-add-pin.md) útmutatást.

A harmadik kódblokkot használ [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) beállítása a térkép a határolókeret térkép osztály függvény a kezdő és záró pontot, az útvonal alapján.

A negyedik kódblokkot küld egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) való [Azure Maps útvonal API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

A legutóbbi kódblokkot elemzi a bejövő válasz. A sikeres válasz összegyűjti az egyes waypoint a szélességi és hosszúsági adatokat. Vonalak egy tömb minden egyes waypoint csatlakozik az ezt követő waypoint hoz létre. Hozzáadja a sorokra a térképre az útvonal megjelenítése. Látható [adjon hozzá egy sort a térképen](./map-add-shape.md#addALine) útmutatást.

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja: 

* [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)

A maps hozzá további kódot példák a következő cikkekben talál: 
* [Forgalom megjelenítése a térképen](./map-show-traffic.md)
* [A térképek - egér események](./map-events.md)
