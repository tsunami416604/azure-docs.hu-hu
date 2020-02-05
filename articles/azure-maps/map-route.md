---
title: Útvonal irányának megjelenítése a térképen | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg a térképen két hely közötti irányokat a Microsoft Azure Maps web SDK használatával.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 359f2b42ca6f56087be53a5aeb328fe43a478d63
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988282"
---
# <a name="show-directions-from-a-to-b"></a>Útvonal megjelenítése A-ból B-be

Ebből a cikkből megtudhatja, hogyan hajthat végre egy útvonal-kérést, és hogyan jelenítheti meg az útvonalat a térképen.

Ezt kétféleképpen teheti meg. Az első módszer az [Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) lekérdezése egy szolgáltatási modulon keresztül. A második módszer az [API beolvasása](https://fetch.spec.whatwg.org/) , hogy keresési kérést végezzen a [Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)-hoz. Az alábbiakban mindkét módszert ismertetjük.

## <a name="query-the-route-via-service-module"></a>Az útvonal lekérdezése a Service Module használatával

<iframe height='500' scrolling='no' title='Irányok megjelenítése a-tól B-re egy térképen (szolgáltatási modul)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti a tollat a <a href='https://codepen.io/azuremaps/pen/RBZbep/'>-tól B-ig a térképeken (szolgáltatási modul)</a> .
</iframe>

A fenti kódban az első blokk létrehozza a Térkép objektumot, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat használatára. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkja létrehoz egy `TokenCredential` a hozzáférési jogkivonattal Azure Maps HTTP-kérések hitelesítéséhez. Ezután átadja a `TokenCredential`nak `atlas.service.MapsURL.newPipeline()` és létrehoz egy [folyamat](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) -példányt. A `routeURL` a Azure Maps [Route](https://docs.microsoft.com/rest/api/maps/route) műveletekhez tartozó URL-címet jelöli.

A kód harmadik blokkja létrehoz egy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objektumot a térképhez, és hozzáadja azt.

A kód negyedik blokkja a kezdő [és végponti](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objektumokat hozza létre, és hozzáadja őket az adatforrás-objektumhoz.

A vonal a LineString egyik [funkciója](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) . A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) az [adatforrásban](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sorokként burkolt vonalakat jeleníti meg a térképen. A kód negyedik blokkja létrehoz egy sor réteget a térképhez, és hozzáadja azt. Lásd: egy sor rétegének tulajdonságai a következő helyen: [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

A [szimbólum-réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) szövegeket vagy ikonokat [használ a pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)-alapú adatforrásba becsomagolt megjelenítéshez. A szöveg vagy az ikon szimbólumként jelenik meg a térképen. A kód ötödik blokkja egy szimbólum réteget hoz létre, és hozzáadja a térképhez.

A kód hatodik blokkja lekérdezi a Azure Maps útválasztási szolgáltatást, amely a [szolgáltatás modul](how-to-use-services-module.md)részét képezi. A RouteURL [calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) metódusa a kezdő-és végpontok közötti útvonal lekérésére szolgál. A válaszból származó GeoJSON-gyűjteményt a rendszer a `geojson.getFeatures()` metódussal kinyeri, és hozzáadja az adatforráshoz. Ezután a választ útvonalként jeleníti meg a térképen. További információ a vonal a térképhez való hozzáadásáról: [vonal hozzáadása a térképhez](map-add-line-layer.md).

A kód utolsó blokkja a Térkép [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonságával állítja be a leképezés határait.

Az útvonal-lekérdezés, az adatforrás, a szimbólum, a vonal és a kamera határai a Térkép [esemény-figyelőján](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)belül jönnek létre és állíthatók be. A kód szerkezete biztosítja, hogy az eredmények csak a Térkép teljes betöltése után jelenjenek meg.

## <a name="query-the-route-via-fetch-api"></a>Az útvonal lekérdezése a fetch API használatával

<iframe height='500' scrolling='no' title='Az A és B közötti útvonalak megjelenítése térképen' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>-tól B-ig egy térképen</a> a <a href='https://codepen.io'>CodePen</a>Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>).
</iframe>

A fenti kódban a kód első blokkja létrehozza a Térkép objektumot, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat használatára. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkja létrehoz és hozzáadja a térképhez egy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objektumot.

A harmadik kódrészlet létrehozza a kezdő és a célhelyet az útvonalhoz, és hozzáadja azokat az adatforráshoz. A [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)használatával kapcsolatos utasításokért tekintse [meg a térképen a PIN-kód hozzáadása](map-add-pin.md) című témakört.

A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) az [adatforrásban](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sorokként burkolt vonalakat jeleníti meg a térképen. A kód negyedik blokkja létrehoz egy sor réteget a térképhez, és hozzáadja azt. Lásd: egy sor rétegének tulajdonságai a következő helyen: [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

A [szimbólum-réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) szöveggel vagy ikonokkal jeleníti meg a pont-alapú adatforrásokat az [adatforrásban](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) szimbólumként a térképen. A kód ötödik blokkja egy szimbólum réteget hoz létre, és hozzáadja a térképhez. Tekintse meg a szimbólumok rétegének tulajdonságait a következő helyen: [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

A következő kódrészlet `SouthWest` és `NorthEast` pontokat hoz létre a kezdő és a célhelyről, és a Térkép [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonságával állítja be a leképezés határait.

A kód utolsó blokkja a [beolvasás API](https://fetch.spec.whatwg.org/) -t használja a [Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)-ra irányuló keresési kérések elvégzéséhez. A rendszer elemzi a választ. Ha a válasz sikeres volt, a szélességi és a hosszúsági adatokat a rendszer egy sor létrehozásához használja a pontok összekapcsolásával. Ekkor a rendszer hozzáadja az adatforráshoz a sor-adatforrást, hogy az útvonalat a térképen jelenítse meg. Útmutatásért lásd: [vonal hozzáadása a térképen](map-add-line-layer.md) .

Az útvonal-lekérdezés, az adatforrás, a szimbólum, a vonal és a kamera határai a Térkép [esemény-figyelőján](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)belül jönnek létre és állíthatók be. Azt szeretnénk, hogy az eredmények a Térkép teljes betöltése után is megjelenjenek.

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

A következő cikkekben talál részletes példákat:

> [!div class="nextstepaction"]
> [Forgalom megjelenítése a térképen](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interakció a Térkép-egér eseményeivel](./map-events.md)
