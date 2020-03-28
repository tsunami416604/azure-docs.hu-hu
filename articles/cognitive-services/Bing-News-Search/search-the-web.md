---
title: Mi az a Bing News Search API?
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogy a Bing News Search API-val hogyan kereshet az interneten az aktuális címsorok között a kategóriák között, beleértve a címsorokat és a felkapott témaköröket is.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: e0e99c7f677173c64afad3109b2f4accd7cb3cb9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448448"
---
# <a name="what-is-the-bing-news-search-api"></a>Mi az a Bing News Search API?

A Bing News Search API megkönnyíti a Bing kognitív hírek keresésének lehetőségeit az alkalmazásokba való integrálásához. Az API hasonló élményt nyújt, mint a [Bing News,](https://www.bing.com/news)amely lehetővé teszi a keresési lekérdezések küldését és a releváns hírek fogadását.

Ne feledje, hogy a Bing News Search API csak a hírek keresési eredményeit tartalmazza. A [Bing Web Search API,](../bing-web-search/search-the-web.md) [a Video Search API](../bing-video-search/search-the-web.md) és a Image Search [API](../bing-image-search/overview.md) segítségével más típusú webes tartalmakhoz használható.

## <a name="bing-news-search-api-features"></a>A Bing News Search API szolgáltatásai

Bár a Bing News Search API elsősorban releváns híreket talál meg és ad vissza, számos funkciót biztosít az intelligens és célzott hírek visszakereséséhez az interneten.

|Szolgáltatás  |Leírás  |
|---------|---------|
|[Keresési kifejezések reklisése és használata](concepts/search-for-news.md#suggest-and-use-search-terms)     | A [bing automatikus javaslat API](../bing-autosuggest/get-suggested-search-terms.md) használatával javíthatja a keresési élményt a javasolt keresési kifejezések beírásakor történő megjelenítéséhez.         |
|[Általános hírek beszerezni](concepts/search-for-news.md#get-general-news)     | Híreket kereshet a Bing News Search API-ba küldött keresési lekérdezéssel, és visszakeresheti a releváns hírek listáját.           |
|[A mai legfontosabb hírek](concepts/search-for-news.md#get-todays-top-news)      | Szerezd meg a nap legfontosabb híreit, minden kategóriában.       |
|[Hírek kategóriánként](concepts/search-for-news.md)     | Hírek keresése adott kategóriákban.        | 
|[Kiemelt hírek](concepts/search-for-news.md)     | Keresse meg a legnépszerűbb címsorokat az összes kategóriában.         |

## <a name="workflow"></a>Munkafolyamat

A Bing News Search API egy RESTful webszolgáltatás, amely megkönnyíti a http-kérelmek készítésére és a JSON elemzésre képes programozási nyelvről történő hívását. A szolgáltatás a REST API vagy az SDK segítségével használható.

1. Hozzon létre egy Cognitive Services API-fiókot, amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, [létrehozhat egy fiókot ingyenesen.](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-news-api)

2. Küldjön egy kérést az API-ra egy érvényes keresési lekérdezéssel.

3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="next-steps"></a>További lépések

Először próbálja ki a Bing News Search API [interaktív bemutatóját.](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) Ez a bemutató bemutatja, hogyan szabhatja gyorsan a keresési lekérdezést, és hogyan kereshet híreket az interneten.

Az első API-kérelem gyors megkezdéséhez próbálkozzon egy rövid útmutatóval a [REST API-hoz](quickstart.md) vagy az [SDK-k](sdk.md)egyikéhez.

## <a name="see-also"></a>Lásd még

* A [Bing News Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) referenciaszakasza definíciókat és információkat tartalmaz a végpontokra, a fejlécekre, az API-válaszokra és a lekérdezési paraméterekre vonatkozóan, amelyek segítségével képalapú keresési eredményeket kérhet.
* A [Bing használati és megjelenítési követelményei](./useanddisplayrequirements.md) a Bing Search API-k használatával kapott tartalmak és információk elfogadható használatát határozzák meg.
* Látogasson el a [Bing Search API-központ oldalára](../bing-web-search/search-the-web.md) a többi elérhető API-k felfedezéséhez.