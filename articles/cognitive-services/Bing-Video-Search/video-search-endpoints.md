---
title: Videó keresési végpontok |} Microsoft Docs
description: A videó keresési API-végpont összegzését.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 12/04/2017
ms.author: v-gedod
ms.openlocfilehash: 9836d9928362ab37b0a81ff5043d99f9bf353f22
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346950"
---
# <a name="video-search-endpoints"></a>Videó keresési végpontok
A **videó Search API** három végpontok tartalmazza.  1. végpont videók lekérdezés alapján a webkiszolgálóról adja vissza. 2. végpont alapján videó áttekintést ad vissza a `modules` URL-paramétert.  Végpont 3 trendekkel képek adja vissza.

## <a name="endpoints"></a>Végpontok
A Bing API-jával videó eredményekhez juthat, küldjön egy `GET` kérelem a következő végpont közül. A fejlécek és URL-cím paraméterek segítségével további specifikációk meghatározása.

**Endpoint1:** adja vissza, amely kapcsolódik a felhasználói keresési lekérdezés határozzák meg a videók `?q=""`.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```

**2. végpont:** videó, például kapcsolódó videók áttekintést ad vissza. Tartalmazza a `modules` [lekérdezésparaméter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query-parameters), vagyis kéréséhez insights vesszővel tagolt listája.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details
```

**3. végpont:** mások által keresési kérelmek alapján vannak trendek videókért értéket ad vissza. A videók egymástól különböző kategóriákba például méltó személyek és események alapján.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending
```

A fejlécek, a paraméterek, a piaci kódok, a válasz objektumok, a hibák, vonatkozó további információért stb, tekintse meg a [Bing videó Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) hivatkozás.
## <a name="response-json"></a>Válasz JSON
A videók keresési kérelemre válaszolva eredmények JSON-objektumként tartalmazza. További példák a eredmények értelmezése: a [oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app) és [forráskód](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app-source).

## <a name="next-steps"></a>További lépések
A **Bing** API-k támogatják a keresési eredményeket a alapján műveleteket. Az összes keresési végpontok JSON-válasz objektumként adja vissza az eredményeket.  Minden végpontok egy adott nyelvhez és/vagy a hely által a földrajzi hosszúság értéke, a szélességi és a keresési radius visszaadó lekérdezések támogatja.

A végpontok által támogatott paraméterekkel kapcsolatos részletes információkért lásd: az egyes referencia oldalak.
A videó keresési API alapvető kérelmek, tekintse meg a [videó keresési gyors-elindul](https://docs.microsoft.com/azure/cognitive-services/bing-video-search).