---
title: Mi az a Bing Image Search API?
titleSuffix: Azure Cognitive Services
description: A Bing Image Search API segítségével használhatja a Bing kognitív képkeresési képességeit az alkalmazásában. A felhasználói lekérdezések az API-n keresztüli elküldésével releváns és jó minőségű képeket kérhet le és jeleníthet meg a Bing Imageshez hasonlóan.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 02/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: fa1e2e6ac6e85c431a759d8eb1c22923e86e40d4
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237314"
---
# <a name="what-is-the-bing-image-search-api"></a>Mi az a Bing Image Search API?

A Bing Image Search API lehetővé teszi a Bing kép keresési lehetőségeket használhatja az alkalmazásban. Keresési lekérdezéseket küld az API-t, juthat magas minőségű képeket hasonló [bing.com/images](https://www.bing.com/images).

A Bing Image Search API a csak rendszerkép-keresési eredmények biztosít, miközben egyesítése, vagy használhatja az egyéb elérhető [Bing Search APIs](../bing-web-search/bing-api-comparison.md) számos különböző típusú tartalom keresése a weben.

## <a name="bing-image-search-features"></a>A Bing Image Search funkciói

| Szolgáltatás                                                                                                                                                                                 | Leírás                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Keresési kifejezések valós idejű felkínálása](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) | A [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) a begépelés során javaslatokat tesz a keresési kifejezésekre, így könnyebbé teszi az alkalmazások használatát. |
| [Képtalálatok szűrése és korlátozása](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images)                       | A Bing által visszaadott képek szűrése a lekérdezési paraméterek szerkesztésével.                                                                                                       |
| [Miniatűrök körülvágása, átméretezése és megjelenítése](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/resize-and-crop-thumbnails)                                                | A Bing Image Search által visszaadott képek előnézeti miniatűrjeinek szerkesztése és megjelenítése.                                                                                      |
| [Felhasználói keresési lekérdezések szegmentálása és kiterjesztése](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)               | A keresési képességek kiterjesztése a Bing által javasolt keresési kifejezések a lekérdezésekbe történő belefoglalásával és megjelenítésével.                                                                    |
| [Népszerű képek lekérése](https://review.docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)                                                                     | Népszerű képek testre szabott keresése a világ minden tájáról.                                                                                                          |

## <a name="workflow"></a>Munkafolyamat

A Bing Image Search API egy RESTful-webszolgáltatás, így bármely olyan programnyelvből meghívható, amely képes HTTP-lekérdezéseket kezelni és JSON formátumot elemezni. A szolgáltatás a [REST API](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?) vagy az [SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) segítségével használható.

1. Hozzon létre egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Küldjön egy kérést az API-ra egy érvényes [keresési lekérdezéssel](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries).
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="next-steps"></a>További lépések

Először próbálja ki a Bing Image Search API [interaktív bemutatóját](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/).
Ez bemutatja, hogyan lehet gyorsan testre szabni a keresési lekérdezéseket és felkutatni a webes képeket.

Ha már készen áll az API-t, hozzon létre egy [Cognitive services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Az első API-kérés gyors létrehozásához sajátítsa el a következőket:

* [Keresési lekérdezések küldése a Bingre](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp) a REST API használatával, illetve
* a Bing által visszaadott képek [kérése és szűrése](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) az SDK használatával.

## <a name="see-also"></a>Lásd még

* [Díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) a Bing Search APIs. 

* A [a Bing Image Search API 7-es verziója](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) referencia a szakasz ismerteti az API-k végpontok, fejlécek, API-válaszok és a lekérdezési paraméterek.

* A [Bing használati és megjelenítési követelményei](./useanddisplayrequirements.md) a Bing Search API-k használatával kapott tartalmak és információk elfogadható használatát határozzák meg.

* A [első képeket a weben a Bing Image Search API az](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) cikk ismerteti, hogyan keresheti ki és képek bekérése a webről.

* A [Sending és a keresési lekérdezéseket](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) cikk teheti, testre szabhatja, és keresési lekérdezések forgáspont módját ismerteti.
