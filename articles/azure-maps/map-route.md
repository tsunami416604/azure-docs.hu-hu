---
title: Az Azure Maps-útvonal megjelenítése |} A Microsoft Docs
description: Javascript a térképen két hely közötti kétirányú megjelenítése
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 4f999df23b5c08125b107f15091d90d85a045908
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586290"
---
# <a name="show-directions-from-a-to-b"></a>Útvonal megjelenítése A-ból B-be

Ez a cikk bemutatja, hogyan indítson egy útvonalat, és az útvonal megjelenítése a térképen.

Ehhez két módja van. Az első lehetőség lekérdezés a [Azure Maps útvonal API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) egy modul segítségével. A második lehetőség, győződjön meg arról, hogy egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) az API-hoz. Mindkét irányban keresztszűréssel az alábbiak ismertetik.

## <a name="query-the-route-via-service-module"></a>Lekérdezése a route service modulon keresztül

<iframe height='500' scrolling='no' title='Útvonal megjelenítése a-b egy térképen (szolgáltatás modul)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/RBZbep/'>útvonal megjelenítése a-b (Szolgáltatásmodul) a térképen</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A vonal jelzi a második kódblokkot egy ügyfél szolgáltatás példányosítja.

A harmadik hoz létre, és hozzáad egy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objektum, melyet a térképen.

 Egy sor egy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) LineString. A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) rendereket sor objektumok csomagolni a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sorokként a térképen. A negyedik kódblokkot hoz létre, és a egy vonalréteg hozzáadása a térképen. Megtekintheti a tulajdonságait, egy sor réteg [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

A [szimbólum réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) használja a szöveges vagy ikonjai csomagolni szoftverfrissítésipont-alapú adatok megjelenítése a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) szimbólumra a térképen. Az ötödik kódblokkot hoz létre, és adja hozzá egy szimbólum réteget a térkép.

A hatodik kódblokkot hoz létre a kezdő és záró [pontok](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objektumot, és hozzáadja őket az adatkészlet-objektumot.

A hetedik kódblokkot beállítja a térkép a leképezéssel meze [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonság.

A legutóbbi kódblokkot lekérdezi a az Azure Maps útválasztási szolgáltatás részét képezi, a [modul](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1). A [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest) módszert kéri le a kezdő és a végpontok közötti egy útvonalat. A válasz majd szűrőtulajdonság GeoJSON formátumú használatával a [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routegeojson?view=azure-iot-typescript-latest) metódust. Egy útvonalat a térképen, a válasz majd megjelenítve. Egy sort a térkép történő hozzáadásával kapcsolatos további információkért lásd: [adjon hozzá egy sort a térképen](./map-add-shape.md#addALine).

Létrehozza és a térkép belül az útválasztási lekérdezés, adatforrás, szimbólumok, és a sor rétegek és a kamera határokon [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) annak érdekében, hogy az eredmények jelennek meg a térkép teljes betölti követően.

## <a name="query-the-route-via-xmlhttprequest"></a>Az útvonal keresztül XMLHttpRequest lekérdezése

<iframe height='500' scrolling='no' title='Útvonal megjelenítése a-b a térképen' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>útvonal megjelenítése a-b egy térképen</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot hoz létre, és hozzáad egy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objektum, melyet a térképen.

A harmadik kódblokkot az útvonal a kezdő és a cél pontokat hoz létre, és hozzáadja őket az adatforrásba. Látható [PIN-kód hozzáadása a térképen](map-add-pin.md) használatára vonatkozó utasítások [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest).

 A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) rendereket sor objektumok csomagolni a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sorokként a térképen. A negyedik kódblokkot hoz létre, és a egy vonalréteg hozzáadása a térképen. Megtekintheti a tulajdonságait, egy sor réteg [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

A [szimbólum réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) használja a szöveges vagy ikonjai csomagolni szoftverfrissítésipont-alapú adatok megjelenítése a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) szimbólumra a térképen. Az ötödik kódblokkot hoz létre, és adja hozzá egy szimbólum réteget a térkép. Tekintse meg a szimbólum réteg tulajdonságainak [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

A következő kódblokk hoz létre `SouthWest` és `NorthEast` kezdő- és cél pontokról mutat, és beállítja a térkép a leképezéssel meze [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonság.

A legutóbbi kódblokkot küld egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) való [Azure Maps útvonal API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Ezután a bejövő válasz elemzését végzi. És a sikeres válasz, az egyes útvonal a szélességi és hosszúsági adatokat gyűjt, és létrehoz egy tömböt, a sorok azokat a pontokat csatlakozással. Ezután hozzáadja az alakzatot a térképen az útvonal megjelenítése az adatforrás minden sorokra. Látható [adjon hozzá egy sort a térképen](./map-add-shape.md#addALine) útmutatást.

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
