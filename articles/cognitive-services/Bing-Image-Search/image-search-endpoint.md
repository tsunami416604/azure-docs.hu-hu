---
title: Keresési végpontok kép |} Microsoft Docs
description: A kép keresési API-végpont összegzését.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 11/30/2017
ms.author: v-gedod
ms.openlocfilehash: 0d5f28bfdb45b27b04df068f75e8a20d0235d12b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346883"
---
# <a name="image-search-endpoints"></a>Kép keresési végpontok
A **kép Search API** három végpontok tartalmazza.  1. végpont képek lekérdezés alapján a webkiszolgálóról adja vissza. 2. végpont adja vissza [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse).  Végpont 3 trendekkel képek adja vissza.
## <a name="endpoints"></a>Végpontok
A Bing API-jával kép eredményekhez juthat, kérelmet küld a következő végpont közül. A fejlécek és URL-cím paraméterek segítségével további specifikációk meghatározása.

**1. végpont:** adja vissza, amely kapcsolódik a felhasználói keresési lekérdezés határozzák meg a képek `?q=""`.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**2. végpont:** adja vissza a képet, vagy észrevételeket `GET` vagy `POST`.
``` 
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
GET kérés kép, például a lemezképet tartalmazó weblapokra észrevételeket adja vissza. Tartalmazza a [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) paraméterrel egy `GET` kérelmet.

Vagy megadhat egy bináris törzsében egy `POST` igényelhet, és állítsa be a [modulok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) paramétert `RecognizedEntities`. Ez visszaadja azt egy [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v5-reference#insightstoken) használni egy későbbi paraméterként `GET` kérelmet, amelyet a kép személyek információt ad vissza.  Állítsa be `modules` való `All` összes elemzések lekérése, kivéve a `RecognizedEntities` eredményei a `POST` anélkül, hogy egy másik hívás használatával a `insightsToken`. 


**3. végpont:** értéket ad vissza lemezképeket, amelyek trendek mások által keresési kérelmek alapján. A lemezképek egymástól különböző kategóriákba például méltó személyek és események alapján.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Amely támogatja a trendekkel lemezképeket piacok listájáért lásd: [trendek képek](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

A fejlécek, a paraméterek, a piaci kódok, a válasz objektumok, a hibák, vonatkozó további információért stb, tekintse meg a [Bing kép Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) hivatkozás.
## <a name="response-json"></a>Válasz JSON
Az egy kép keresési irányuló kérelemre adott válasz eredmények JSON-objektumként tartalmazza. További példák a eredmények értelmezése: a [oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) és [forráskód](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>További lépések
A **Bing** API-k támogatják a keresési eredményeket a alapján műveleteket. Az összes keresési végpontok JSON-válasz objektumként adja vissza az eredményeket.  Minden végpontok egy adott nyelvhez és/vagy a hely által a földrajzi hosszúság értéke, a szélességi és a keresési radius visszaadó lekérdezések támogatja.

A végpontok által támogatott paraméterekkel kapcsolatos részletes információkért lásd: az egyes referencia oldalak.
A lemezkép-keresési API alapvető kérelmek, tekintse meg a [kép keresési gyors-elindul](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).