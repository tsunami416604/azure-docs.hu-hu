---
title: Egy Térkép koordinátáival kapcsolatos információk megjelenítése | Microsoft Azure térképek
description: Megtudhatja, hogyan jelenítheti meg a térképen a címekre vonatkozó információkat, amikor a felhasználó kiválaszt egy koordinátákat.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5450ada04a1af44a3fff0402b30540e899cc4dd5
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911045"
---
# <a name="get-information-from-a-coordinate"></a>Koordináta információinak lekérése

Ebből a cikkből megtudhatja, hogyan végezhet fordított címtartomány-keresést, amely megjeleníti egy rákattintott előugró hely címeit.

A fordított címek keresésének kétféleképpen végezhető el. Az egyik módszer az, ha lekérdezi az [Azure Maps fordított címtartomány-keresés API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) -t egy szolgáltatási modulon keresztül. A másik lehetőség, hogy a [beolvasás API](https://fetch.spec.whatwg.org/) -t használja, hogy egy adott címnek megfelelő kérést kérjen a [Azure Maps fordított CÍMTARTOMÁNY-kereső API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) -nak. Az alábbiakban mindkét módszert megvizsgáljuk.

## <a name="make-a-reverse-search-request-via-service-module"></a>Fordított keresési kérelem küldése a szolgáltatási modulon keresztül

<iframe height='500' scrolling='no' title='Információ lekérése egy koordinátaből (szolgáltatási modul)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti a tollat a <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>koordináta (szolgáltatás modul)</a> segítségével.
</iframe>

A fenti kódban a kód első blokkja létrehozza a Térkép objektumot, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat kihasználása érdekében. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkja létrehoz egy `TokenCredential` a hozzáférési jogkivonattal Azure Maps HTTP-kérések hitelesítéséhez. Ezután átadja a `TokenCredential`nak `atlas.service.MapsURL.newPipeline()` és létrehoz egy [folyamat](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) -példányt. A `searchURL` Azure Maps [keresési](https://docs.microsoft.com/rest/api/maps/search) műveletekhez tartozó URL-címet jelöli.

A kód harmadik blokkja frissíti az egérmutató stílusát egy mutatóra, és létrehoz egy [előugró](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objektumot. Útmutatásért tekintse [meg az előugró ablak hozzáadása a térképen](./map-add-popup.md) .

A kód negyedik blokkja egy egérkattintással jeleníti meg az [esemény-figyelőt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Az aktiváláskor egy keresési lekérdezést hoz létre a rákattintott pont koordinátáival. Ezután a szolgáltatás moduljának [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) metódusával lekérdezi a [keresési fordított API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) -t a koordináták címeihez. A válaszból származó GeoJSON-gyűjteményt a rendszer a `geojson.getFeatures()` metódus használatával kinyeri.

A kód ötödik blokkja beállítja a HTML-előugró tartalmat, hogy megjelenjen a válasz címe a rákattintott koordináta-pozícióhoz.

A Térkép [betöltési eseményének figyelője](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) a kurzor, egy előugró objektum és a click (kattintás) esemény módosításával gondoskodik arról, hogy a Térkép betöltése teljesen megtörténjen a koordináták adatainak beolvasása előtt.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Fordított keresési kérelem készítése a fetch API használatával

A térképre kattintva fordított geocode-kérelmet készíthet az adott helyhez a fetch használatával.

<iframe height='500' scrolling='no' title='Koordináta információinak lekérése' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>a tollat a</a> <a href='https://codepen.io'>CodePen</a>Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) segítségével.
</iframe>

A fenti kódban a kód első blokkja létrehozza a Térkép objektumot, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat kihasználása érdekében. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkja az egérmutató és a [felugró](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objektum stílusát frissíti. Útmutatásért tekintse [meg az előugró ablak hozzáadása a térképen](./map-add-popup.md) .

A kód harmadik blokkja egy esemény-figyelőt hoz létre egérkattintáshoz. Ha egérkattintásra kattint, a [beolvasás API](https://fetch.spec.whatwg.org/) -val lekérdezi az [Azure Maps reverse Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) -t a rákattintott koordináták címeire. A sikeres válaszhoz a rendszer a rákattintott helyhez tartozó címeket gyűjti, és az előugró osztály [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) funkcióján keresztül meghatározza az előugró tartalmat és a pozíciót.

A Térkép [betöltési eseményének figyelője](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) a kurzor, egy előugró objektum és a click (kattintás) esemény módosításával gondoskodik arról, hogy a Térkép betöltése teljesen megtörténjen a koordináták adatainak beolvasása előtt.

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

A következő cikkekben talál részletes példákat:

> [!div class="nextstepaction"]
> [Irányok megjelenítése a-tól B-be](./map-route.md)

> [!div class="nextstepaction"]
> [Forgalom megjelenítése](./map-show-traffic.md)
