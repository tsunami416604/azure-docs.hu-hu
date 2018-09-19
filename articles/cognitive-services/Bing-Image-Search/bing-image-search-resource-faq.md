---
title: Gyakori kérdések (GYIK) – a Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Kapcsolatos fogalmakat, a kód és a forgatókönyvek a Bing Image Search API kapcsolatos gyakori kérdésekre adott válaszok.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: troubleshooting
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: ea170f4751952288c7894cab9c5acda2bf443043
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295497"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>A Bing Image Search API kapcsolatos gyakori kérdések (GYIK)

Kapcsolatos fogalmakat, a kód és a forgatókönyvek a Bing Image Search API a Microsoft Cognitive Services, Azure-beli kapcsolatos gyakori kérdésekre adott válaszok.

## <a name="response-headers-in-javascript"></a>A JavaScript válaszfejlécek

Előfordulhat, hogy a következő fejléceket a Bing Image Search API érkező válaszokat.

|||
|-|-|
|`X-MSEdge-ClientID`|A Bing a felhasználó rendelkezik hozzárendelt egyedi azonosítója|
|`BingAPIs-Market`|A piacon, amellyel a kérés teljesítéséhez|
|`BingAPIs-TraceId`|A Bing API-kiszolgálón (a támogatási) a kérés a naplóbejegyzés|

Különösen fontos megőrizni az ügyfél-Azonosítót, és küldje vissza a későbbi kérelmeket. Ha így tesz, a Keresés a környezetben, a keresési eredmények rangsorolása korábbi, és egységes felhasználói élmény is biztosítanak.

Azonban amikor a Bing Image Search API hívása JavaScript, a böngésző beépített biztonsági funkciókat (CORS) előfordulhat, hogy megakadályozzák a fér hozzá a következő fejlécek értékeit.

A fejlécek eléréséhez, hogy a Bing Image Search API-kérelem CORS proxyn keresztül. Ilyen proxyn válasza rendelkezik egy `Access-Control-Expose-Headers` fejléc adott listáinak válaszfejlécek, és elérhetővé teszi azokat a JavaScript.

Egyszerű, hogy a CORS-proxy telepítéséhez a [oktatóanyag alkalmazása](tutorial-bing-image-search-single-page-app.md) eléréséhez a választható ügyféltanúsítvány-fejléceket. Ha még nem rendelkezik, először [a Node.js telepítéséhez](https://nodejs.org/en/download/). Majd adja meg a következő parancsot a parancssorba.

    npm install -g cors-proxy-server

Ezután módosítsa a Bing Image Search API-végpont lévő a HTML-fájl:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Végezetül indítsa el a CORS-proxy a következő parancsot:

    cors-proxy-server

Hagyja meg a parancssori ablakban nyissa meg az oktatóanyag alkalmazása; használata közben az ablak bezárása leállítja a proxy. A bővíthető HTTP-fejlécek szakaszban alább a keresési eredmények között, most már megtekintheti a `X-MSEdge-ClientID` fejléc (többek között), és ellenőrizze, hogy minden egyes kérés esetében azonos.

## <a name="response-headers-in-production"></a>Éles környezetben válaszfejlécek

Az előző válasz leírt CORS proxy megközelítés a fejlesztés, tesztelés és tanulás a megfelelő.

Éles környezetben azonban kell üzemeltetése egy kiszolgálóoldali parancsprogram, amely a Bing Web Search API weblapként ugyanabban a tartományban. Ez a szkript kell ténylegesen hajtsa végre az API-hívások kérésre a weblap JavaScript-alapú, és adja át az összes eredmény, beleértve a fejléceket, az ügyfélnek. A két erőforrás (oldal és parancsfájl) oszt meg egy eredeti adatforrást, mert a CORS nem jut, és a speciális fejlécek acessible a JavaScript a weblapon.

Ez a megközelítés is védelmet nyújt az API-kulcsot a nyilvános való kitettség mivel csak a kiszolgálóoldali parancsprogram szükség van. A szkript egy másik módszerrel (például a odkazující HTTP) segítségével ellenőrizze, hogy a kérés engedélyezett.

## <a name="next-steps"></a>További lépések

Egy hiányzó szolgáltatás vagy funkció a kérdése van? Kérő vagy szavaz, fontolja meg a [felhasználói visszajelzési webhelyen](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Lásd még

 [A stack Overflow: Cognitive Services](http://stackoverflow.com/questions/tagged/bing-api)
