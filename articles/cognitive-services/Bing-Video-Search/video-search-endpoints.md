---
title: Videókeresési végpontok – a Bing Videókeresés
titlesuffix: Azure Cognitive Services
description: A Bing Videókeresési API-végpont összefoglalása.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: rosh
ms.openlocfilehash: db65f0ec857b2d69a2d36a22a1d9e3676af91683
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190631"
---
# <a name="video-search-endpoints"></a>Videó végpontok keresése
A **Video Search API** három végpontokat tartalmazza.  1. végpont videókat a weben lekérdezés alapján adja vissza. 2. végpont alapján videó észrevételeket adja vissza a `modules` URL paramétert.  Végpont 3 felkapott képeket ad vissza.

## <a name="endpoints"></a>Végpontok
A Bing API-val videó eredményeinek beolvasása, küldjön egy `GET` kérelem a következő végpontok egyikéhez. A fejlécek és URL-paraméterek használatával további specifikációk meghatározása.

**Endpoint1:** Adja vissza, amely a felhasználó keresési lekérdezés által meghatározott a videók `?q=""`.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```

**2. végpont:** Adja vissza egy videót, például a kapcsolódó videók információival. Tartalmazza a `modules` [lekérdezési paraméter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query-parameters), azaz kéréséhez insights vesszővel tagolt listája.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details
```

**3. végpont:** Értéket ad vissza videók, amelyek pedig kedvelheti keresési kéréseket a mások által végrehajtott alapján. A videók elkülönül egymástól különböző kategóriákba például méltó személyek és események alapján.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending
```

További információk a fejlécek, paraméterek, piaci kódok, válaszobjektumok, hibák stb, tekintse meg a [Bing Video Search API 7-es verziója](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) referencia.
## <a name="response-json"></a>Válasz JSON
A videók keresése kérelemre válaszként eredményeket JSON-objektumként tartalmazza. Elemzési eredmények példát talál a [oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app) és [forráskódját](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app-source).

## <a name="next-steps"></a>További lépések
A **Bing** API-kat támogatja a keresési műveleteket, amelyek a típusuk eredményeket adja vissza. Végpontok keresése az összes eredmény JSON-válasz objektumként adja vissza.  Minden végpontok lekérdezések támogatására, amelyeket egy adott nyelvhez és/vagy a helyet a földrajzi hosszúság, szélesség és keresési radius vissza.

Minden végpont által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes referenciaoldalait.
A Video search API-t használó alapszintű kérések példákért lásd [Video Search használatának első lépései](https://docs.microsoft.com/azure/cognitive-services/bing-video-search).
