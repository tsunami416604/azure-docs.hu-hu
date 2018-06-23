---
title: Entitás keresési végpont |} Microsoft Docs
description: Az entitás keresési API-végpont összegzését.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: a2557c6000445544b3b47a05d7d356ccaa9928b4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346886"
---
# <a name="entity-search-endpoint"></a>Entitás keresési végpont
A **entitás Search API** , hogy a lekérdezés alapján a webkiszolgálóról entitásokat ad vissza egy végpontot tartalmaz.

## <a name="endpoint"></a>Végpont
Használatával eredmények lekérése entitás a **Bing API**, küldjön egy `GET` kérelem a következő végponthoz. A fejlécek és URL-cím paraméterek segítségével további specifikációk meghatározása.

**Végpont:** adja vissza, amely kapcsolódik a határozzák meg a felhasználó keresési lekérdezés entitások `?q=""`.
```
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

A fejlécek, a paraméterek, a piaci kódok, a válasz objektumok, a hibák, vonatkozó további információért stb, tekintse meg a [Bing entitás Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) hivatkozás.

## <a name="response-json"></a>Válasz JSON
Az egy entitás keresési irányuló kérelemre adott válasz eredmények JSON-objektumként tartalmazza. Az eredményeket, tekintse meg a [Ismerkedés](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start).

## <a name="next-steps"></a>További lépések
A **Bing** API-k támogatják a keresési eredményeket a alapján műveleteket. Az összes keresési végpontok JSON-válasz objektumként adja vissza az eredményeket.  Minden végpontok egy adott nyelvhez és/vagy a hely által a földrajzi hosszúság értéke, a szélességi és a keresési radius visszaadó lekérdezések támogatja.

A végpontok által támogatott paraméterekkel kapcsolatos részletes információkért lásd: az egyes referencia oldalak.
Az entitás-keresési API-t, tekintse meg a [Ismerkedés](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start) és [Resizing és a vágás miniatűr képeket](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/resize-and-crop-thumbnails).