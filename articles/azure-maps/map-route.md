---
title: Az Azure-leképezések irányban megjelenítése |} Microsoft Docs
description: A Javascript térképen két hely közötti kétirányú megjelenítése
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 5e9ab73ddc16517e17894cddd9bc102f3941f00c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "35759798"
---
# <a name="show-directions-from-a-to-b"></a>Útvonal megjelenítése A-ból B-be 

Ez a cikk bemutatja, hogyan útvonal kérés és az útvonal megjelenítése a térképen. 

## <a name="understand-the-code"></a>A kód értelmezése

<iframe height='500' scrolling='no' title='Megjelenítése irányban-ről a b kiszolgálóra a térképen' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>megjelenítése irányban-ről a b kiszolgálóra a térképen</a> Azure leképezésekhez (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódot az első kódblokkot egy térkép objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) utasításokat.

A második kódblokkot hoz létre, és hozzáadja a PIN-kódok képviselő kezdő- és végpont az útvonal a térképen. Látható [PIN kód hozzáadásához a térképen](map-add-pin.md) utasításokat.

A harmadik kódblokkot használ [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) függvény a térkép osztály a térkép határolókeret beállítása a kezdő- és végpontok az útvonal alapján.

A negyedik kódblokkot küld egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) való [Azure Maps útvonal API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

Az utolsó kódblokkot elemzi a bejövő válasz. A sikeres válasz összegyűjti a szélességi és hosszúsági minden waypoint adatai. Minden waypoint csatlakozva a későbbi waypoint hoz létre sorok tömbjét. Hozzáadja a térképre az útvonal megjelenítéséhez érintett sorok. Látható [adja hozzá a sort a térképen](./map-add-shape.md#addALine) utasításokat.

## <a name="next-steps"></a>További lépések

További tudnivalók az osztályok és az ebben a cikkben használt módszerek: 

* [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)

További kód hozzáadása a maps, tekintse meg a következő cikkeket: 
* [A térképen forgalom megjelenítése](./map-show-traffic.md)
* [A térkép - egér események való interakció](./map-events.md)
