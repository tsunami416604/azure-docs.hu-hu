---
title: Kép keresési végpontok – a Bing Image Search API
titleSuffix: Azure Cognitive Services
description: A Bing Image Search API elérhető végpontok listája.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 11/30/2017
ms.author: v-gedod
ms.openlocfilehash: ca38943908bf3eee04c40cf4decf81fd20b08a1f
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295920"
---
# <a name="image-search-endpoints"></a>Lemezkép-végpontok keresése

A **Image Search API** három végpontokat tartalmazza.  1. végpont képeket a weben lekérdezés alapján adja vissza. 2. végpont adja vissza [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse).  Végpont 3 felkapott képeket ad vissza.
## <a name="endpoints"></a>Végpontok
A Bing API-val képtalálatok lekéréséhez kérést küldhet az alábbi végpontok egyikét. A fejlécek és URL-paraméterek használatával további specifikációk meghatározása.

**1. végpont:** adja vissza, amely a felhasználó keresési lekérdezés által meghatározott a lemezképek `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**2. végpont:** adja vissza a képet, vagy észrevételeket `GET` vagy `POST`.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Egy GET kéréssel információival, kép, például a lemezképet tartalmazó weblapokra adja vissza. Tartalmazza a [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) paraméterrel egy `GET` kérelmet.

Vagy megadhat egy bináris törzsében egy `POST` kérelem, és állítsa be a [modulok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) paramétert `RecognizedEntities`. Ez visszaad egy [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v5-reference#insightstoken) használni egy későbbi paraméterként `GET` kérelmet, amelyet a képen személyek információt ad vissza.  Állítsa be `modules` a `All` összes elemzések lekérése, kivéve a `RecognizedEntities` eredményei a `POST` anélkül, hogy egy másik átlépéséhez használja a `insightsToken`.


**3. végpont:** értéket ad vissza rendszerképek pedig kedvelheti alapján a mások által végrehajtott keresési kéréseket. A képek elkülönül egymástól különböző kategóriákba például méltó személyek és események alapján.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Olyan piacon, amely támogatja a felkapott képek egy listája: [népszerű képek](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

További információk a fejlécek, paraméterek, piaci kódok, válaszobjektumok, hibák stb, tekintse meg a [a Bing Image Search API 7-es verziója](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) hivatkozás.
## <a name="response-json"></a>Válasz JSON
A válasz egy lemezkép-keresési kérelem eredményeket JSON-objektumként tartalmazza. Elemzési eredmények példát talál a [oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) és [forráskódját](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>További lépések
A **Bing** API-kat támogatja a keresési műveleteket, amelyek a típusuk eredményeket adja vissza. Végpontok keresése az összes eredmény JSON-válasz objektumként adja vissza.  Minden végpontok lekérdezések támogatására, amelyeket egy adott nyelvhez és/vagy a helyet a földrajzi hosszúság, szélesség és keresési radius vissza.

Minden végpont által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes referenciaoldalait.
A képkeresési API-t használó alapszintű kérések példákért lásd [Képkeresés használatának első lépései](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
