---
title: A Bing képkeresési API végpontjai
titleSuffix: Azure Cognitive Services
description: A képkeresési API három végpontot tartalmaz. Az 1-es végpont képeket ad vissza az internetről. A 2-es végpont eredménye az ImageInsights. A 3-as végpont függvény trendképeket ad vissza.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 38416f6a580d270aefc287de0c198bd418a44db9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072633"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>A Bing képkeresési API végpontjai

A **képkeresési API** három végpontot tartalmaz.  Az 1- es végpont lekérdezés alapján visszaadja a webről származó képeket. A 2-es végpont eredménye az [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  A 3-as végpont függvény trendképeket ad vissza.

## <a name="endpoints"></a>Végpontok

Ha a Bing API használatával szeretne képeredményeket kapni, küldjön egy kérést az alábbi végpontok egyikére. A fejlécek és az URL-paraméterek segítségével további specifikációkat határozhatja meg.

**1. végpont:** A felhasználó által `?q=""`definiált keresési lekérdezéséhez kapcsolódó képeket ad eredményül.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**2. végpont:** A képelemzési adatokat `GET` `POST`a vagy a használatával ad eredményül.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
A GET-kérelem elemzési adatokat ad vissza egy képről, például a képet tartalmazó weblapokról. Az [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) paraméter `GET` t.

Vagy felvehet egy bináris lemezképet a `POST` kérelem törzsébe, `RecognizedEntities`és [beállíthatja](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) a modulok paraméterét . Ez egy [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) függvényt ad vissza `GET` paraméterként egy későbbi kérelemben, amely a rendszerképben lévő személyek adatait adja vissza.  Állítsa `modules` `All` be, hogy az `RecognizedEntities` összes betekintést, `POST` kivéve az `insightsToken`eredményeket a anélkül, hogy egy másik hívást a .


**3. végpont:** A mások által küldött keresési kérelmek alapján felkapott képeket ad vissza. A képek különböző kategóriákba sorolhatók, például figyelemre méltó emberek vagy események alapján.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

A felkapott képeket támogató piacok listáját a [Felkapott képek (Felkapott képek) (Felkapott képek) (Felkapott képek) (Felkapott képek) (Felkapott képek) (Felkapott képek) (Felkapott képek)](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)témakörben található

A fejlécekről, paraméterekről, piaci kódokról, válaszobjektumokról, hibákról stb. [Bing Image Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
## <a name="response-json"></a>Válasz JSON
A képkeresési kérelemre adott válasz JSON-objektumokként tartalmazza az eredményeket. Az eredmények elemzésével például az [oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) és a [forráskód látható.](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source)

## <a name="next-steps"></a>További lépések
A **Bing** API-k támogatják azokat a keresési műveleteket, amelyek típusuk nak megfelelően adják vissza az eredményeket.Minden keresési végpont json válaszobjektumként adja vissza az eredményeket. Minden végpont támogatja azokat a lekérdezéseket, amelyek hosszúság, szélesség és keresési sugár szerint egy adott nyelvet és/vagy helyet adnak vissza.

Az egyes végpontok által támogatott paraméterekről az egyes típusok referenciaoldalain talál teljes körű tájékoztatást.
A Képkeresési API-t használó alapszintű kérelmekről a [Képkeresés gyorsindítása](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web)című témakörben talál példákat.
