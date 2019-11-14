---
title: A Bing Image Search API végpontjai
titleSuffix: Azure Cognitive Services
description: A Image Search API három végpontot tartalmaz. Az 1. végpont a webről származó képeket ad vissza. A 2. végpont a ImageInsights adja vissza. A 3. végpont a trendi képeket adja vissza.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 38416f6a580d270aefc287de0c198bd418a44db9
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072633"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>A Bing Image Search API végpontjai

A **Image Search API** három végpontot tartalmaz.  Az 1. végpont a webről származó képeket ad vissza egy lekérdezés alapján. A 2. végpont a [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse)adja vissza.  A 3. végpont a trendi képeket adja vissza.

## <a name="endpoints"></a>Végpontok

Ha a Bing API használatával szeretné beolvasni a képkeresési eredményeket, küldjön egy kérelmet a következő végpontok egyikére. A fejlécek és az URL-paraméterek használatával további specifikációkat határozhat meg.

**1. végpont:** Azokat a lemezképeket adja vissza, amelyek a `?q=""`által definiált felhasználó keresési lekérdezéséhez szükségesek.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**2. végpont:** Egy képpel kapcsolatos elemzéseket ad vissza `GET` vagy `POST`használatával.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
A GET-kérelem egy képpel kapcsolatos információkat ad vissza, például a képet tartalmazó weblapokat. Adja meg a [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) paramétert egy `GET` kéréssel.

Vagy egy `POST` kérelem törzsében is használhat bináris képet, és a modules [paramétert](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) `RecognizedEntities`értékre állíthatja. Ez egy olyan [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) ad vissza, amelyet paraméterként fog használni egy későbbi `GET` kérelemben, amely a rendszerképben szereplő személyekre vonatkozó adatokat adja vissza.  Állítsa be úgy a `modules`t, hogy `All` az összes bepillantást, kivéve a `POST` eredményében lévő `RecognizedEntities`eket anélkül, hogy egy másik hívást kellene beszereznie a `insightsToken`használatával.


**3. végpont:** Azokat a képeket adja vissza, amelyek a mások által végzett keresési kérelmek alapján alakulnak. A képek különböző kategóriákba vannak elkülönítve, például a figyelemreméltó személyek vagy események alapján.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

A Trends lemezképeket támogató piacok listáját a következő témakörben tekintheti meg: [trendek képek](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

A fejlécekről, paraméterekről, piaci kódokról, válasz-objektumokról, hibákról és egyéb adatokról a [Bing Image Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) -dokumentációban talál további információt.
## <a name="response-json"></a>Válasz JSON
A képkeresési kérelemre adott válasz az eredményeket JSON-objektumokként tartalmazza. Az eredmények elemzésére példákat az [oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) és a [forráskód](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source)című témakörben talál.

## <a name="next-steps"></a>További lépések
A **Bing** API-k olyan keresési műveleteket támogatnak, amelyek típusaik alapján adják vissza az eredményeket. A keresési végpontok az eredményeket JSON-válasz objektumokként adják vissza.  Minden végpont támogatja a lekérdezéseket, amelyek adott nyelvet és/vagy helyet adnak vissza a földrajzi hosszúság, a szélesség és a keresési sugár alapján.

Az egyes végpontok által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes típusok hivatkozási oldalait.
A képkeresési API-t használó alapszintű kérelmekre például a következő témakörben talál további információt: [Image Search Quick-starts](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
