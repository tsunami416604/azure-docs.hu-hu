---
title: Az Azure Maps koordináta információkat jelenít meg |} A Microsoft Docs
description: Hogyan egy címet kapcsolatos információk megjelenítéséhez a térképen, amikor a felhasználó kiválaszt egy koordinátája
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 50f906a9d8a0dc19f5eb47bef4cb68f4703f020f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59256056"
---
# <a name="get-information-from-a-coordinate"></a>Koordináta információinak lekérése

Ez a cikk bemutatja, hogyan javíthatja egy fordított cím keresését, amely megjeleníti a cím való kattintás előugró hely.

Győződjön meg arról, fordított cím keresés két módon lehet. Egyik módszer, a lekérdezés a [Azure Maps fordított cím Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) egy modul segítségével. A másik lehetőség, hogy az a [Fetch API](https://fetch.spec.whatwg.org/) , kérheti a [Azure Maps fordított cím Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) található cím. Mindkét irányban keresztszűréssel alatt vannak felügyelt.

## <a name="make-a-reverse-search-request-via-service-module"></a>Indítson egy fordított keresési szolgáltatás modulon keresztül

<iframe height='500' scrolling='no' title='(Szolgáltatásmodul) koordináta információinak lekérése' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>(Szolgáltatásmodul) koordináta információinak lekérése</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre, és beállítja a hitelesítési mechanizmust, az előfizetési kulcsot használják. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot létrehoz egy `SubscriptionKeyCredentialPolicy` az előfizetési kulcsot az Azure Maps HTTP-kérések hitelesítéséhez. A `atlas.service.MapsURL.newPipeline()` fogadja a a `SubscriptionKeyCredential` házirendet, és létrehoz egy [folyamat](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) példány. A `searchURL` egy URL-címet jelöli az Azure Maps [keresési](https://docs.microsoft.com/rest/api/maps/search) műveleteket.

A harmadik kódblokkot mutató egérmutatót stílusát frissíti, és létrehoz egy [előugró](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objektum. Látható [egy előugró ablak hozzáadása a térképen](./map-add-popup.md) útmutatást.

A negyedik kódblokkot hozzáad egy kattintásra [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Amikor elindul, egy keresési lekérdezést hoz a koordináták a való kattintás pont. Ezután használja a szolgáltatás-modulja [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) lekérdezési metódust a [első Search cím fordított API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) a koordináták a cím. A válaszból GeoJSON funkció gyűjtemény majd ki kell olvasni használatával a `geojson.getFeatures()` metódust.

Az ötödik kódblokkot beállítja a helyi menü HTML-tartalom megjelenítése a válasz koordináta pozice való kattintás.

A módosítás a kurzor, egy előugró objektum és a kattintás esemény összes jönnek létre a térkép [terhelés eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) biztosításához térkép terhelések teljes előtt a koordináták adatait.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Indítson egy fordított keresési Fetch API-n keresztül

Kattintson az adott hely fetch használatával egy fordított geocode-kérés a térképen.

<iframe height='500' scrolling='no' title='Koordináta információinak lekérése' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>koordináta információinak lekérése</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre, és beállítja a hitelesítési mechanizmust, az előfizetési kulcsot használják. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot mutató frissíti az egérmutatót a stílus és [előugró](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objektum. Látható [egy előugró ablak hozzáadása a térképen](./map-add-popup.md) útmutatást.

A harmadik kódblokkot hozzáad egy Eseményfigyelőt, a kattintások. A kattintásra esetén használja a [Fetch API](https://fetch.spec.whatwg.org/) lekérdezéshez a [Azure Maps fordított cím Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) való kattintás koordinátái címét. Sikeres válasz, a cím való kattintás helyéhez gyűjti, és meghatározza az előugró ablak tartalmát és pozícióját keresztül [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) függvény az előugró osztály.

A módosítás a kurzor, egy előugró objektum és a kattintás esemény összes jönnek létre a térkép [terhelés eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) biztosításához térkép terhelések teljes előtt a koordináták adatait.

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

A teljes hitelesítésikód-példák a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Útvonal megjelenítése a-b](./map-route.md)

> [!div class="nextstepaction"]
> [Forgalom megjelenítése](./map-show-traffic.md)
