---
title: Mi az a Bing Video Search API?
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan kereshet videókat a weben a Bing Video Search API használatával.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 06/26/2019
ms.author: scottwhi
ms.openlocfilehash: 4f3a52590a9a0c50bdc87f87792027333fc5269f
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500155"
---
# <a name="what-is-the-bing-video-search-api"></a>Mi az a Bing Video Search API?

A Bing Video Search API megkönnyíti a videók keresési funkcióinak hozzáadását a szolgáltatásaihoz és alkalmazásaihoz. Ha a felhasználói keresési lekérdezéseket az API-val küldi el, a [Bing](https://www.bing.com/video)-videóhoz hasonló releváns és kiváló minőségű videókat kaphat és jeleníthet meg. Ezt az API-t csak videókat tartalmazó keresési eredményekhez használhatja. A [Bing Web Search API](../bing-web-search/search-the-web.md) más típusú webes tartalmakat, például weblapokat, videókat, híreket és képeket adhat vissza.

## <a name="bing-video-search-api-features"></a>Bing Video Search API funkciók

| Funkció                                                                                                                                                                                 | Leírás                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Keresési kifejezések valós idejű felkínálása](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | A [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) a begépelés során javaslatokat tesz a keresési kifejezésekre, így könnyebbé teszi az alkalmazások használatát. |
| [A videók eredményeinek szűrése és korlátozása](concepts/get-videos.md#filtering-videos)                      | A lekérdezési paraméterek szerkesztésével visszaadott videók szűrése.                                                                                                       |
| [Miniatűrök körülvágása, átméretezése és megjelenítése](../bing-web-search/resize-and-crop-thumbnails.md)                                                | A Bing Video Search API által visszaadott videók miniatűr előnézeteit szerkesztheti és jelenítheti meg.                                                                                      |
| [Trendek videók letöltése](trending-videos.md) | Keresse meg a világ különböző pontjain található videók trendjét.                                                                                                          |
| [Videó-felismerések beolvasása](video-insights.md) | Testre szabhatja a világ minden tájáról származó videók keresését.                                                                                                          |

## <a name="workflow"></a>Munkafolyamat

A Bing Video Search API egy REST-alapú webszolgáltatás, így könnyen hívható bármely programozási nyelvről, amely HTTP-kéréseket tesz elérhetővé, és értelmezi a JSON-t. A szolgáltatás a [REST API](csharp.md) vagy az [SDK](video-search-sdk-quickstart.md) segítségével használható.

1. Hozzon létre egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Kérelem küldése az API-nak érvényes keresési lekérdezéssel.
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.


## <a name="next-steps"></a>További lépések

A Bing Video Search API [interaktív bemutató](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) bemutatja, hogyan szabhatja testre a keresési lekérdezést, és hogyan kereshet meg videókat a weben.

Amikor kész meghívni az API-t, hozzon létre egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

A rövid útmutató segítségével gyorsan megkezdheti első [API-](csharp.md) kérelmét.

## <a name="see-also"></a>Lásd még

* A [Bing Video Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) -hivatkozási oldal a keresési eredmények kéréséhez használt végpontok, fejlécek és lekérdezési paraméterek listáját tartalmazza.

* A [Bing használati és megjelenítési követelményei](./useanddisplayrequirements.md) a Bing Search API-k használatával kapott tartalmak és információk elfogadható használatát határozzák meg.