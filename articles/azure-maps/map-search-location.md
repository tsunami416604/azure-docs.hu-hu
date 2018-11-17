---
title: Az Azure Maps a keresési eredmények megjelenítése |} A Microsoft Docs
description: Hogyan hajthat végre keresést az Azure Maps-kérést, majd az eredményeket megjeleníteni a Javascript térképen
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cf27864d691fe2fe13c9483348fb2abed121874d
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51713503"
---
# <a name="show-search-results-on-the-map"></a>A térképen a keresési eredmények megjelenítése

Ez a cikk bemutatja, hogyan keresse meg a lényeges helyét, és a keresési eredmények megjelenítése a térképen.

Két módon hely hasznos helyek keresése. Egyik módja egy modul segítségével egy keresési kérelmet. A másik lehetőség, hogy egy keresési kérelmet, egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) való [Azure Maps intelligens keresés API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). Mindkét irányban keresztszűréssel az alábbiak ismertetik.

## <a name="make-a-search-request-via-service-module"></a>Indítson egy keresési szolgáltatás modulon keresztül

<iframe height='500' scrolling='no' title='Keresési eredmény megjelenítése térképen (szolgáltatás modul)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>találatok megjelenítése a térképen (Szolgáltatásmodul)</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot egy térkép-objektumot hoz létre, és az ügyfélszolgáltatás inicializálja. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot használja a [Azure Maps intelligens keresési API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) hasznos helyekre vezető útvonalak keresése. Intelligens keresést API intelligens bemenetek bármilyen kombinációját képes kezelni. Az intelligens keresés szolgáltatástól kapott válasz majd szűrőtulajdonság GeoJSON formátumú használatával a [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest#geojsonsearchresponse) metódust. A PIN-kódok ezután hozzáadódnak a hasznos helyekhez a térképen megjeleníthető leképezés.

A legutóbbi kódblokkot segítségével állítja be a kamera esik, a térkép a térkép [setCameraBounds](/javascript/api/azure-maps-control/atlas.map#setcamerabounds-cameraboundsoptions-) tulajdonság.

## <a name="make-a-search-request-via-xmlhttprequest"></a>Keresés keresztül XMLHttpRequest kérést

<iframe height='500' scrolling='no' title='Keresési eredmény megjelenítése térképen' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show keresési eredmények a térképen</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokk keresési eredmények réteget ad hozzá a térképen. A keresési eredmények réteg megjelenik a keresési eredmények között, mint a térképen rögzíti. PIN-kódok kerülnek használatával [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).

A harmadik kódblokkot küld egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) való [Azure Maps intelligens keresés API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) a pont hasznos hely kereséséhez. Intelligens keresést API intelligens bemenetek bármilyen kombinációját képes kezelni.

A legutóbbi kódblokkot elemzi a választ, és beállítja a a térkép segítségével állítja be a térkép kamera határán [setCameraBounds](/javascript/api/azure-maps-control/atlas.map#setcamerabounds-cameraboundsoptions-) az eredmény PIN-kód megjelenítéséhez.

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
