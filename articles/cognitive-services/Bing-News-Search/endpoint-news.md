---
title: Bing News Search-végpontok
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Hírek keresési API-végpontok összegzését tartalmazza; Hírek, legfontosabb hírek és trendek a hírekhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: dc7d16fe809e3e324f384b0d9e088dd7e6ab261c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "74111483"
---
# <a name="bing-news-search-api-endpoints"></a>Bing News Search API végpontok

A **News Search API** híreket, weblapokat, képeket, videókat és [entitásokat](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)ad vissza. Az entitások egy személy, hely vagy témakör összegző információit tartalmazzák.

## <a name="endpoints"></a>Végpontok

Ha a Bing News Search API használatával szeretné beolvasni a keresési eredményeket, küldjön egy `GET` kérelmet a következő végpontok egyikére. A fejlécek és az URL-paraméterek további specifikációkat határoznak meg.

### <a name="news-items-by-search-query"></a>Hírek keresési lekérdezés szerint

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Egy keresési lekérdezésen alapuló híreket ad vissza. Ha a keresési lekérdezés üres, az API különböző kategóriákból származó legfontosabb híreket ad vissza. A keresési kifejezés URL-címének kódolásával küldje el a lekérdezést, és fűzze hozzá a `q=""` paraméterhez. A rendelkezésre állást lásd: [támogatott országok/régiók és piacok](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Legfontosabb hírek kategóriánként

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

A legfontosabb híreket adja vissza kategóriánként. A, a, a vagy a használatával a legfontosabb üzleti, sport-vagy szórakoztató cikkeket kérheti le `category=business` `category=sports` `category=entertainment` . A `category` paraméter csak az `/news` URL-cím használatával használható. Néhány formális követelmény a kategóriák megadásához; `category` a [lekérdezési paraméter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) dokumentációjában tájékozódhat. A keresési kifejezés URL-címének kódolásával küldje el a lekérdezést, és fűzze hozzá a `q=""` paraméterhez. A rendelkezésre állást lásd: [támogatott országok/régiók és piacok](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Trendek – Hírek 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

A közösségi hálózatokra jelenleg kapcsolódó híreket adja vissza. A beállítás megadása esetén a `/trendingtopics` Bing Search figyelmen kívül hagyja a többi paramétert, például a és a elemet `freshness` `?q=""` . A rendelkezésre állást lásd: [támogatott országok/régiók és piacok](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>További lépések

A fejlécekkel, paraméterekkel, piaci kódokkal, válasz-objektumokkal, hibákkal és egyéb adatokkal kapcsolatos részletekért tekintse meg a [Bing News Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) dokumentációját.

Az egyes végpontok által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes típusok hivatkozási oldalait.
A News Search API-t használó alapszintű kérelmekre például a következő témakörben talál további információt: [Bing News Search Quick-starts](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
