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
ms.date: 12/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 6bd8c472aa659bec31440292e20328865b1cb531
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79134527"
---
# <a name="what-is-the-bing-image-search-api"></a>Mi az a Bing Image Search API?

A Bing Image Search API lehetővé teszi a Bing képkeresési funkcióinak használatát az alkalmazásban. Ha keresési lekérdezéseket küld az API-nak, a [Bing.com/images](https://www.bing.com/images)-hez hasonló magas színvonalú képeket érhet el.

Míg a Bing Image Search API csak képkeresési eredményeket biztosít, kombinálhatja vagy használhatja a többi elérhető [Bing Search API-k](../bing-web-search/bing-api-comparison.md) , így számos különböző típusú tartalmat talál a weben.

## <a name="bing-image-search-features"></a>A Bing Image Search funkciói

| Funkció                                                                                                                                                                                 | Leírás                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Keresési kifejezések valós idejű felkínálása](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) | A [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) a begépelés során javaslatokat tesz a keresési kifejezésekre, így könnyebbé teszi az alkalmazások használatát. |
| [Képtalálatok szűrése és korlátozása](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images)                       | A Bing által visszaadott képek szűrése a lekérdezési paraméterek szerkesztésével.                                                                                                       |
| [Miniatűrök körülvágása, átméretezése és megjelenítése](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/resize-and-crop-thumbnails)                                                | A Bing Image Search által visszaadott képek előnézeti miniatűrjeinek szerkesztése és megjelenítése.                                                                                      |
| [Felhasználói keresési lekérdezések szegmentálása és kiterjesztése](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)               | A keresési képességek kiterjesztése a Bing által javasolt keresési kifejezések a lekérdezésekbe történő belefoglalásával és megjelenítésével.                                                                    |
| [Népszerű képek lekérése](trending-images.md)                                                                     | Népszerű képek testre szabott keresése a világ minden tájáról.                                                                                                          |

## <a name="workflow"></a>Munkafolyamat

A Bing Image Search API egy RESTful-webszolgáltatás, így bármely olyan programnyelvből meghívható, amely képes HTTP-lekérdezéseket kezelni és JSON formátumot elemezni. A szolgáltatás a [REST API](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?) vagy az [SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) segítségével használható.

1. Hozzon létre egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Küldjön egy kérést az API-ra egy érvényes [keresési lekérdezéssel](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries).
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="next-steps"></a>Következő lépések

Először próbálja ki a Bing Image Search API [interaktív bemutatóját](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/).
Ez bemutatja, hogyan lehet gyorsan testre szabni a keresési lekérdezéseket és felkutatni a webes képeket.

Ha készen áll az API meghívására, hozzon létre egy [kognitív Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Az első API-kérés gyors létrehozásához sajátítsa el a következőket:

* [Keresési lekérdezések küldése a Bingre](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp) a REST API használatával, illetve
* a Bing által visszaadott képek [kérése és szűrése](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) az SDK használatával.

## <a name="see-also"></a>Lásd még

* A Bing Search API-k [díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) . 

* A [Bing Image Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) -hivatkozás szakasz az API-végpontokról, a fejlécekről, az API-válaszokról és a lekérdezési paraméterekről tartalmaz információkat.

* A [Bing használati és megjelenítési követelményei](./useanddisplayrequirements.md) a Bing Search API-k használatával kapott tartalmak és információk elfogadható használatát határozzák meg.

* A [webről származó rendszerképek beolvasása a Bing Image Search API](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) cikkből megtudhatja, hogyan kereshet és kérhet le képeket a webről.

* A [keresési lekérdezések küldésével és](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) használatával foglalkozó cikk a keresési lekérdezések készítését, testreszabását és kimutatását ismerteti.

* A többi elérhető API-k megismeréséhez látogasson el a [BING Search API hub oldalára](../bing-web-search/search-the-web.md) .
