---
title: Mi az a Bing Image Search API?
titleSuffix: Azure Cognitive Services
description: A Bing Image Search API segítségével használhatja a Bing kognitív képkeresési képességeit az alkalmazásában. A felhasználói lekérdezések az API-n keresztüli elküldésével releváns és jó minőségű képeket kérhet le és jeleníthet meg a Bing Imageshez hasonlóan.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 71e8e363a02e08a099d9051c7f851e11bbd9b80b
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467281"
---
# <a name="what-is-the-bing-image-search-api"></a>Mi az a Bing Image Search API?

A Bing Image Search API segítségével használhatja a Bing kognitív képkeresési képességeit az alkalmazásában. A felhasználói lekérdezések az API-n keresztüli elküldésével releváns és jó minőségű képeket kérhet le és jeleníthet meg a [Bing Imageshez](https://www.bing.com/images) hasonlóan.

Ne feledje, hogy a Bing Image Search API csak képtalálatokat hoz fel. A webes tartalmak egyéb típusainak kereséséhez használja a [Bing Web Search API-t](../bing-web-search/search-the-web.md), a [Video Search API-t](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search) és a [News Search API-t](https://review.docs.microsoft.com/azure/cognitive-services/bing-news-search).

## <a name="bing-image-search-features"></a>A Bing Image Search funkciói

Ugyan a Bing Image Search elsősorban releváns képeket keres és ad vissza a keresési lekérdezésekben, a szolgáltatás emellett további funkciókat is biztosít a képek intelligens és fókuszált webes lekéréséhez.


| Szolgáltatás                                                                                                                                                                                 | Leírás                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Keresési kifejezések valós idejű felkínálása](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) | A [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) a begépelés során javaslatokat tesz a keresési kifejezésekre, így könnyebbé teszi az alkalmazások használatát. |
| [Képtalálatok szűrése és korlátozása](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#filtering-images)                       | A Bing által visszaadott képek szűrése a lekérdezési paraméterek szerkesztésével.                                                                                                       |
| [Miniatűrök körülvágása, átméretezése és megjelenítése](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/resize-and-crop-thumbnails)                                                | A Bing Image Search által visszaadott képek előnézeti miniatűrjeinek szerkesztése és megjelenítése.                                                                                      |
| [Felhasználói keresési lekérdezések szegmentálása és kiterjesztése](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries#pivoting-the-query)               | A keresési képességek kiterjesztése a Bing által javasolt keresési kifejezések a lekérdezésekbe történő belefoglalásával és megjelenítésével.                                                                    |
| [Népszerű képek lekérése](https://review.docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)                                                                     | Népszerű képek testre szabott keresése a világ minden tájáról.                                                                                                          |

## <a name="workflow"></a>Munkafolyamat

A Bing Image Search API egy RESTful-webszolgáltatás, így bármely olyan programnyelvből meghívható, amely képes HTTP-lekérdezéseket kezelni és JSON formátumot elemezni. A szolgáltatás a [REST API](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?) vagy az [SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) segítségével használható.

1. Hozzon létre egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Küldjön egy kérést az API-ra egy érvényes [keresési lekérdezéssel](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries).
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="next-steps"></a>További lépések

Először próbálja ki a Bing Image Search API [interaktív bemutatóját](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/).
Ez bemutatja, hogyan lehet gyorsan testre szabni a keresési lekérdezéseket és felkutatni a webes képeket.

Amikor kész meghívni az API-t, hozzon létre egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Az első API-kérés gyors létrehozásához sajátítsa el a következőket:

* [Keresési lekérdezések küldése a Bingre](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp) a REST API használatával, illetve
* a Bing által visszaadott képek [kérése és szűrése](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) az SDK használatával.

## <a name="see-also"></a>Lásd még

* A [Bing Image Search API 7-es verziójának](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) referenciáit bemutató szakasz a képalapú találatok lekérdezéséhez használható végpontokkal, fejlécekkel, API-válaszokkal és lekérdezési paraméterekkel kapcsolatos információkat és definíciókat tartalmazza.

* A [Bing használati és megjelenítési követelményei](./useanddisplayrequirements.md) a Bing Search API-k használatával kapott tartalmak és információk elfogadható használatát határozzák meg.

* [A képek a Bing Image Search API használatával a webről való lekérését](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) ismertető témakör a weben található képek keresését és lekérését mutatja be.

* A [keresési lekérdezések küldését és használatát](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) ismertető témakör a keresési lekérdezések összeállítását, testreszabását és szegmentálását írja le.
