---
title: Webes keresési végpont
titleSuffix: Azure Cognitive Services
description: A webes keresési eredmények beszerzéséhez küldjön `GET` kérelmet a következő végpontnak. A fejlécek és az URL-paraméterek további specifikációkat határoznak meg.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: 2edc6a4cf4d4fb174c1b2067d8b3fb3b6f31859f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111339"
---
# <a name="web-search-endpoint"></a>Web Search végpont

A **Web Search API** weblapokat, híreket, képeket, videókat és [entitásokat](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)ad vissza. Az entitások összegző információkkal rendelkeznek egy személyről, helyről vagy témakörről.

## <a name="endpoint"></a>Végpont

Ha a Bing API használatával szeretné lekérni a webes keresési eredményeket, küldjön egy `GET` kérelmet a következő végpontnak. A fejlécek és az URL-paraméterek további specifikációkat határoznak meg.

**Végpont**: a `?q=""`által definiált felhasználó keresési lekérdezéséhez kapcsolódó webes eredményeket adja vissza.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Végpont: a fejlécek, a paraméterek, a piaci kódok, a válasz objektumok, a hibák és egyéb információk részleteiért tekintse meg a [Bing web API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) dokumentációját.

## <a name="response-json"></a>Válasz JSON

A webes keresési kérelemre adott válasz a JSON-objektumok összes eredményét tartalmazza. Az eredmény elemzéséhez olyan eljárások szükségesek, amelyek az egyes típusok elemeit kezelik. Példákért tekintse meg az [oktatóanyagot](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) és a [forráskódot](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) .

## <a name="next-steps"></a>További lépések

A **Bing** API-k olyan keresési műveleteket támogatnak, amelyek típusaik alapján adják vissza az eredményeket. A keresési végpontok az eredményeket JSON-válasz objektumokként adják vissza.  Minden végpont támogatja a lekérdezéseket, amelyek adott nyelvet és helyet adnak vissza a földrajzi hosszúság, a szélesség és a keresési sugár alapján.

Az egyes végpontok által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes típusok hivatkozási oldalait.
Példák a webes keresési API-t használó alapszintű kérelmekre: [Keresés a weben – gyors indítás](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
