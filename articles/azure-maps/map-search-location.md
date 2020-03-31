---
title: Keresési eredmények megjelenítése a térképen | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan hajthat végre keresési kérelmet a Microsoft Azure Maps Web SDK használatával, és hogyan jelenítheti meg az eredményeket a térképen.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e82a1daee381c7bad19c83fa735d0028bef2010e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371398"
---
# <a name="show-search-results-on-the-map"></a>Keresési eredmények megjelenítése a térképen

Ez a cikk bemutatja, hogyan kereshet érdekes helyet, és hogyan jelenítheti meg a keresési eredményeket a térképen.

Kétféleképpen kereshet egy érdekes helyet. Az egyik módja az, hogy egy szolgáltatás modult, hogy a keresési kérelmet. A másik módja az, hogy egy keresési kérelmet [az Azure Maps Fuzzy keresési API-t](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) a Fetch API.The other way is to make a search request to Azure Maps Fuzzy search API through the Fetch [API.](https://fetch.spec.whatwg.org/) Mindkét irányban az alábbiakban tárgyaljuk.

## <a name="make-a-search-request-via-service-module"></a>Keresési kérelem kérése a szolgáltatásmodulon keresztül

<iframe height='500' scrolling='no' title='Keresési eredmények megjelenítése térképen (szolgáltatásmodul)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen Show keresési eredményeket az<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) által a CodePen térképen <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>(service module)</a> című <a href='https://codepen.io'>témakörben.</a>
</iframe>

A fenti kódban az első blokk létrehoz egy térképobjektumot, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat használatára. Láthatjuk [hozzon létre egy térképet](./map-create.md) az utasításokat.

A második kódblokk `TokenCredential` létrehoz egy HTTP-kérelmek hitelesítéséhez az Azure Maps a hozzáférési jogkivonattal. Ezután átadja `TokenCredential` a `atlas.service.MapsURL.newPipeline()` továbbad egy folyamatpéldányt, és létrehoz egy [folyamatpéldányt.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) Az `searchURL` Azure Maps [Search-műveletek](https://docs.microsoft.com/rest/api/maps/search) URL-címét jelöli.

A harmadik kódblokk adatforrás-objektumot hoz létre a [DataSource-osztály](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) használatával, és keresési eredményeket ad hozzá. A [szimbólumréteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) szöveget vagy ikonokat használ az [Adatforrásban](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) lévő pontalapú adatok nak a térképen lévő szimbólumokként való megjelenítéséhez.  Ezután létrejön egy szimbólumréteg. Az adatforrás hozzáadódik a szimbólumréteghez, amelyet aztán hozzáad a térképhez.

A negyedik kódblokk a [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) metódust használja a [szervizmodulban.](how-to-use-services-module.md) Ez lehetővé teszi, hogy végre egy szabad formában szöveges keresés révén a [Get Search Fuzzy többi API-t](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) keresni érdekes hely. A Search Fuzzy API-hoz érkező kérések az intelligens bemenetek bármilyen kombinációját képesek kezelni. A válaszból származó GeoJSON-szolgáltatásgyűjteményt `geojson.getFeatures()` ezután kinyeri a metódus, és hozzáadja az adatforráshoz, ami automatikusan azt eredményezi, hogy az adatok a szimbólumrétegen keresztül jelennek meg a térképen.

Az utolsó kódblokk a Térkép [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonságával állítja be a térkép kamerahatárait.

A keresési kérelem, adatforrás, szimbólumréteg és kamerahatárok a térkép [eseményfigyelőjén](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) belül vannak. Biztosítani szeretnénk, hogy az eredmények a térkép teljes betöltése után jelenjenek meg.


## <a name="make-a-search-request-via-fetch-api"></a>Keresési kérelem kérése a Fetch API-n keresztül

<iframe height='500' scrolling='no' title='Keresési eredmények megjelenítése a térképen' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show keresési eredményeket az</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) térképén a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

A fenti kódban az első kódblokk egy térképobjektumot hoz létre. Beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat használatára. Láthatjuk [hozzon létre egy térképet](./map-create.md) az utasításokat.

A második kódblokk létrehoz egy URL-címet, amelyhez keresési kérelmet kell benyújtani. Emellett két tömböt hoz létre a keresési eredmények hez kötött és tűk tárolására.

A harmadik kódblokk a [Fetch API-t](https://fetch.spec.whatwg.org/)használja. A [Fetch API-t](https://fetch.spec.whatwg.org/) arra használják, hogy az [Azure Maps Fuzzy keresési API-t](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) az érdekes pontok kereséséhez. A Fuzzy keresési API képes kezelni bármilyen kombinációja fuzzy bemenetek. Ezután kezeli és elemzi a keresési választ, és hozzáadja az eredménycsapokat a searchPins tömbhöz.

A negyedik kódblokk adatforrás-objektumot hoz létre a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály használatával. A kódban keresési eredményeket adunk a forrásobjektumhoz. A [szimbólumréteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) szöveget vagy ikonokat használ az [Adatforrásban](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) lévő pontalapú adatok nak a térképen lévő szimbólumokként való megjelenítéséhez. Ezután létrejön egy szimbólumréteg. Az adatforrás hozzáadódik a szimbólumréteghez, amelyet aztán hozzáad a térképhez.

Az utolsó kódblokk [boundingbox objektumot](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) hoz létre. Használja a tömb az eredmények, majd beállítja a kamera határait a térkép segítségével a térkép [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Ezután rendereli az eredmény csapok.

A keresési kérelem, az adatforrás, a szimbólumréteg és a kamerahatárok a térkép [eseményfigyelőjén](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) belül vannak beállítva annak érdekében, hogy az eredmények a térkép teljes betöltése után jelenjenek meg.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Gyakorlati tanácsok a keresési szolgáltatás használatához](how-to-use-best-practices-for-search.md)

További információ a **Fuzzy Search-ről:**

> [!div class="nextstepaction"]
> [Az Azure Maps fuzzy keresési API-ja](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

A teljes kódpéldákat az alábbi cikkekben talál:

> [!div class="nextstepaction"]
> [Koordináta információinak lekérése](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Útvonal megjelenítése A-ból B-be](map-route.md)
