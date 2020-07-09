---
title: Mi az a Bing News Search API?
titleSuffix: Azure Cognitive Services
description: Ebből a témakörből megtudhatja, hogyan használhatja a Bing News Search API a webes keresésre az aktuális főcímek kategóriák között, beleértve a főcímeket és a Kiemelt témákat.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: d44fe58eb17e7f11dc64ee1426df7f356cb91aef
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85602754"
---
# <a name="what-is-the-bing-news-search-api"></a>Mi az a Bing News Search API?

A Bing News Search API megkönnyíti a Bing kognitív Hírek keresési funkcióinak integrálását az alkalmazásokba. Az API hasonló élményt nyújt a [Bing hírekhez](https://www.bing.com/news), így lehetővé teszi a keresési lekérdezések küldését és a releváns Hírek fogadását.

Vegye figyelembe, hogy a Bing News Search API csak a Hírek keresési eredményeit jeleníti meg. Használja a [Bing Web Search API](../bing-web-search/search-the-web.md), [Video Search API](../bing-video-search/search-the-web.md) -t és a [Image Search API](../bing-image-search/overview.md) -t más típusú webes tartalomhoz.

## <a name="bing-news-search-api-features"></a>Bing News Search API funkciók

Noha a Bing News Search API elsődlegesen megkeresi és visszaadja a kapcsolódó híreket, számos funkciót kínál az intelligens és a célzott híreket a weben.

|Szolgáltatás  |Leírás  |
|---------|---------|
|[Keresési kifejezések ajánlása és használata](concepts/search-for-news.md#suggest-and-use-search-terms)     | A [Bing AutoSuggest API](../bing-autosuggest/get-suggested-search-terms.md) használatával javíthatja a keresési élményét, ha Begépelte a javasolt keresési kifejezéseket.         |
|[Általános hírek beszerzése](concepts/search-for-news.md#get-general-news)     | Megkeresheti a híreket úgy, hogy keresési lekérdezést küld a Bing News Search APInak, és lekérdezi a kapcsolódó híreket tartalmazó cikkeket.           |
|[A mai legfontosabb hírek](concepts/search-for-news.md#get-todays-top-news)      | Tekintse meg az első híreket az egész napra, az összes kategóriába.       |
|[Hírek kategóriánként](concepts/search-for-news.md)     | Hírek keresése adott kategóriákban.        | 
|[Headline Hírek](concepts/search-for-news.md)     | Keresse meg az összes kategóriát a legnépszerűbb főcímek között.         |

## <a name="workflow"></a>Munkafolyamat

A Bing News Search API egy REST-alapú webszolgáltatás, így könnyen hívható bármely programozási nyelvről, amely HTTP-kéréseket tesz elérhetővé, és értelmezi a JSON-t. A szolgáltatás a REST API vagy az SDK segítségével használható.

1. Hozzon létre egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/free/cognitive-services/).
2. Küldjön egy kérést az API-ra egy érvényes keresési lekérdezéssel.
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="next-steps"></a>További lépések

Először próbálja ki a Bing News Search API [interaktív bemutatóját](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) . Ez a bemutató bemutatja, hogyan lehet gyorsan testreszabni egy keresési lekérdezést, és híreket keresni a weben.

Az első API-kérelem gyors megkezdéséhez próbálkozzon a [REST API](quickstart.md) vagy az [SDK](sdk.md)-k egyikével.

## <a name="see-also"></a>Lásd még

* A [Bing News Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) -hivatkozás szakasza tartalmazza a végpontok, fejlécek, API-válaszok és lekérdezési paraméterek definícióit és információit, amelyeket a képalapú keresési eredmények kérelmezéséhez használhat.
* A [Bing használati és megjelenítési követelményei](./useanddisplayrequirements.md) a Bing Search API-k használatával kapott tartalmak és információk elfogadható használatát határozzák meg.
* A többi elérhető API-k megismeréséhez látogasson el a [BING Search API hub oldalára](../bing-web-search/search-the-web.md) .