---
title: Gyakori kérdések (GYAKORI KÉRDÉSEK) – Bing Képkeresési API
titleSuffix: Azure Cognitive Services
description: Válaszok a Bing Image Search API-val kapcsolatos fogalmakkal, kódokkal és forgatókönyvekkel kapcsolatos gyakori kérdésekre.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 6841e573446103466e2719797da9e4161b70b5a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881694"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Gyakori kérdések a Bing Képkereső API-val kapcsolatban

Válaszok a Bing Image Search API-val kapcsolatos fogalmakkal, kódokkal és forgatókönyvekkel kapcsolatos gyakori kérdésekre az Azure-ban.

## <a name="response-headers-in-javascript"></a>Válaszfejlécek JavaScriptben

A bingképkereső API válaszaiban a következő fejlécek fordulhatnak elő.

| `Attribute`         | `Description` |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |A Bing által a felhasználóhoz rendelt egyedi azonosító |
| `BingAPIs-Market`   |A kérelem teljesítéséhez használt piac |
| `BingAPIs-TraceId`  |A bing API-kiszolgáló naplóbejegyzése ehhez a kéréshez (támogatás) |

Különösen fontos, hogy továbbra is az ügyfél-azonosítót, és adja vissza a későbbi kérésekkel. Ha ezt teszi, a keresés a keresési eredmények rangsorolásában a múltbeli környezetet fogja használni, és egységes felhasználói élményt biztosít.

Ha azonban javascriptből hívja meg a Bing Image Search API-t, a böngésző beépített biztonsági szolgáltatásai (CORS) megakadályozhatják a fejlécek értékeinek elérését.

A fejlécek eléréséhez a Bing Image Search API-kérelmet egy CORS-proxyn keresztül teheti meg. Az ilyen proxyk válasza rendelkezik egy `Access-Control-Expose-Headers` fejléccel, amely engedélyezési listára teszi a válaszfejléceket, és elérhetővé teszi őket a JavaScript számára.

Könnyen telepíthet CORS proxyt, hogy [oktatóalkalmazásunk](tutorial-bing-image-search-single-page-app.md) hozzáférhessen az opcionális ügyfélfejlécekhez. Első lépésként [telepítse a Node.js-t](https://nodejs.org/en/download/), ha még nem tette meg. Ezután írja be a következő parancsot a parancssorba.

    npm install -g cors-proxy-server

Ezután módosítsa a Bing Image Search API-végpontot a HTML-fájlban a következőre:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Végül indítsa el a CORS-proxyt a következő paranccsal:

    cors-proxy-server

Ne zárja be a parancsablakot, amíg használja az oktatóalkalmazást; az ablak bezárása leállítja a proxyt. A bővíthető HTTP-fejlécek szakaszában, a keresési eredmények alatt, most már az `X-MSEdge-ClientID` fejléc is megjelenik, és ellenőrizheti, hogy ugyanaz a fejléc szerepel-e minden kérésnél.

## <a name="response-headers-in-production"></a>Válaszfejlécek éles környezetben

Az előző válaszban leírt CORS proxy megközelítés megfelelő a fejlesztéshez, teszteléshez és tanuláshoz.

Éles környezetben azonban kiszolgálóoldali parancsfájlt kell üzemeltetnie ugyanazon a tartományon, mint a Bing Web Search API-t használó weblap. Ez a szkript ténylegesen nem az API-hívások kérésére a weblap JavaScript és adja át az összes eredményt, beleértve a fejlécek, vissza az ügyfélnek. Mivel a két erőforrás (oldal és parancsfájl) egy eredeten osztozik, a CORS nem kerül szóba, és a speciális fejlécek a weblapon található JavaScript számára is elérhetők.

Ez a megközelítés is védi az API-kulcsot a nyilvános, mivel csak a kiszolgálóoldali szkript et kell. A parancsfájl más módszert (például a HTTP-hivatkozót) is használhat a kérelem engedélyezésének biztosításához.

## <a name="next-steps"></a>További lépések

A kérdése hiányzik egy hiányzó funkcióról vagy funkcióról? Fontolja meg a user voice [weboldalunkon](https://cognitive.uservoice.com/forums/555907-bing-search)történő igénylést vagy szavazást.

## <a name="see-also"></a>Lásd még

 [Verem túlcsordulás: Kognitív szolgáltatások](https://stackoverflow.com/questions/tagged/bing-api)
