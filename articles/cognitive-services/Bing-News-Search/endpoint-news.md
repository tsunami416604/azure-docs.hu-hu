---
title: Bing hírek keresési végpontok |} Microsoft Docs
description: A hírek keresési API-végpont összegzését.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: b8ae99698926a818bf22b0b4027af8610413aaa6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346958"
---
# <a name="news-search-endpoints"></a>Hírek keresési végpontok
A **hírek Search API** adja vissza a hírek cikkeket, a weblapokat, képek, videók, és [entitások](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entitások egy személy, hely vagy a témakör összefoglaló információkat tartalmaznak. 
## <a name="endpoints"></a>Végpontok
Ahhoz, hogy a Bing API-jával hírek találatok, küldjön egy `GET` kérelem a következő végpont közül. További specifikációk meghatározása a fejlécek és URL-cím paramétereket.

**1. végpont:** adja vissza a felhasználói keresési lekérdezés alapján híreket. Ha a keresési lekérdezés üres, a hívást a felső hírcikkeket adja vissza. A lekérdezés `?q=""` lehetőséget is használható a `/news` URL-CÍMÉT. A rendelkezésre állás érdekében tekintse meg a [támogatott országokban és piacok](supported-countries-markets.md#supported-markets-for-news-search-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search 
``` 

**2. végpont:** adja vissza a híreket elemek kategória szerint. Kifejezetten kérheti a legfontosabb üzleti, sport vagy Szórakozás cikkek használják `category=business`, `category=sports`, vagy `category=entertainment`.  A `category` paraméter csak akkor használható a `/news` URL-CÍMÉT. Nincsenek néhány formális a kategóriák; megadása Tekintse meg `category` a a [lekérdezésparaméter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters) dokumentációját. A rendelkezésre állás érdekében tekintse meg a [támogatott országokban és piacok](supported-countries-markets.md#supported-markets-for-news-endpoint). 
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

**3. végpont:** értéket ad vissza hírek foglalkozó témakörök, amelyek a rendszer jelenleg trendek a közösségi hálózatokkal. Ha a `/trendingtopics` beállítás található, a Bing keresési figyelmen kívül hagyja a számos más paraméter, például a `freshness` és `?q=""`. A rendelkezésre állás érdekében tekintse meg a [támogatott országokban és piacok](supported-countries-markets.md#supported-markets-for-news-trending-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics 
``` 

A fejlécek, a paraméterek, a piaci kódok, a válasz objektumok, a hibák, vonatkozó további információért stb, tekintse meg a [hírek Bing keresési API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) hivatkozás.
## <a name="response-json"></a>Válasz JSON
Az egy hírek keresési irányuló kérelemre adott válasz eredmények JSON-objektumként tartalmazza. Az eredmények értelmezése szükséges eljárásokat, amelyek minden típusú elemeket kezeli. Tekintse meg a [oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app) és [forráskód](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app-source) példákat.

## <a name="next-steps"></a>További lépések
A **Bing** API-k támogatják a keresési eredményeket a alapján műveleteket. Az összes keresési végpontok JSON-válasz objektumként adja vissza az eredményeket.  Minden végpontok egy adott nyelvhez és/vagy a hely által a földrajzi hosszúság értéke, a szélességi és a keresési radius visszaadó lekérdezések támogatja.

A végpontok által támogatott paraméterekkel kapcsolatos részletes információkért lásd: az egyes referencia oldalak.
A hírek keresési API alapvető kérelmek, tekintse meg a [Bing hírek keresési gyors-elindul](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).