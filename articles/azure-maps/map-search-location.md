---
title: Keresés eredményeinek megjelenítése térképeken | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan hajthat végre keresési kéréseket Microsoft Azure Maps web SDK használatával, és hogyan jelenítheti meg az eredményeket a térképen.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7123bfda0baa3054dbed336dc4bed8958680cb62
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910924"
---
# <a name="show-search-results-on-the-map"></a>Keresési eredmények megjelenítése a térképen

Ez a cikk bemutatja, hogyan keresheti meg az érdeklődési helyet, és hogyan jelenítheti meg a keresési eredményeket a térképen.

Az érdeklődési hely két módon kereshető. Az egyik módszer egy szolgáltatási modul használata a keresési kérelem elvégzéséhez. A másik lehetőség, hogy keresési kérést készítsen [Azure Maps fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) -t a [Fetch API](https://fetch.spec.whatwg.org/)-n keresztül. Az alábbiakban mindkét módszert ismertetjük.

## <a name="make-a-search-request-via-service-module"></a>Keresési kérelem küldése a Service Module használatával

<iframe height='500' scrolling='no' title='Keresési eredmények megjelenítése térképeken (szolgáltatási modul)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Az <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti, hogy a tollon <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>megjelenjenek-e a keresési eredmények a térképeken (szolgáltatási modul)</a> .
</iframe>

A fenti kódban a kód első blokkja létrehozza a Térkép objektumot, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat kihasználása érdekében. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkja létrehoz egy `TokenCredential` a hozzáférési jogkivonattal Azure Maps HTTP-kérések hitelesítéséhez. Ezután átadja a `TokenCredential`nak `atlas.service.MapsURL.newPipeline()` és létrehoz egy [folyamat](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) -példányt. A `searchURL` Azure Maps [keresési](https://docs.microsoft.com/rest/api/maps/search) műveletekhez tartozó URL-címet jelöli.

A kód harmadik blokkja létrehoz egy adatforrás-objektumot az [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály használatával, és hozzáadja a keresési eredményeket. A [szimbólum-réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) szöveggel vagy ikonokkal jeleníti meg a pont-alapú adatforrásokat az [adatforrásban](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) szimbólumként a térképen.  A rendszer létrehoz egy szimbólum réteget, és hozzáadja az adatforrást a szimbólum réteghez, amelyet aztán hozzáad a térképhez.

A negyedik kódú blokk a [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) metódust használja a [szolgáltatási modulban](how-to-use-services-module.md). Lehetővé teszi, hogy a [keresési fuzzy REST API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) -n keresztül ingyenes szöveges keresést végezzen, hogy megkeresse az érdekes helyet. A keresés a fuzzy API-val a fuzzy bemenetek tetszőleges kombinációját kezelhetik. Ezután a válaszból kinyert egy GeoJSON-gyűjteményt, amely a `geojson.getFeatures()` metódussal lett kibontva, és hozzáadva az adatforráshoz, amely automatikusan az ábrán megjelenített adatokkal jeleníti meg a térképet.

A kód utolsó blokkja a Térkép [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonságának használatával módosítja a térképhez tartozó kamera-határokat.

A keresési kérelem, az adatforrás és a Symbol réteg, valamint a kamera határai jönnek létre és állíthatók be a Térkép kész [esemény-figyelőn](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) belül, hogy az eredmények megjelenjenek, miután a Térkép betöltődik.


## <a name="make-a-search-request-via-fetch-api"></a>Keresési kérelem készítése a fetch API használatával

<iframe height='500' scrolling='no' title='Keresés eredményeinek megjelenítése térképeken' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollak <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>keresési eredményeinek megjelenítése</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódban a kód első blokkja létrehozza a Térkép objektumot, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat kihasználása érdekében. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkja egy URL-címet hoz létre, amely egy keresési kérést tesz elérhetővé. Emellett két tömböt hoz létre a határértékek és a PIN-kódok tárolására a keresési eredményekhez.

A kód harmadik blokkja a [beolvasás API](https://fetch.spec.whatwg.org/) -val küld egy kérést, hogy [Azure Maps fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) -t, hogy megkeresse az érdekes pontokat. A fuzzy keresési API a fuzzy bemenetek tetszőleges kombinációját képes kezelni. Ezután kezeli és elemzi a keresési választ, és hozzáadja az eredmény-PIN-eket a searchPins tömbhöz.

A kód negyedik blokkja egy adatforrás-objektumot hoz létre az [adatforrás osztály használatával](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) , és hozzáadja a keresési eredményeket. A [szimbólum-réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) szöveggel vagy ikonokkal jeleníti meg a pont-alapú adatforrásokat az [adatforrásban](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) szimbólumként a térképen. A rendszer létrehoz egy szimbólum réteget, és hozzáadja az adatforrást a szimbólum réteghez, amelyet aztán hozzáad a térképhez.

A kód utolsó blokkja létrehoz egy [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) objektumot az eredmények tömbje alapján, majd a Térkép [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)használatával módosítja a térképhez tartozó kamera-határokat. Ezután megjeleníti az eredmény PIN-kód.

A keresési kérelem, az adatforrás és a Symbol réteg, valamint a kamera határai a Térkép [esemény-figyelőn](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) belül vannak beállítva, hogy az eredmények megjelenjenek, miután a Térkép teljes mértékben betöltődik.

## <a name="next-steps"></a>Következő lépések

További információ a **fuzzy keresésről**:

> [!div class="nextstepaction"]
> [Azure Maps fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

A következő cikkekben talál részletes példákat:

> [!div class="nextstepaction"]
> [Adatok lekérése egy koordinátaből](./map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Irányok megjelenítése a-tól B-be](./map-route.md)
