---
title: Webes keresés végpont
titleSuffix: Azure Cognitive Services
description: A webes keresési API-végpont összefoglalása.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: 3058ca6cf0eb99486dd4c269d43b274fb367f7a9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46125459"
---
# <a name="web-search-endpoint"></a>Webes keresés végpont
A **Web Search API** adja vissza a weblapok, hírek, képek, videók, és [entitások](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entitás tartalmaz egy személy, hely vagy a témakör összefoglaló adatait.
## <a name="endpoint"></a>Végpont
Első webes találatok, a Bing API-val, küldjön egy `GET` kérelem a következő végpont. További specifikációk meghatározása a fejlécek és URL-paraméter.

**Végpont**: értéket ad vissza webes találatokat, amely a felhasználó a keresési lekérdezés által meghatározott `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```
Végpont: További információk a fejlécek, paraméterek, a piaci kódokat válaszobjektumok, hibák stb., tekintse meg a [a Bing webes-API 7-es verziója](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) hivatkozást.

## <a name="response-json"></a>Válasz JSON
Keresés webes kérelemre adott válasz JSON-objektumként minden eredmény tartalmazza. Az eredmény-elemzés szükséges eljárásokat, amelyek az egyes elemek kezelésére. Tekintse meg a [oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) és [forráskódját](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app-source) példákat.

## <a name="next-steps"></a>További lépések
A **Bing** API-kat támogatja a keresési műveleteket, amelyek a típusuk eredményeket adja vissza. Végpontok keresése az összes eredmény JSON-válasz objektumként adja vissza.  Minden végpontok lekérdezések támogatására, amelyeket egy adott nyelvhez és/vagy a helyet a földrajzi hosszúság, szélesség és keresési radius vissza.

Minden végpont által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes referenciaoldalait.
A webes keresési API-t használó alapszintű kérések példákért lásd [keresés a webes használatának első lépései](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
