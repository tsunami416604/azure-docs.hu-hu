---
title: Az Bing Entity Search API végpont
titleSuffix: Azure Cognitive Services
description: A Bing Entity Search API egy végponttal rendelkezik, amely egy lekérdezés alapján a webes entitásokat adja vissza. Ezeket a keresési eredményeket a rendszer a JSON-ban adja vissza.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: e7f5af42d0bb0079746cc9e64b621adfebd565d1
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072671"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing Entity Search API végpont


A Bing Entity Search API egy végponttal rendelkezik, amely egy lekérdezés alapján a webes entitásokat adja vissza. Ezeket a keresési eredményeket a rendszer a JSON-ban adja vissza.

## <a name="get-entity-results-from-the-endpoint"></a>Entitás eredményeinek beolvasása a végpontból

Ha az entitások eredményeit a **BING API**használatával szeretné lekérni, küldjön egy `GET` kérelmet a következő végpontnak. A keresési kérelem személyre szabásához használja a [fejléceket](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) és a [lekérdezési paramétereket](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) . A keresési kérelmeket a `?q=` paraméterrel lehet elküldeni.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Mi a Bing Entity Search API?](overview.md)

## <a name="see-also"></a>Lásd még 

További információ a fejlécekről, a paraméterekről, a piaci kódokról, a válaszok objektumairól és a hibákról: [Bing Entity Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) -útmutató.
