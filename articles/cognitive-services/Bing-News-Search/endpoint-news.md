---
title: Bing News Search-végpontok
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Hírek keresési API-végpontjainak összegzését tartalmazza; hírek, top hírek és a felkapott hírek.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: dc7d16fe809e3e324f384b0d9e088dd7e6ab261c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74111483"
---
# <a name="bing-news-search-api-endpoints"></a>Bing News Search API-végpontok

A **News Search API** híreket, weblapokat, képeket, videókat és [entitásokat ad](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)vissza. Az entitások egy személyre, helyre vagy témakörre vonatkozó összesítő információkat tartalmaznak.

## <a name="endpoints"></a>Végpontok

Ha a Bing News Search API használatával `GET` szeretne híreket kapni, küldjön egy kérést az alábbi végpontok egyikére. A fejlécek és az URL-paraméterek további specifikációkat határoznak meg.

### <a name="news-items-by-search-query"></a>Hírek keresési lekérdezés szerint

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Keresési lekérdezésalapján adja vissza a híreket. Ha a keresési lekérdezés üres, az API különböző kategóriákból származó legfontosabb híreket fog visszaadni. Küldjön egy lekérdezést url kódolás a keresési`q=""` kifejezés, és hozzáfűzve azt a paramétert. A rendelkezésre állásról a [Támogatott országok/régiók és piacok című témakörben lehet részt.](language-support.md#supported-markets-for-news-search-endpoint)

### <a name="top-news-items-by-category"></a>Top hírek kategória szerint

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

A legfontosabb híreket adja eredményül kategória szerint. A legfontosabb üzleti, sport- vagy szórakoztatócikkeket `category=business` `category=sports`a `category=entertainment`használatával külön igényelheti, vagy a használatával. A `category` paraméter csak az `/news` URL-címével használható. A kategóriák meghatározására vonatkozóan vannak formai követelmények; lásd `category` a [lekérdezési paraméter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) dokumentációjában. Küldjön egy lekérdezést url kódolás a keresési`q=""` kifejezés, és hozzáfűzve azt a paramétert. A rendelkezésre állásról a [Támogatott országok/régiók és piacok című témakörben lehet részt.](language-support.md#supported-markets-for-news-endpoint)

### <a name="trending-news-topics"></a>Felkapott hírek témák 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

A közösségi hálózatokon jelenleg népszerű hírtémaköröket adja vissza. Ha `/trendingtopics` a beállítás szerepel, a Bing-keresés figyelmen `freshness` `?q=""`kívül hagy számos más paramétert, például a és a lehetőséget. A rendelkezésre állásról a [Támogatott országok/régiók és piacok című témakörben lehet részt.](language-support.md#supported-markets-for-news-trending-endpoint)

## <a name="next-steps"></a>További lépések

A fejlécekről, paraméterekről, piaci kódokról, válaszobjektumokról, hibákról stb. [Bing News search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)

Az egyes végpontok által támogatott paraméterekről az egyes típusok referenciaoldalain talál teljes körű tájékoztatást.
A Hírek keresési API-t használó alapvető kérelmekre a [Bing News Search gyorsindítása című](https://docs.microsoft.com/azure/cognitive-services/bing-news-search)témakörben talál példákat.
