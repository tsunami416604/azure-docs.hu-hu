---
title: Webes keresési végpont
titleSuffix: Azure Cognitive Services
description: A webes keresési eredmények `GET` lekéréséhez küldjön egy kérést a következő végpontra. A fejlécek és az URL-paraméterek további specifikációkat határoznak meg.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: 2edc6a4cf4d4fb174c1b2067d8b3fb3b6f31859f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74111339"
---
# <a name="web-search-endpoint"></a>Webes keresési végpont

A **Web Search API** weblapokat, híreket, képeket, videókat és [entitásokat ad](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)vissza. Az entitások összegző információkkal rendelkeznek egy személyről, helyről vagy témakörről.

## <a name="endpoint"></a>Végpont

Ha a Bing API használatával szeretne `GET` webes keresési eredményeket kapni, küldjön egy kérést a következő végpontra. A fejlécek és az URL-paraméterek további specifikációkat határoznak meg.

**Végpont**: A felhasználó által `?q=""`meghatározott keresési lekérdezéséhez kapcsolódó webes eredményeket ad eredményül.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Végpont: A fejlécekről, paraméterekről, piackódokról, válaszobjektumokról, hibákról és egyebekről a [Bing Web API v7-es](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) hivatkozása.

## <a name="response-json"></a>Válasz JSON

A webes keresési kérelemre adott válasz tartalmazza az összes találatot JSON-objektumként. Az eredmény elemzéséhez olyan eljárásokra van szükség, amelyek kezelik az egyes típusok elemeit. Tekintse meg az [oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) és a [forráskód](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) példákat.

## <a name="next-steps"></a>További lépések

A **Bing** API-k támogatják azokat a keresési műveleteket, amelyek típusuk nak megfelelően adják vissza az eredményeket.Minden keresési végpont json válaszobjektumként adja vissza az eredményeket. Minden végpont támogatja azokat a lekérdezéseket, amelyek hosszúság, szélesség és keresési sugár szerint egy adott nyelvet és helyet adnak vissza.

Az egyes végpontok által támogatott paraméterekről az egyes típusok referenciaoldalain talál teljes körű tájékoztatást.
A webes keresési API-t használó alapszintű kérelmekről a [Keresés a webes gyorsindítások](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web)című témakörben talál példákat.
