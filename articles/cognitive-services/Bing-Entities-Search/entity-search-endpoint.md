---
title: Az Bing Entity Search API végpont
titleSuffix: Azure Cognitive Services
description: Ismerje meg az Bing Entity Search API végpontot, és küldje el a kérelmeket.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9d08091d0ea6869d13e294e60454f85a84f672ad
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424052"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing Entity Search API végpont


A Bing Entity Search API egy végponttal rendelkezik, amely egy lekérdezés alapján a webes entitásokat adja vissza. Ezeket a keresési eredményeket a rendszer a JSON-ban adja vissza.

## <a name="get-entity-results-from-the-endpoint"></a>Entitás eredményeinek beolvasása a végpontból

Ha az entitások eredményeit a **Bing API**használatával szeretné lekérni, küldjön egy `GET` kérelmet a következő végpontnak. A [](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) keresési kérelem személyre szabásához használja a fejléceket és a [lekérdezési paramétereket](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) . A keresési kérelmeket a `?q=` paraméter használatával lehet elküldeni.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Mi a Bing Entity Search API?](overview.md)

## <a name="see-also"></a>Lásd még 

További információ a fejlécekről, a paraméterekről, a piaci kódokról, a válaszok objektumairól és a hibákról: [Bing Entity Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) -útmutató.
