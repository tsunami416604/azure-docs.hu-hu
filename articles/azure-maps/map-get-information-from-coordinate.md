---
title: Egy Térkép koordinátáival kapcsolatos információk megjelenítése | Microsoft Azure térképek
description: Megtudhatja, hogyan jelenítheti meg a térképen a címekre vonatkozó információkat, amikor a felhasználó kiválaszt egy koordinátákat.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 174bdc496e52a6ac8f2a2d631db92e0f21a819be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80371423"
---
# <a name="get-information-from-a-coordinate"></a>Koordináta információinak lekérése

Ebből a cikkből megtudhatja, hogyan végezhet fordított címtartomány-keresést, amely megjeleníti egy rákattintott előugró hely címeit.

A fordított címek keresésének kétféleképpen végezhető el. Az egyik módszer az, ha lekérdezi az [Azure Maps fordított címtartomány-keresés API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) -t egy szolgáltatási modulon keresztül. A másik lehetőség, hogy a [beolvasás API](https://fetch.spec.whatwg.org/) használatával kérelmet küld a [Azure Maps fordított CÍMTARTOMÁNY-kereső API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) -nak a címek kereséséhez. Az alábbiakban mindkét módszert megvizsgáljuk.

## <a name="make-a-reverse-search-request-via-service-module"></a>Fordított keresési kérelem küldése a szolgáltatási modulon keresztül

<iframe height='500' scrolling='no' title='Információ lekérése egy koordinátaből (szolgáltatási modul)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) című cikkből megtudhatja, hogyan <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>kérhet le adatokat a koordináta (szolgáltatási modul) szolgáltatásból</a> .
</iframe>

A fenti kódban az első blokk létrehozza a Térkép objektumot, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat használatára. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A második kódrészlet létrehoz egy `TokenCredential` -t, amely hitelesíti a hozzáférési jogkivonattal Azure maps http-kérelmeket. Ezután továbbítja a `TokenCredential` -t `atlas.service.MapsURL.newPipeline()` , és létrehoz egy [folyamat](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) -példányt. A `searchURL` a Azure Maps [keresési](https://docs.microsoft.com/rest/api/maps/search) műveletekhez tartozó URL-címet jelöli.

A harmadik kódú blokk frissíti az egérmutató stílusát egy mutatóra, és létrehoz egy [előugró](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objektumot. Útmutatásért tekintse [meg az előugró ablak hozzáadása a térképen](./map-add-popup.md) .

A kód negyedik blokkja egy egérkattintással jeleníti meg az [esemény-figyelőt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Az aktiváláskor egy keresési lekérdezést hoz létre a rákattintott pont koordinátáival. Ezután a [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)metódus használatával kérdezi le a [keresési fordított API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) -t a koordináták címeihez. Ezután a válaszból a `geojson.getFeatures()` metódussal Kinyer egy GeoJSON-gyűjteményt.

A kód ötödik blokkja beállítja a HTML-előugró tartalmat, hogy megjelenjen a válasz címe a rákattintott koordináta-pozícióhoz.

A kurzor, a felugró objektum és a click esemény változása mind a Térkép [betöltési eseményének figyelőben](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)jön létre. A kód szerkezete biztosítja a Térkép teljes terhelését a koordináták adatainak beolvasása előtt.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Fordított keresési kérelem készítése a fetch API használatával

A térképre kattintva fordított geocode-kérelmet készíthet az adott helyhez a fetch használatával.

<iframe height='500' scrolling='no' title='Koordináta információinak lekérése' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io'>CodePen</a>-on található<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () által <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>koordinált adatokból</a> .
</iframe>

A fenti kódban a kód első blokkja létrehozza a Térkép objektumot, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat használatára. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkja frissíti az egérmutató stílusát egy mutatóra. Előugró objektumot hoz [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) létre. Útmutatásért tekintse [meg az előugró ablak hozzáadása a térképen](./map-add-popup.md) .

A kód harmadik blokkja egy esemény-figyelőt hoz létre egérkattintáshoz. Ha egérkattintásra kattint, a [beolvasás API](https://fetch.spec.whatwg.org/) használatával kérdezi le a [Azure Maps fordított CÍMTARTOMÁNY-keresés API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) -t a rákattintott koordináták címeire. A sikeres válaszhoz a rendszer a kijelölt helyhez tartozó címeket gyűjti. Meghatározza az előugró tartalmat és a pozíciót az előugró osztály [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) funkciójával.

A kurzor, a felugró objektum és a click esemény változása mind a Térkép [betöltési eseményének figyelőben](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)jön létre. A kód szerkezete biztosítja a Térkép teljes terhelését a koordináták adatainak beolvasása előtt.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ajánlott eljárások a keresési szolgáltatás használatához](how-to-use-best-practices-for-search.md)

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Felugró ablak](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

A következő cikkekben talál részletes példákat:

> [!div class="nextstepaction"]
> [Útvonal megjelenítése A-ból B-be](./map-route.md)

> [!div class="nextstepaction"]
> [Forgalom megjelenítése](./map-show-traffic.md)
