---
title: Az Azure Maps-útvonal megjelenítése |} A Microsoft Docs
description: Javascript a térképen két hely közötti kétirányú megjelenítése
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: ed522779f5a86e38ee12a246cea9ac85d0379f9e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729059"
---
# <a name="show-directions-from-a-to-b"></a>Útvonal megjelenítése A-ból B-be

Ez a cikk bemutatja, hogyan indítson egy útvonalat, és az útvonal megjelenítése a térképen.

Ehhez két módja van. Az első lehetőség lekérdezés a [Azure Maps útvonal API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) egy modul segítségével. A második lehetőség, győződjön meg arról, hogy egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) az API-hoz. Mindkét irányban keresztszűréssel az alábbiak ismertetik.

## <a name="query-the-route-via-service-module"></a>Lekérdezése a route service modulon keresztül

<iframe height='500' scrolling='no' title='Útvonal megjelenítése a-b egy térképen (szolgáltatás modul)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/RBZbep/'>útvonal megjelenítése a-b (Szolgáltatásmodul) a térképen</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A sort a második kódblokkot a szolgáltatási ügyfél példányosítja.

A harmadik kódblokk inicializálja a [karakterlánc réteg sor](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings) a térképen.

A negyedik kódblokkot hoz létre, és hozzáadja a PIN-kódok, amelyek a kezdő és záró pontot, az útvonal a térképen. Látható [PIN-kód hozzáadása a térképen](map-add-pin.md) használatára vonatkozó utasítások [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).

Használja a következő kódblokkot [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) beállítása a térkép a határolókeret térkép osztály függvény a kezdő és záró pontot, az útvonal alapján.

A hatodik kódblokkot egy útvonal lekérdezést hoz létre.

A legutóbbi kódblokkot keresztül az Azure Maps útválasztási szolgáltatás lekérdezi a [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) metódus egy útvonalat a kezdő és a cél pont közötti lekéréséhez. A válasz majd szűrőtulajdonság GeoJSON formátumú használatával a [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) metódust. Hozzáadja a sorokra a térképre az útvonal megjelenítése. További információért lásd a [vonal térképhez való hozzáadását](./map-add-shape.md#addALine) ismertető cikket.

## <a name="query-the-route-via-xmlhttprequest"></a>Az útvonal keresztül XMLHttpRequest lekérdezése

<iframe height='500' scrolling='no' title='Útvonal megjelenítése a-b a térképen' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>útvonal megjelenítése a-b egy térképen</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot hoz létre, és hozzáadja a PIN-kódok, amelyek a kezdő és záró pontot, az útvonal a térképen. Látható [PIN-kód hozzáadása a térképen](map-add-pin.md) használatára vonatkozó utasítások [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).

A harmadik kódblokkot használ [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) beállítása a térkép a határolókeret térkép osztály függvény a kezdő és záró pontot, az útvonal alapján.

A negyedik kódblokkot küld egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) való [Azure Maps útvonal API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

A legutóbbi kódblokkot elemzi a bejövő válasz. A sikeres válasz összegyűjti az egyes waypoint a szélességi és hosszúsági adatokat. Vonalak egy tömb minden egyes waypoint csatlakozik az ezt követő waypoint hoz létre. Hozzáadja a sorokra a térképre az útvonal megjelenítése. Látható [adjon hozzá egy sort a térképen](./map-add-shape.md#addALine) útmutatást.

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

A teljes hitelesítésikód-példák a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Forgalom megjelenítése a térképen](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [A térképek - egér események](./map-events.md)
