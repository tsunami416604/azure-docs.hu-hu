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
ms.openlocfilehash: 37323bacf47613c0faf7769701808ecef2645115
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157953"
---
# <a name="show-directions-from-a-to-b"></a>Útvonal megjelenítése A-ból B-be 

Ez a cikk bemutatja, hogyan indítson egy útvonalat, és az útvonal megjelenítése a térképen.

Ehhez két módszer van, az egyik módszer, amelyet lekérdezésével a [Azure Maps útvonal API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) szolgáltatáson keresztül modul és a egy másik program azáltal, hogy egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) az API-hoz. Mindkettő az alábbiak ismertetik.

## <a name="use-service-module-to-query-for-a-route"></a>Útvonal lekérdezéshez modul használata

## <a name="understand-the-code"></a>A kód értelmezése

<iframe height='500' scrolling='no' title='Útvonal megjelenítése a-b egy térképen (szolgáltatás modul)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/RBZbep/'>útvonal megjelenítése a-b (Szolgáltatásmodul) a térképen</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A sort a második kódblokkot a szolgáltatási ügyfél példányosítja.

A harmadik kódblokk inicializálja a [karakterlánc réteg sor](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings) a térképen.

A negyedik kódblokkot hoz létre, és hozzáadja a PIN-kódok, amelyek a kezdő és záró pontot, az útvonal a térképen. Látható [PIN-kód hozzáadása a térképen](map-add-pin.md) útmutatást.

Használja a következő kódblokkot [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) beállítása a térkép a határolókeret térkép osztály függvény a kezdő és záró pontot, az útvonal alapján.

A hatodik kódblokkot egy útvonal lekérdezést hoz létre.

A legutóbbi kódblokkot keresztül az Azure Maps útválasztási szolgáltatás lekérdezi a [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) metódus egy útvonalat a kezdő és a cél pont közötti lekéréséhez. A válasz majd szűrőtulajdonság GeoJSON formátumú használatával a [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) metódust. Hozzáadja a sorokra a térképre az útvonal megjelenítése. Látható [adjon hozzá egy sort a térképen](./map-add-shape.md#addALine) további információt.

## <a name="use-xmlhttprequest-to-query-for-a-route"></a>Lekérdezés útvonal XMLHTTPRequest használatával

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

* [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections)
* [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes)
* [Vonalréteg karakterlánc](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
* [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)

A maps hozzá további kódot példák a következő cikkekben talál: 
* [Forgalom megjelenítése a térképen](./map-show-traffic.md)
* [A térképek - egér események](./map-events.md)
