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
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: d5a4c3110186329516f10465c5e80a10b0ceb7b7
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2020
ms.locfileid: "94379820"
---
# <a name="what-is-the-bing-video-search-api"></a>Mi az a Bing Video Search API?

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A Bing Video Search API megkönnyíti a videók keresési funkcióinak hozzáadását a szolgáltatásaihoz és alkalmazásaihoz. Ha a felhasználói keresési lekérdezéseket az API-val küldi el, a [Bing-videóhoz](https://www.bing.com/video)hasonló releváns és kiváló minőségű videókat kaphat és jeleníthet meg. Ezt az API-t csak videókat tartalmazó keresési eredményekhez használhatja. A [Bing Web Search API](../bing-web-search/overview.md) más típusú webes tartalmakat, például weblapokat, videókat, híreket és képeket adhat vissza.

## <a name="bing-video-search-api-features"></a>Bing Video Search API funkciók

| Szolgáltatás                                                                                                                                                                                 | Leírás                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Keresési kifejezések valós idejű felkínálása](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | A [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) a begépelés során javaslatokat tesz a keresési kifejezésekre, így könnyebbé teszi az alkalmazások használatát. |
| [A videók eredményeinek szűrése és korlátozása](concepts/get-videos.md#filtering-videos)                      | A lekérdezési paraméterek szerkesztésével visszaadott videók szűrése.                                                                                                       |
| [Miniatűrök körülvágása, átméretezése és megjelenítése](../bing-web-search/resize-and-crop-thumbnails.md)                                                | A Bing Video Search API által visszaadott videók miniatűr előnézeteit szerkesztheti és jelenítheti meg.                                                                                      |
| [Népszerű videók lekérése](trending-videos.md) | Keresse meg a világ különböző pontjain található videók trendjét.                                                                                                          |
| [Videoelemzések lekérése](video-insights.md) | Testre szabhatja a világ minden tájáról származó videók keresését.                                                                                                          |

## <a name="workflow"></a>Munkafolyamat

A Bing Video Search API egy REST-alapú webszolgáltatás, így könnyen hívható bármely programozási nyelvről, amely HTTP-kéréseket tesz elérhetővé, és értelmezi a JSON-t. A szolgáltatás a [REST API](./quickstarts/csharp.md) vagy az [SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) segítségével használható.

1. Hozzon létre egy [Cognitive Services API-fiókot](../cognitive-services-apis-create-account.md), amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/free/cognitive-services/).
2. Küldjön egy kérést az API-ra egy érvényes keresési lekérdezéssel.
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.


## <a name="next-steps"></a>Következő lépések

A Bing Video Search API [interaktív bemutató](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) bemutatja, hogyan szabhatja testre a keresési lekérdezést, és hogyan kereshet meg videókat a weben.

A rövid útmutató segítségével gyorsan megkezdheti első [API-](./quickstarts/csharp.md) kérelmét.

## <a name="see-also"></a>Lásd még

* A [Bing Video Search API v7](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) -hivatkozási oldal a keresési eredmények kéréséhez használt végpontok, fejlécek és lekérdezési paraméterek listáját tartalmazza.

* A [Bing használati és megjelenítési követelményei](../bing-web-search/use-display-requirements.md) a Bing Search API-k használatával kapott tartalmak és információk elfogadható használatát határozzák meg.

* A többi elérhető API-k megismeréséhez látogasson el a [BING Search API hub oldalára](../bing-web-search/overview.md) .