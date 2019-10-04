---
title: Bing News Search-végpontok
titleSuffix: Azure Cognitive Services
description: A Hírek keresési API-végpontjának összefoglalása.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 82e37e8fa47b467e7c2fe98f801482675809a266
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423786"
---
# <a name="bing-news-search-api-endpoints"></a>Bing News Search API végpontok

A **News Search API** híreket, weblapokat, képeket, videókat és entitásokat [](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)ad vissza. Az entitások egy személy, hely vagy témakör összegző információit tartalmazzák.

## <a name="endpoints"></a>Végpontok

Ha a Bing News Search API használatával szeretné beolvasni a keresési eredményeket, `GET` küldjön egy kérelmet a következő végpontok egyikére. A fejlécek és az URL-paraméterek további specifikációkat határoznak meg.

### <a name="news-items-by-search-query"></a>Hírek keresési lekérdezés szerint

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Egy keresési lekérdezésen alapuló híreket ad vissza. Ha a keresési lekérdezés üres, az API különböző kategóriákból származó legfontosabb híreket ad vissza. A keresési kifejezés URL-címének kódolásával küldje el a lekérdezést, és`q=""` fűzze hozzá a paraméterhez. A rendelkezésre állást lásd: [támogatott országok/régiók és piacok](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Legfontosabb hírek kategóriánként

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

A legfontosabb híreket adja vissza kategóriánként. A, `category=business` `category=sports`a, a vagy `category=entertainment`a használatával a legfontosabb üzleti, sport-vagy szórakoztató cikkeket kérheti le.  A `category` paraméter csak az `/news` URL-cím használatával használható. Néhány formális követelmény a kategóriák megadásához; a lekérdezési paraméter dokumentációjában tájékozódhat. [](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) `category` A keresési kifejezés URL-címének kódolásával küldje el a lekérdezést, és`q=""` fűzze hozzá a paraméterhez. A rendelkezésre állást lásd: [támogatott országok/régiók és piacok](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Trendek – Hírek 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

A közösségi hálózatokra jelenleg kapcsolódó híreket adja vissza. A beállítás `/trendingtopics` megadása esetén a Bing Search figyelmen kívül hagyja a többi paramétert, például `freshness` a `?q=""`és a elemet. A rendelkezésre állást lásd: [támogatott országok/régiók és piacok](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>További lépések

A fejlécekkel, paraméterekkel, piaci kódokkal, válasz-objektumokkal, hibákkal és egyéb adatokkal kapcsolatos részletekért tekintse meg a [Bing News Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) dokumentációját.

Az egyes végpontok által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes típusok hivatkozási oldalait.
A News Search API-t használó alapszintű kérelmekre például a következő témakörben talál további információt: [Bing News Search Quick-starts](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
