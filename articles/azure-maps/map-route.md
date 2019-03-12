---
title: Az Azure Maps-útvonal megjelenítése |} A Microsoft Docs
description: Javascript a térképen két hely közötti kétirányú megjelenítése
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 786880c5fa919fce5ed60d011211e6d7348f7260
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570062"
---
# <a name="show-directions-from-a-to-b"></a>Útvonal megjelenítése A-ból B-be

Ez a cikk bemutatja, hogyan indítson egy útvonalat, és az útvonal megjelenítése a térképen.

Ehhez két módja van. Az első lehetőség lekérdezés a [Azure Maps útvonal API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) egy modul segítségével. A második lehetőség, hogy kihasználhassák a [Fetch API](https://fetch.spec.whatwg.org/) való keresés kérheti a [Azure Maps útvonal API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Mindkét irányban keresztszűréssel az alábbiak ismertetik.

## <a name="query-the-route-via-service-module"></a>Lekérdezése a route service modulon keresztül

<iframe height='500' scrolling='no' title='Útvonal megjelenítése a-b egy térképen (szolgáltatás modul)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/RBZbep/'>útvonal megjelenítése a-b (Szolgáltatásmodul) a térképen</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre, és beállítja a hitelesítési mechanizmust, az előfizetési kulcsot használják. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot létrehoz egy **SubscriptionKeyCredentialPolicy** az előfizetési kulcsot az Azure Maps HTTP-kérések hitelesítéséhez. A **atlas.service.MapsURL.newPipeline()** veszi a **SubscriptionKeyCredential** házirend, és létrehoz egy [folyamat](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) példány. A **routeURL** egy URL-címet jelöli az Azure Maps [útvonal](https://docs.microsoft.com/rest/api/maps/route) műveleteket.

A harmadik kódblokkot hoz létre, és hozzáad egy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objektum, melyet a térképen.

A negyedik kódblokkot hoz létre a kezdő és záró [pontok](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objektumokat, és hozzáadja őket az adatkészlet-objektumot.

Egy sor egy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) LineString. A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) rendereket sor objektumok csomagolni a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sorokként a térképen. A negyedik kódblokkot hoz létre, és a egy vonalréteg hozzáadása a térképen. Megtekintheti a tulajdonságait, egy sor réteg [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

A [szimbólum réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) használja a szöveges vagy ikonjai csomagolni szoftverfrissítésipont-alapú adatok megjelenítése a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) szimbólumra a térképen. Az ötödik kódblokkot hoz létre, és adja hozzá egy szimbólum réteget a térkép.

A hatodik kódblokkot lekérdezi a az Azure Maps útválasztási szolgáltatás részét képezi, a [modul](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=2). A [calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) a RouteURL metódusa egy útvonalat a kezdő és a végpontok közötti használnak. A válaszból GeoJSON funkció gyűjtemény majd ki kell olvasni használatával a **geojson.getFeatures()** metódus és az adatforrás van adva. Egy útvonalat a térképen, a válasz majd megjelenítve. Egy sort a térkép történő hozzáadásával kapcsolatos további információkért lásd: [adjon hozzá egy sort a térképen](./map-add-shape.md#addALine).

A legutóbbi kódblokkot beállítja a térkép a leképezéssel meze [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonság.

Létrehozza és a térkép belül az útválasztási lekérdezés, adatforrás, szimbólumok, és a sor rétegek és a kamera határokon [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) annak érdekében, hogy az eredmények jelennek meg a térkép teljes betölti követően.

## <a name="query-the-route-via-fetch-api"></a>A Fetch API-n keresztül útvonal lekérdezése

<iframe height='500' scrolling='no' title='Útvonal megjelenítése a-b a térképen' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>útvonal megjelenítése a-b egy térképen</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre, és beállítja a hitelesítési mechanizmust, az előfizetési kulcsot használják. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot hoz létre, és hozzáad egy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objektum, melyet a térképen.

A harmadik kódblokkot az útvonal a kezdő és a cél pontokat hoz létre, és hozzáadja őket az adatforrásba. Látható [PIN-kód hozzáadása a térképen](map-add-pin.md) használatára vonatkozó utasítások [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest).

A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) rendereket sor objektumok csomagolni a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sorokként a térképen. A negyedik kódblokkot hoz létre, és a egy vonalréteg hozzáadása a térképen. Megtekintheti a tulajdonságait, egy sor réteg [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

A [szimbólum réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) használja a szöveges vagy ikonjai csomagolni szoftverfrissítésipont-alapú adatok megjelenítése a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) szimbólumra a térképen. Az ötödik kódblokkot hoz létre, és adja hozzá egy szimbólum réteget a térkép. Tekintse meg a szimbólum réteg tulajdonságainak [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

A következő kódblokk hoz létre `SouthWest` és `NorthEast` kezdő- és cél pontokról mutat, és beállítja a térkép a leképezéssel meze [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonság.

A legutóbbi kódblokkot használja a [Fetch API](https://fetch.spec.whatwg.org/) való keresés kérheti a [Azure Maps útvonal API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Ezután a bejövő válasz elemzését végzi. És a sikeres válasz, az egyes útvonal a szélességi és hosszúsági adatokat gyűjt, és létrehoz egy tömböt, a sorok azokat a pontokat csatlakozással. Ezután hozzáadja az alakzatot a térképen az útvonal megjelenítése az adatforrás minden sorokra. Látható [adjon hozzá egy sort a térképen](./map-add-shape.md#addALine) útmutatást.

Létrehozza és a térkép belül az útválasztási lekérdezés, adatforrás, szimbólumok, és a sor rétegek és a kamera határokon [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) annak érdekében, hogy az eredmények jelennek meg a térkép teljes betölti követően.

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

A teljes hitelesítésikód-példák a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Forgalom megjelenítése a térképen](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [A térképek - egér események](./map-events.md)
