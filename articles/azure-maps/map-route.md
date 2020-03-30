---
title: Útvonalútvonal-útvonalak megjelenítése a térképen | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg a két hely közötti útvonaltervet a térképen a Microsoft Azure Maps Web SDK használatával.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: dde9264d0cb65726b624b918982cfa01985b63ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371412"
---
# <a name="show-directions-from-a-to-b"></a>Útvonal megjelenítése A-ból B-be

Ez a cikk bemutatja, hogyan lehet útvonalkérelmet benyújtani, és megjeleníteni az útvonalat a térképen.

Ezt kétféleképpen lehet megtenni. Az első módja az [Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) lekérdezése egy szolgáltatásmodulon keresztül. A második módszer az, hogy a [Fetch API használatával](https://fetch.spec.whatwg.org/) keresési kérelmet az Azure Maps Route API.The second way is to use the Fetch API to make a search request to the Azure Maps Route [API.](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) Mindkét irányban az alábbiakban tárgyaljuk.

## <a name="query-the-route-via-service-module"></a>Az útvonal lekérdezése a szervizmodulon keresztül

<iframe height='500' scrolling='no' title='Útvonaltervek megjelenítése A-tól B-ig a térképen (szervizmodul)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Show útvonalterveket A-tól B-ig az</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) által a CodePen térképen (szervizmodul) című <a href='https://codepen.io'>témakörben.</a>
</iframe>

A fenti kódban az első blokk létrehoz egy térképobjektumot, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat használatára. Láthatjuk [hozzon létre egy térképet](./map-create.md) az utasításokat.

A második kódblokk `TokenCredential` létrehoz egy HTTP-kérelmek hitelesítéséhez az Azure Maps a hozzáférési jogkivonattal. Ezután átadja `TokenCredential` a `atlas.service.MapsURL.newPipeline()` továbbad egy folyamatpéldányt, és létrehoz egy [folyamatpéldányt.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) Az `routeURL` Azure Maps [Route-műveletek](https://docs.microsoft.com/rest/api/maps/route) URL-címét jelöli.

A harmadik kódblokk létrehoz és hozzáad egy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objektumot a térképhez.

A negyedik kódblokk kezdő- és [végponti objektumokat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) hoz létre, és hozzáadja őket a dataSource objektumhoz.

A vonal a LineString [szolgáltatása.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) a [DataSource-ba](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) burkolt vonalobjektumokat a térképen lévő vonalakként jeleníti meg. A negyedik kódblokk létrehoz és hozzáad egy vonalréteget a térképhez. Lásd egy vonalréteg tulajdonságainak megtekintéséhez a [LinestringLayerOptions oldalon.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

A [szimbólumréteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) szövegeket vagy ikonokat használ a adatforrásba burkolt pontalapú adatok [megjelenítéséhez.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) A szövegek vagy az ikonok szimbólumként jelennek meg a térképen. Az ötödik kódblokk létrehoz és hozzáad egy szimbólumréteget a térképhez.

A hatodik kódblokk lekérdezi az Azure Maps útválasztási szolgáltatását, amely a [szolgáltatásmodul](how-to-use-services-module.md)része. A [RouteURL calculateRouteS](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) metódusa a kezdő és a végpontok közötti útvonal leválasztására szolgál. A válasz geoJSON-szolgáltatásgyűjteményét ezután `geojson.getFeatures()` kinyeri a metódus, és hozzáadja az adatforráshoz. Ezután a választ útvonalként jeleníti meg a térképen. Ha többet szeretne tudni arról, hogy miként adhat hozzá vonalat a térképhez, olvassa el a Vonal hozzáadása a térképen című [témakört.](map-add-line-layer.md)

Az utolsó kódblokk a térkép határait állítja be a Map [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonságával.

Az útvonallekérdezés, az adatforrás, a szimbólum, a vonalrétegek és a kamerahatárok az [eseményfigyelőn](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)belül jönnek létre. Ez a kódstruktúra biztosítja, hogy az eredmények csak a térkép teljes betöltése után jelenjenek meg.

## <a name="query-the-route-via-fetch-api"></a>Az útvonal lekérdezése a Fetch API-n keresztül

<iframe height='500' scrolling='no' title='Útvonaltervek megjelenítése A-tól B-ig a térképen' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Toll <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>megjelenítése útvonalterveket A-tól B-ig az</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) térképén a <a href='https://codepen.io'>CodePen oldalon.</a>
</iframe>

A fenti kódban az első kódblokk egy térképobjektumot hoz létre, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat használatára. Láthatjuk [hozzon létre egy térképet](./map-create.md) az utasításokat.

A második kódblokk létrehoz és hozzáad egy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objektumot a térképhez.

A harmadik kódblokk létrehozza az útvonal kezdő- és célpontjait. Ezután hozzáadja őket az adatforráshoz. Láthatjuk [hozzá egy pin a térképen](map-add-pin.md) az utasításokat segítségével [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest).

A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) a [DataSource-ba](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) burkolt vonalobjektumokat a térképen lévő vonalakként jeleníti meg. A negyedik kódblokk létrehoz és hozzáad egy vonalréteget a térképhez. Lásd egy vonalréteg tulajdonságainak megtekintéséhez a [LineLayerOptions oldalon.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

A [szimbólumréteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) szöveget vagy ikonokat használ az [Adatforrásban](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) lévő pontalapú adatok nak a térképen lévő szimbólumokként való megjelenítéséhez. Az ötödik kódblokk létrehoz és hozzáad egy szimbólumréteget a térképhez. Lásd a szimbólumréteg tulajdonságait a [SymbolLayerOptions oldalon.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

A következő kódblokk létrehozza `SouthWest` és `NorthEast` pontokat a kezdő és a célpontok, és beállítja a határait a térkép segítségével a map [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonság.

Az utolsó kódblokk a [Fetch API-t](https://fetch.spec.whatwg.org/) használja az [Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)keresési kérelmének. A válasz ezután elemzi. Ha a válasz sikeres volt, a szélességi és hosszúsági adatok segítségével tömböt hozunk létre a pontok összekapcsolásával. A vonaladatok ezután hozzáadódnak az adatforráshoz, hogy az útvonal megjelenjen a térképen. Az utasításokért a [térképen látható,](map-add-line-layer.md) hogy egy sort is hozzáadhat.

Az útvonallekérdezés, az adatforrás, a szimbólum, a vonalrétegek és a kamerahatárok az [eseményfigyelőn](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)belül jönnek létre. Ismét biztosítani szeretnénk, hogy az eredmények a térkép teljes betöltése után jelenjenek meg.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Gyakorlati tanácsok az útválasztási szolgáltatás használatához](how-to-use-best-practices-for-search.md)

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

A teljes kódpéldákat az alábbi cikkekben talál:

> [!div class="nextstepaction"]
> [Forgalom megjelenítése a térképen](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Kölcsönhatásban áll a térkép - egér események](./map-events.md)
