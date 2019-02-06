---
title: Az Azure Maps a keresési eredmények megjelenítése |} A Microsoft Docs
description: Hogyan hajthat végre keresést az Azure Maps-kérést, majd az eredményeket megjeleníteni a Javascript térképen
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c68b4bdffde5f987fe07d50d76fa83e7bdfa5235
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755042"
---
# <a name="show-search-results-on-the-map"></a>A térképen a keresési eredmények megjelenítése

Ez a cikk bemutatja, hogyan keresse meg a lényeges helyét, és a keresési eredmények megjelenítése a térképen.

Két módon hely hasznos helyek keresése. Egyik módja egy modul segítségével egy keresési kérelmet. A másik lehetőség, hogy egy keresési kérelmet, egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) való [Azure Maps intelligens keresés API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). Mindkét irányban keresztszűréssel az alábbiak ismertetik.

## <a name="make-a-search-request-via-service-module"></a>Indítson egy keresési szolgáltatás modulon keresztül

<iframe height='500' scrolling='no' title='Keresési eredmény megjelenítése térképen (szolgáltatás modul)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>találatok megjelenítése a térképen (Szolgáltatásmodul)</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kóddal az első kódblokkot egy térkép-objektumot hoz létre, és az ügyfélszolgáltatás inicializálja. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot használja a [getSearchFuzzy](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchfuzzy-string--searchgetsearchfuzzyoptionalparams-) metódus az a [modul](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1). Lehetővé teszi a szabad formátumú szöveges keresés keresztül végezhető el a [intelligens keresés rest API-t](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) hasznos helyekre vezető útvonalak keresése. Intelligens keresést API intelligens bemenetek bármilyen kombinációját képes kezelni. Az intelligens keresés szolgáltatástól kapott válasz majd szűrőtulajdonság GeoJSON formátumú használatával a [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest) metódust. 

A harmadik kódblokkot létrehoz egy objektum használhatja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztályt, és a keresési eredmények hozzá. A [szimbólum réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) használja a szöveges vagy ikonjai csomagolni szoftverfrissítésipont-alapú adatok megjelenítése a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) szimbólumra a térképen.  Egy szimbólum réteg majd jön létre, és az adatforrás hozzáadásakor a szimbólum réteghez, majd a térkép felvett.

A legutóbbi kódblokkot segítségével állítja be a kamera esik, a térkép a térkép [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonság.

A keresési kérelmet, az adatforráshoz, és szimbólum réteg és a kamera határokon létrehozott, és állítsa be a térkép belül [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) annak érdekében, hogy az eredmények jelennek meg a térkép teljes betölti követően.


## <a name="make-a-search-request-via-xmlhttprequest"></a>Keresés keresztül XMLHttpRequest kérést

<iframe height='500' scrolling='no' title='Keresési eredmény megjelenítése térképen' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show keresési eredmények a térképen</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot küld egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) való [Azure Maps intelligens keresés API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) a pont hasznos hely kereséséhez. Az intelligens keresés API intelligens bemenetek bármilyen kombinációját képes kezelni. 

A harmadik kódblokkot elemzi a keresési választ, és tárolja az eredményeket egy tömb határán kiszámításához. Ezután a keresési eredményeket adja vissza.

A negyedik kódblokkot létrehoz egy objektum használhatja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztályt, és a keresési eredmények hozzá. A [szimbólum réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) használja a szöveges vagy ikonjai csomagolni szoftverfrissítésipont-alapú adatok megjelenítése a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) szimbólumra a térképen. Egy szimbólum réteg majd jön létre, és az adatforrás hozzáadásakor a szimbólum réteghez, majd a térkép felvett.

A legutóbbi kódblokkot létrehoz egy [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) objektumba az eredmények a tömbben, és majd segítségével állítja be a kamera esik, a térkép a térkép [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Ezután az eredmények PIN-kód jelennek meg.

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
