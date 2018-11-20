---
title: Gyakori kérdések (GYIK) – a Bing Web Search API
titleSuffix: Azure Cognitive Services
description: A Bing Web Search API kapcsolatos gyakori kérdésekre adott válaszok.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: troubleshooting
ms.date: 10/06/2017
ms.author: aahi
ms.openlocfilehash: dbff161f96949100703451a64875079288574014
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52162422"
---
# <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

 Kapcsolatos fogalmakat, a kód és a forgatókönyvek a Bing Web Search API a Microsoft Cognitive Services, Azure-beli kapcsolatos gyakori kérdésekre adott válaszok.

## <a name="response-headers-in-javascript"></a>A JavaScript válaszfejlécek

Előfordulhat, hogy a következő fejléceket a Bing Web Search API érkező válaszokat.

|||
|-|-|
|`X-MSEdge-ClientID`|A Bing a felhasználó rendelkezik hozzárendelt egyedi azonosítója|
|`BingAPIs-Market`|A piacon, amellyel a kérés teljesítéséhez|
|`BingAPIs-TraceId`|A Bing API-kiszolgálón (a támogatási) a kérés a naplóbejegyzés|

Különösen fontos megőrizni az ügyfél-Azonosítót, és küldje vissza a későbbi kérelmeket. Ha így tesz, a Keresés a környezetben, a keresési eredmények rangsorolása korábbi, és egységes felhasználói élmény is biztosítanak.

Azonban amikor a Bing Web Search API hívása JavaScript, a böngésző beépített biztonsági funkciókat (CORS) előfordulhat, hogy megakadályozzák a fér hozzá a következő fejlécek értékeit.

A fejlécek eléréséhez, hogy a Bing Web Search API-kérelem CORS proxyn keresztül. Az ilyen proxyk válasza rendelkezik egy `Access-Control-Expose-Headers` fejléccel, amely engedélyezési listára teszi a válaszfejléceket, és elérhetővé teszi őket a JavaScript számára.

Egyszerű, hogy a CORS-proxy telepítéséhez a [oktatóanyag alkalmazása](tutorial-bing-web-search-single-page-app.md) eléréséhez a választható ügyféltanúsítvány-fejléceket. Első lépésként [telepítse a Node.js-t](https://nodejs.org/en/download/), ha még nem tette meg. Majd adja meg a következő parancsot a parancssorba.

    npm install -g cors-proxy-server

Ezután módosítsa a Bing Web Search API-végpont lévő a HTML-fájl:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Végül indítsa el a CORS-proxyt a következő paranccsal:

    cors-proxy-server

Ne zárja be a parancsablakot, amíg használja az oktatóalkalmazást; az ablak bezárása leállítja a proxyt. A bővíthető HTTP-fejlécek szakaszában, a keresési eredmények alatt, most már az `X-MSEdge-ClientID` fejléc is megjelenik, és ellenőrizheti, hogy ugyanaz a fejléc szerepel-e minden kérésnél.

## <a name="response-headers-in-production"></a>Éles környezetben válaszfejlécek

Az előző válasz leírt CORS proxy megközelítés a fejlesztés, tesztelés és tanulás a megfelelő.

Éles környezetben azonban kell üzemeltetése egy kiszolgálóoldali parancsprogram, amely a Bing Web Search API weblapként ugyanabban a tartományban. Ez a szkript kell ténylegesen hajtsa végre az API-hívások kérésre a weblap JavaScript-alapú, és adja át az összes eredmény, beleértve a fejléceket, az ügyfélnek. A két erőforrás (oldal és parancsfájl) oszt meg egy eredeti adatforrást, mert a CORS nem jut, és a speciális fejlécek elérhetők a JavaScript a weblapon.

Ez a megközelítés is védelmet nyújt az API-kulcsot a nyilvános való kitettség mivel csak a kiszolgálóoldali parancsprogram szükség van. A szkript egy másik módszer segítségével ellenőrizze, hogy a kérés engedélyezett.

## <a name="next-steps"></a>További lépések

Egy hiányzó szolgáltatás vagy funkció a kérdése van? Kérő vagy szavaz, fontolja meg a [felhasználói visszajelzési webhelyen](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Lásd még

 [A stack Overflow: Cognitive Services](http://stackoverflow.com/questions/tagged/bing-api)
