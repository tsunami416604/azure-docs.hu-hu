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
ms.openlocfilehash: c52098d86efe60ee524735e6158add5260a0757f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338231"
---
# <a name="what-is-the-bing-image-search-api"></a>Mi az a Bing Image Search API?

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A Bing Image Search API lehetővé teszi a Bing képkeresési funkcióinak használatát az alkalmazásban. Ha keresési lekérdezéseket küld az API-nak, a [Bing.com/images](https://www.bing.com/images)-hez hasonló magas színvonalú képeket érhet el.

Míg a Bing Image Search API csak képkeresési eredményeket biztosít, kombinálhatja vagy használhatja a többi elérhető [Bing Search API-k](../bing-web-search/bing-api-comparison.md) , így számos különböző típusú tartalmat talál a weben.

## <a name="bing-image-search-features"></a>A Bing Image Search funkciói

| Funkció                                                                                                                                                                                 | Leírás                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Keresési kifejezések valós idejű felkínálása](./concepts/bing-image-search-sending-queries.md) | A [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) a begépelés során javaslatokat tesz a keresési kifejezésekre, így könnyebbé teszi az alkalmazások használatát. |
| [Képtalálatok szűrése és korlátozása](./concepts/bing-image-search-get-images.md)                       | A Bing által visszaadott képek szűrése a lekérdezési paraméterek szerkesztésével.                                                                                                       |
| [Miniatűrök körülvágása, átméretezése és megjelenítése](../bing-web-search/resize-and-crop-thumbnails.md)                                                | A Bing Image Search által visszaadott képek előnézeti miniatűrjeinek szerkesztése és megjelenítése.                                                                                      |
| [Felhasználói keresési lekérdezések szegmentálása és kiterjesztése](./concepts/bing-image-search-sending-queries.md)               | A keresési képességek kiterjesztése a Bing által javasolt keresési kifejezések a lekérdezésekbe történő belefoglalásával és megjelenítésével.                                                                    |
| [Népszerű képek lekérése](trending-images.md)                                                                     | Népszerű képek testre szabott keresése a világ minden tájáról.                                                                                                          |

## <a name="workflow"></a>Munkafolyamat

A Bing Image Search API egy RESTful-webszolgáltatás, így bármely olyan programnyelvből meghívható, amely képes HTTP-lekérdezéseket kezelni és JSON formátumot elemezni. A szolgáltatás a [REST API](./quickstarts/csharp.md) vagy az [SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) segítségével használható.

1. Hozzon létre egy [Cognitive Services API-fiókot](../cognitive-services-apis-create-account.md), amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/free/cognitive-services/).
2. Küldjön egy kérést az API-ra egy érvényes [keresési lekérdezéssel](./concepts/bing-image-search-sending-queries.md).
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="next-steps"></a>További lépések

Először próbálja ki a Bing Image Search API [interaktív bemutatóját](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/).
Ez bemutatja, hogyan lehet gyorsan testre szabni a keresési lekérdezéseket és felkutatni a webes képeket.

Az első API-kérés gyors létrehozásához sajátítsa el a következőket:

* [Keresési lekérdezések küldése a Bingre](./quickstarts/csharp.md) a REST API használatával, illetve
* a Bing által visszaadott képek [kérése és szűrése](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) az SDK használatával.

## <a name="see-also"></a>Lásd még

* A Bing Search API-k [díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) . 

* A [Bing Image Search API v7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) -hivatkozás szakasz az API-végpontokról, a fejlécekről, az API-válaszokról és a lekérdezési paraméterekről tartalmaz információkat.

* A [Bing használati és megjelenítési követelményei](../bing-web-search/use-display-requirements.md) a Bing Search API-k használatával kapott tartalmak és információk elfogadható használatát határozzák meg.

* A [webről származó rendszerképek beolvasása a Bing Image Search API](./concepts/bing-image-search-get-images.md) cikkből megtudhatja, hogyan kereshet és kérhet le képeket a webről.

* A [keresési lekérdezések küldésével és](./concepts/bing-image-search-sending-queries.md) használatával foglalkozó cikk a keresési lekérdezések készítését, testreszabását és kimutatását ismerteti.

* A többi elérhető API-k megismeréséhez látogasson el a [BING Search API hub oldalára](../bing-web-search/overview.md) .