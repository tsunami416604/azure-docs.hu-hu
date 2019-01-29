---
title: Bing Entity Search-végpont
titlesuffix: Azure Cognitive Services
description: A Bing Entity Search API-végpont összefoglalása.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 4013b1791e4c725469d3f180ae0fdecc9ae2ebba
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55167053"
---
# <a name="entity-search-endpoint"></a>Entity Search-végpont
A **Entity Search API** tartalmaz egy végpontot, amely entitásokat ad vissza, a webről lekérdezés alapján.

## <a name="endpoint"></a>Végpont
Használatával eredmények lekérése entitás a **Bing-API**, küldjön egy `GET` kérelem a következő végpont. A fejlécek és URL-paraméterek használatával további specifikációk meghatározása.

**Végpont:** Adja vissza, amely a felhasználó keresési lekérdezés által meghatározott a entitások `?q=""`.
```
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

További információk a fejlécek, paraméterek, piaci kódok, válaszobjektumok, hibák stb, tekintse meg a [Bing Entity Search API 7-es verziója](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) referencia.

## <a name="response-json"></a>Válasz JSON
Az entity search kérelemre válaszként eredményeket JSON-objektumként tartalmazza. Az eredmények példák: [Ismerkedés](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start).

## <a name="next-steps"></a>További lépések
A **Bing** API-kat támogatja a keresési műveleteket, amelyek a típusuk eredményeket adja vissza. Végpontok keresése az összes eredmény JSON-válasz objektumként adja vissza.  Minden végpontok lekérdezések támogatására, amelyeket egy adott nyelvhez és/vagy a helyet a földrajzi hosszúság, szélesség és keresési radius vissza.

Minden végpont által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes referenciaoldalait.
Az Entity search API-t használó példákért lásd [Ismerkedés](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start) és [átméretezés és a miniatűr képekhez vágása](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/resize-and-crop-thumbnails).
