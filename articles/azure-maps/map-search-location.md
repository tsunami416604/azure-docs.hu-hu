---
title: Az Azure Maps a keresési eredmények megjelenítése |} A Microsoft Docs
description: Hogyan hajthat végre keresést az Azure Maps-kérést, majd az eredményeket megjeleníteni a Javascript térképen
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8ae6c8a20a05df723d3f6b394e0639f218896a85
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57845137"
---
# <a name="show-search-results-on-the-map"></a>A térképen a keresési eredmények megjelenítése

Ez a cikk bemutatja, hogyan keresse meg a lényeges helyét, és a keresési eredmények megjelenítése a térképen.

Két módon hely hasznos helyek keresése. Egyik módja egy modul segítségével egy keresési kérelmet. A másik lehetőség, hogy a keresési kérheti [Azure Maps intelligens keresés API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) keresztül a [Fetch API](https://fetch.spec.whatwg.org/). Mindkét irányban keresztszűréssel az alábbiak ismertetik.

## <a name="make-a-search-request-via-service-module"></a>Indítson egy keresési szolgáltatás modulon keresztül

<iframe height='500' scrolling='no' title='Keresési eredmény megjelenítése térképen (szolgáltatás modul)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>találatok megjelenítése a térképen (Szolgáltatásmodul)</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre, és beállítja a hitelesítési mechanizmust, az előfizetési kulcsot használják. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot létrehoz egy **SubscriptionKeyCredentialPolicy** az előfizetési kulcsot az Azure Maps HTTP-kérések hitelesítéséhez. A **atlas.service.MapsURL.newPipeline()** fogadja a a **SubscriptionKeyCredential** házirendet, és létrehoz egy [folyamat](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) példány. A **searchURL** egy URL-címet jelöli az Azure Maps [keresési](https://docs.microsoft.com/rest/api/maps/search) műveleteket.

A harmadik kódblokkot létrehoz egy objektum használhatja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztályt, és a keresési eredmények hozzá. A [szimbólum réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) használja a szöveges vagy ikonjai csomagolni szoftverfrissítésipont-alapú adatok megjelenítése a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) szimbólumra a térképen.  Egy szimbólum réteg majd jön létre, és az adatforrás hozzáadásakor a szimbólum réteghez, majd a térkép felvett.

A negyedik kódblokk használja a [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) metódus az a [modul](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=2). Lehetővé teszi a szabad formátumú szöveges keresés keresztül végezhető el a [keresés intelligens lekérése rest API-t](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) hasznos helyekre vezető útvonalak keresése. Get-keresés intelligens API intelligens bemenetek bármilyen kombinációját képes kezelni. A válaszból GeoJSON funkció gyűjtemény majd ki kell olvasni használatával a **geojson.getFeatures()** metódust, és hozzáadja az adatforráshoz, ami az adatok a térképen a szimbólum réteg keresztül rendereli a rendszer automatikusan eredményez.

A legutóbbi kódblokkot segítségével állítja be a kamera esik, a térkép a térkép [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonság.

A keresési kérelmek, az adatforráshoz, és szimbólum réteg és a kamera határokon jönnek létre, és a set belül a térkép készen áll a [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) annak érdekében, hogy az eredmények jelennek meg a térkép teljes betölti követően.


## <a name="make-a-search-request-via-fetch-api"></a>Győződjön meg arról, egy keresési kérelmet Fetch API-n keresztül

<iframe height='500' scrolling='no' title='Keresési eredmény megjelenítése térképen' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show keresési eredmények a térképen</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre, és beállítja a hitelesítési mechanizmust, az előfizetési kulcsot használják. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot létrehoz egy URL-címet a keresési kérheti. Létrehoz két tömbök esik, és a PIN-kódok a keresési eredmények tárolására is.

A harmadik kódblokkot használja a [Fetch API](https://fetch.spec.whatwg.org/) kérheti a [Azure Maps intelligens keresés API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) hasznos helyekhez kereséséhez. Az intelligens keresés API intelligens bemenetek bármilyen kombinációját képes kezelni. Majd kezeli és elemzi a keresési választ, és az eredmény PIN-kód hozzáadja a searchPins tömb.

A negyedik kódblokkot létrehoz egy objektum használhatja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztályt, és a keresési eredmények hozzá. A [szimbólum réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) használja a szöveges vagy ikonjai csomagolni szoftverfrissítésipont-alapú adatok megjelenítése a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) szimbólumra a térképen. Egy szimbólum réteg majd jön létre, és az adatforrás hozzáadásakor a szimbólum réteghez, majd a térkép felvett.

A legutóbbi kódblokkot létrehoz egy [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) objektumba az eredmények a tömbben, és majd segítségével állítja be a kamera esik, a térkép a térkép [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Ezután az eredmények PIN-kód jelenik meg.

A térkép belül vannak beállítva a keresési kérelmet, az adatforráshoz, és szimbólum réteg és a kamera határokon [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) annak érdekében, hogy az eredmények jelennek meg a térkép teljes betölti követően.

## <a name="next-steps"></a>További lépések

Tudjon meg többet **intelligens keresés**:

> [!div class="nextstepaction"]
> [Azure Maps-intelligens keresési API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

A teljes hitelesítésikód-példák a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Koordináta információinak lekérése](./map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Útvonal megjelenítése a-b](./map-route.md)
