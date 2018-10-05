---
title: Bing News Search-végpontok
titlesuffix: Azure Cognitive Services
description: A News search API-végpont összefoglalása.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: 0ed8b9048c04c4aff5214cea697810a0c573559e
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48800545"
---
# <a name="bing-news-search-endpoints"></a>Bing News Search-végpontok
A **News Search API** adja vissza a hírek cikkeket, weblapok, képek, videók, és [entitások](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entitás tartalmaz egy személy, hely vagy a témakör összefoglaló adatait.
## <a name="endpoints"></a>Végpontok
A Bing API-val Hírkeresési eredményeket beszerzéséhez küldjön egy `GET` kérelem a következő végpontok egyikéhez. További specifikációk meghatározása a fejlécek és URL-paraméter.

**1. végpont:** a felhasználó keresési lekérdezés alapján hírek elemeket adja vissza. Ha a keresési lekérdezés üres, a hívást a felső hírek adja vissza. A lekérdezés `?q=""` lehetőség is használható a `/news` URL-CÍMÉT. Tekintse meg a rendelkezésre állás érdekében [támogatott országok és piacok](language-support.md#supported-markets-for-news-search-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

**2. végpont:** kategória szerint a híreket elemeket adja vissza. Kifejezetten kérheti a legfontosabb üzleti, sport vagy értéket használó cikkeket Szórakozás `category=business`, `category=sports`, vagy `category=entertainment`.  A `category` paraméter csak akkor használható együtt a `/news` URL-CÍMÉT. Néhány hivatalos követelmények megadása a kategóriák; Tekintse meg `category` a a [lekérdezési paraméter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters) dokumentációját. Tekintse meg a rendelkezésre állás érdekében [támogatott országok és piacok](language-support.md#supported-markets-for-news-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

**3. végpont:** , hogy a rendszer jelenleg népszerű lett a közösségi hálózatokkal hírtémakörök értéket ad vissza. Ha a `/trendingtopics` beállítás részét képezi, a Bing search figyelmen kívül hagyja a számos más paramétereket, mint például `freshness` és `?q=""`. Tekintse meg a rendelkezésre állás érdekében [támogatott országok és piacok](language-support.md#supported-markets-for-news-trending-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

További információk a fejlécek, paraméterek, piaci kódok, válaszobjektumok, hibák stb, tekintse meg a [Bing News search API 7-es verziója](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) referencia.
## <a name="response-json"></a>Válasz JSON
A News search kérelemre válaszként eredményeket JSON-objektumként tartalmazza. Elemzési eredmények szükséges eljárásokat, amelyek minden típusú elemeket kezeli. Tekintse meg a [oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app) és [forráskódját](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app-source) példákat.

## <a name="next-steps"></a>További lépések
A **Bing** API-kat támogatja a keresési műveleteket, amelyek a típusuk eredményeket adja vissza. Végpontok keresése az összes eredmény JSON-válasz objektumként adja vissza.  Minden végpontok lekérdezések támogatására, amelyeket egy adott nyelvhez és/vagy a helyet a földrajzi hosszúság, szélesség és keresési radius vissza.

Minden végpont által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes referenciaoldalait.
A News search API-alapszintű kérések példákért lásd [Bing News Search használatának első lépései](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
