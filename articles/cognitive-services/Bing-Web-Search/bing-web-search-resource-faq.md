---
title: Bing az interneten rákeresve API Azure kapcsolatos gyakori kérdések (GYIK) |} Microsoft Docs
description: Válaszok Microsoft kognitív szolgáltatások Bing webes keresés API kapcsolatos gyakori kérdéseket az Azure-on.
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: 321f571c48f2231d1ced43848cdefd17adaa1a08
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347066"
---
# <a name="frequently-asked-questions-faq-about-bing-web-search-api-cognitive-services"></a>Bing webes API-keresés (kognitív szolgáltatások) kapcsolatos gyakori kérdések (GYIK)
 
 Válaszok a fogalmakat, kód és a Microsoft Azure kognitív Services Web a Bing keresési API kapcsolatos forgatókönyvek gyakran feltett kérdésekre.

## <a name="response-headers-in-javascript"></a>A JavaScript válaszfejlécek

Előfordulhat, hogy a következő fejlécek webes a Bing keresési API válaszát.

|||
|-|-|
|`X-MSEdge-ClientID`|Bing hozzá van rendelve a felhasználó egyedi azonosító|
|`BingAPIs-Market`|A piacon a kérés teljesítéséhez használt|
|`BingAPIs-TraceId`|A naplóbejegyzés a Bing API-kiszolgálón a kérelem (a támogatási)|

Különösen fontos megőrizni az ügyfél-Azonosítót, és visszaadja az ezt követő által. Ha ezt teszi, a keresési alapozzák a korábbi a környezetben, a keresési eredmények sorrendjét, és egységes felhasználói élményt biztosítson, is meg.

Azonban a webes Bing keresési API a JavaScript hívja, a böngésző beépített biztonsági szolgáltatásait (CORS) megakadályozhatja ezek a fejlécek értékének elérésekor.

Ahhoz, hogy hozzáférjenek a fejlécek, a Bing webes keresési API-kérelem a CORS-proxy használatával teheti meg. Ilyen proxy válaszát rendelkezik egy `Access-Control-Expose-Headers` fejléc adott whitelists válaszfejlécek és elérhetővé válnak a JavaScript nyelvvel.

Engedélyezi a CORS-proxy telepítéséhez egyszerűen a [oktatóanyag app](tutorial-bing-web-search-single-page-app.md) a választható ügyféltanúsítvány fejlécek eléréséhez. Ha még nem rendelkezik, először [telepítse a Node.js-](https://nodejs.org/en/download/). Adja meg a következő parancsot a parancssorba.

    npm install -g cors-proxy-server

Ezután módosítsa a Bing webes keresési API-végpont a HTML-fájlban:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Végezetül indítsa el a CORS-proxy a következő paranccsal:

    cors-proxy-server

A parancssori ablakban nyitva hagyja az oktatóanyag alkalmazás; használatakor az ablak bezárása leállítja a proxy. A bővíthető HTTP-fejlécek című szakaszt a keresési eredmények között, a most már megtekintheti a `X-MSEdge-ClientID` fejléc (többek között), és győződjön meg arról, hogy minden kérelem esetén.

## <a name="response-headers-in-production"></a>Éles környezetben válaszfejlécek

A CORS proxy megoldás az előző válasz ismertetett, megfelelő fejlesztési, tesztelési, és megismerését. 

Éles környezetben azonban kell működteti a weblap által használt webes a Bing keresési API megegyező tartományban található kiszolgálóoldali parancsfájl. Ezt a parancsfájlt a kell ténylegesen hajtsa végre a weblap JavaScript kérésre API hívást, és adja át minden eredmény, beleértve a fejlécek, az ügyfélnek. Mivel a két erőforrás (oldal és parancsfájl) egy eredeti adatforrást megosztásához CORS nem játékba és a speciális fejlécek acessible a JavaScript a weblap a. 

Ezt a módszert is védi az API-kulcs a lehetősége, hogy a nyilvános azóta csak a kiszolgálóoldali parancsfájl kell azt. A parancsfájl egy másik módszer segítségével győződjön meg arról, hogy a kérelem engedélyezett.

## <a name="next-steps"></a>További lépések

Egy hiányzó szolgáltatást és funkciót a kérdése van? A kért, vagy szavazott a [User Voice webhely](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Lásd még

 [Stack overflow webhelyre: Kognitív szolgáltatások](http://stackoverflow.com/questions/tagged/bing-api)