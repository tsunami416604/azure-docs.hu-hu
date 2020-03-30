---
title: Koordinátával kapcsolatos információk megjelenítése a térképen | Microsoft Azure Maps
description: Megtudhatja, hogy miként jelenítheti meg egy cím adatait a térképen, amikor a felhasználó koordinátát választ.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 174bdc496e52a6ac8f2a2d631db92e0f21a819be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371423"
---
# <a name="get-information-from-a-coordinate"></a>Koordináta információinak lekérése

Ez a cikk bemutatja, hogyan lehet fordított címkeresést végzett, amely egy kattintott előugró hely címét mutatja.

Kétféleképpen lehet fordított címkeresést készíteni. Ennek egyik módja az [Azure Maps reverse address search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) lekérdezése egy szolgáltatásmodulon keresztül. A másik módja az, hogy a [Fetch API-t,](https://fetch.spec.whatwg.org/) hogy egy kérést az [Azure Maps fordított címkeresési API-t,](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) hogy egy címet. Mindkét irányban az alábbiakban felmért.

## <a name="make-a-reverse-search-request-via-service-module"></a>Fordított keresési kérelem kérése a szolgáltatásmodulon keresztül

<iframe height='500' scrolling='no' title='Információk bekéselése koordinátából (szervizmodul)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Toll beolvassa az Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>( ) <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>koordinátáiból származó információk beírását</a> a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

A fenti kódban az első blokk létrehoz egy térképobjektumot, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat használatára. Láthatjuk [hozzon létre egy térképet](./map-create.md) az utasításokat.

A második kódblokk `TokenCredential` létrehoz egy http-kérelmek hitelesítésére az Azure Maps a hozzáférési jogkivonattal. Ezután átadja `TokenCredential` a `atlas.service.MapsURL.newPipeline()` továbbad egy folyamatpéldányt, és létrehoz egy [folyamatpéldányt.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) Az `searchURL` Azure Maps [Search-műveletek](https://docs.microsoft.com/rest/api/maps/search) URL-címét jelöli.

A harmadik kódblokk az egérmutató stílusát egy mutatóra frissíti, és [előugró](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objektumot hoz létre. Az utasításokért [megjelenik egy felugró ablak hozzáadása a térképen.](./map-add-popup.md)

A negyedik kódblokk hozzáad egy egérkattintási [eseményfigyelőt.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) Aktiváláskor keresési lekérdezést hoz létre a kattintási pont koordinátáival. Ezután a [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)metódust használja a [keresési cím fordított lekérdezése API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) lekérdezéséhez a koordináták címéhez. A GeoJSON szolgáltatásgyűjtemény ezután `geojson.getFeatures()` kibontja a metódust a válaszból.

Az ötödik kódblokk beállítja a HTML előugró tartalmat, hogy megjelenítse a kattintással megadott koordináta-pozíció válaszcímét.

A kurzor módosítása, az előugró objektum és a kattintási esemény mind a térkép [betöltési eseményfigyelőjében](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)jön létre. Ez a kódstruktúra biztosítja a teljes terhelés leképezését a koordináta-információk beolvasása előtt.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Fordított keresési kérelem kérése a Fetch API-n keresztül

Kattintson a térképre, hogy fordított geokód kérelmet az adott helyre a fetch használatával.

<iframe height='500' scrolling='no' title='Koordináta információinak lekérése' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Toll információinak<a href='https://codepen.io/azuremaps'>@azuremaps</a>beolvassa az Azure Maps ( ) <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>koordinátáiból</a> a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

A fenti kódban az első kódblokk egy térképobjektumot hoz létre, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat használatára. Láthatjuk [hozzon létre egy térképet](./map-create.md) az utasításokat.

A második kódblokk az egérmutató stílusát egy mutatóra frissíti. Egy felugró objektumot hoz [létre.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) Az utasításokért [megjelenik egy felugró ablak hozzáadása a térképen.](./map-add-popup.md)

A harmadik kódblokk eseményfigyelőt ad hozzá az egérkattintásokhoz. Egy kattintással a [Fetch API-t](https://fetch.spec.whatwg.org/) használja az [Azure Maps fordított címkeresési API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) lekérdezéséhez a kattintott koordináták címéhez. A sikeres válasz érdekében összegyűjti a kattintott hely címét. Ez határozza meg a felugró tartalom és a pozíció segítségével [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) funkció a felugró osztály.

A kurzor módosítása, az előugró objektum és a kattintási esemény mind a térkép [betöltési eseményfigyelőjében](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)jön létre. Ez a kódstruktúra biztosítja, hogy a térkép teljesen betöltődjön a koordináta-információk beolvasása előtt.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Gyakorlati tanácsok a keresési szolgáltatás használatához](how-to-use-best-practices-for-search.md)

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Felugró ablak](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

A teljes kódpéldákat az alábbi cikkekben talál:

> [!div class="nextstepaction"]
> [Útvonal megjelenítése A-ból B-be](./map-route.md)

> [!div class="nextstepaction"]
> [Forgalom megjelenítése](./map-show-traffic.md)
