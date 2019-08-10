---
title: Webes keresési végpont
titleSuffix: Azure Cognitive Services
description: A webes keresési API-végpont összefoglalása.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: be622c5636c253c48bec4d67fba58319262c2603
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883566"
---
# <a name="web-search-endpoint"></a>Web Search végpont

A **Web Search API** weblapokat, híreket, képeket, videókat és [entitásokat](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)ad vissza. Az entitások összegző információkkal rendelkeznek egy személyről, helyről vagy témakörről.

## <a name="endpoint"></a>Végpont

Ha a Bing API használatával szeretné lekérni a webes keresési eredményeket `GET` , küldjön egy kérelmet a következő végpontnak. A fejlécek és az URL-paraméterek további specifikációkat határoznak meg.

**Végpont**: Azokat a webes eredményeket adja vissza, amelyek az által `?q=""`definiált felhasználó keresési lekérdezésére vonatkoznak.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Végpont A fejlécekről, a paraméterekről, a piaci kódokról, a válasz objektumokról, a hibákról és egyéb információkról a [Bing web API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) dokumentációjában talál további információt.

## <a name="response-json"></a>Válasz JSON

A webes keresési kérelemre adott válasz a JSON-objektumok összes eredményét tartalmazza. Az eredmény elemzéséhez olyan eljárások szükségesek, amelyek az egyes típusok elemeit kezelik. Példákért [](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) tekintse meg az oktatóanyagot és a [forráskódot](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) .

## <a name="next-steps"></a>További lépések

A **Bing** API-k olyan keresési műveleteket támogatnak, amelyek típusaik alapján adják vissza az eredményeket. A keresési végpontok az eredményeket JSON-válasz objektumokként adják vissza.  Minden végpont támogatja a lekérdezéseket, amelyek adott nyelvet és helyet adnak vissza a földrajzi hosszúság, a szélesség és a keresési sugár alapján.

Az egyes végpontok által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes típusok hivatkozási oldalait.
Példák a webes keresési API-t használó alapszintű kérelmekre: [Keresés a weben – gyors indítás](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
