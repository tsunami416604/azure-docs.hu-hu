---
title: Mi az a Bing Video Search API?
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogy miként kereshet videókat az interneten a Bing Video Search API használatával.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 8377f0f5d586212c94bb763598b6e7a9e391073c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382718"
---
# <a name="what-is-the-bing-video-search-api"></a>Mi az a Bing Video Search API?

A Bing Video Search API megkönnyíti a videokeresési lehetőségek hozzáadását a szolgáltatásokhoz és alkalmazásokhoz. Ha felhasználói keresési lekérdezéseket küld az API-val, a Bing [Video](https://www.bing.com/video)programhoz hasonló releváns és kiváló minőségű videókat szerezhet be és jeleníthet meg. Használja ezt az API-t olyan keresési eredményekhez, amelyek csak videókat tartalmaznak. A [Bing Web Search API](../bing-web-search/search-the-web.md) más típusú webes tartalmakat is visszaadhat, például weblapokat, videókat, híreket és képeket.

## <a name="bing-video-search-api-features"></a>A Bing Video Search API szolgáltatásai

| Szolgáltatás                                                                                                                                                                                 | Leírás                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Keresési kifejezések valós idejű felkínálása](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | A [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) a begépelés során javaslatokat tesz a keresési kifejezésekre, így könnyebbé teszi az alkalmazások használatát. |
| [A videóeredmények szűrése és korlátozása](concepts/get-videos.md#filtering-videos)                      | Szűrje a visszaadott videókat a lekérdezési paraméterek szerkesztésével.                                                                                                       |
| [Miniatűrök körülvágása, átméretezése és megjelenítése](../bing-web-search/resize-and-crop-thumbnails.md)                                                | A Bing Video Search API által visszaadott videók miniatűr előnézetének szerkesztése és megjelenítése.                                                                                      |
| [Népszerű videók lekérése](trending-videos.md) | Keressen felkapott videókat a világ minden tájáról.                                                                                                          |
| [Videoelemzések lekérése](video-insights.md) | Testreszabhatja a világ minden tájáról származó felkapott videók keresését.                                                                                                          |

## <a name="workflow"></a>Munkafolyamat

A Bing Video Search API egy RESTful webszolgáltatás, amely megkönnyíti a http-kérelmek et és a JSON elemzésre képes programozási nyelvről történő hívását. A szolgáltatás a [REST API](csharp.md) vagy az [SDK](video-search-sdk-quickstart.md) segítségével használható.

1. Hozzon létre egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Küldjön egy kérést az API-ra egy érvényes keresési lekérdezéssel.
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.


## <a name="next-steps"></a>További lépések

A Bing Video Search API [interaktív bemutatója](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) bemutatja, hogyan szabhatja testre a keresési lekérdezést, és hogyan kereshet videókat az interneten.

Amikor kész meghívni az API-t, hozzon létre egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

A [rövid útmutató](csharp.md) segítségével gyorsan elkezdheti az első API-kérelmet.

## <a name="see-also"></a>Lásd még

* A [Bing Video Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) referencialapja a keresési eredmények kéréséhez használt végpontok, fejlécek és lekérdezési paraméterek listáját tartalmazza.

* A [Bing használati és megjelenítési követelményei](./useanddisplayrequirements.md) a Bing Search API-k használatával kapott tartalmak és információk elfogadható használatát határozzák meg.

* Látogasson el a [Bing Search API-központ oldalára](../bing-web-search/search-the-web.md) a többi elérhető API-k felfedezéséhez.