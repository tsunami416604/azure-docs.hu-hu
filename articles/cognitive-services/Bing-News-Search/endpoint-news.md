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
ms.date: 1/10/2019
ms.author: v-gedod
ms.openlocfilehash: cc38dff7cd3ae8d10d8a20cbf98749d724de1ee6
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262812"
---
# <a name="bing-news-search-api-endpoints"></a>A Bing News Search API-végpontok

A **News Search API** adja vissza a hírek cikkeket, weblapok, képek, videók, és [entitások](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entitás tartalmaz egy személy, hely vagy a témakör összefoglaló adatait.

## <a name="endpoints"></a>Végpontok

Beolvasása hírek keresési eredmények a Bing News Search API segítségével, küldjön egy `GET` kérelem a következő végpontok egyikéhez. További specifikációk meghatározása a fejlécek és URL-paraméter.

### <a name="news-items-by-search-query"></a>Keresési lekérdezés híreket

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Keresési lekérdezés alapján hírek elemeket adja vissza. Ha a keresési lekérdezés üres, akkor az API különböző kategóriák legfontosabb híreket visszaadása. Kódolás a keresési kifejezést, és való hozzáfűzésével URL-cím alapján egy lekérdezés küldése a`q=""` paraméter. Tekintse meg a rendelkezésre állás érdekében [támogatott országok és piacok](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Híreket elemek kategória szerint

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Kategória szerint a híreket elemeket adja vissza. Kifejezetten kérheti a legfontosabb üzleti, sport vagy értéket használó cikkeket Szórakozás `category=business`, `category=sports`, vagy `category=entertainment`.  A `category` paraméter csak akkor használható együtt a `/news` URL-CÍMÉT. Néhány hivatalos követelmények megadása a kategóriák; Tekintse meg `category` a a [lekérdezési paraméter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters) dokumentációját. Kódolás a keresési kifejezést, és való hozzáfűzésével URL-cím alapján egy lekérdezés küldése a`q=""` paraméter. Tekintse meg a rendelkezésre állás érdekében [támogatott országok és piacok](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Népszerű hírek témakörök 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Hírtémakörök, hogy a rendszer jelenleg népszerű lett a közösségi hálózatokkal adja vissza. Ha a `/trendingtopics` beállítás részét képezi, a Bing search figyelmen kívül hagyja a számos más paramétereket, mint például `freshness` és `?q=""`. Tekintse meg a rendelkezésre állás érdekében [támogatott országok és piacok](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>További lépések

További információk a fejlécek, paraméterek, piaci kódok, válaszobjektumok, hibák stb, tekintse meg a [Bing News search API 7-es verziója](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) referencia.

Minden végpont által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes referenciaoldalait.
A News search API-alapszintű kérések példákért lásd [Bing News Search használatának első lépései](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
