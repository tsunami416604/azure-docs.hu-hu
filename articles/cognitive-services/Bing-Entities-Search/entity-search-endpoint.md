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
ms.openlocfilehash: 5d6b0a6be0d99815c4d14bb04e56241d011812b5
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93084952"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing Entity Search API végpont

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](https://aka.ms/cogsvcs/bingmove)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](https://aka.ms/cogsvcs/bingmigration).


A Bing Entity Search API egy végponttal rendelkezik, amely egy lekérdezés alapján a webes entitásokat adja vissza. Ezeket a keresési eredményeket a rendszer a JSON-ban adja vissza.

## <a name="get-entity-results-from-the-endpoint"></a>Entitás eredményeinek beolvasása a végpontból

Ha az entitások eredményeit a **BING API** használatával szeretné lekérni, küldjön egy `GET` kérelmet a következő végpontnak. A keresési kérelem személyre szabásához használja a [fejléceket](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) és a [lekérdezési paramétereket](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) . A keresési kérelmeket a paraméter használatával lehet elküldeni `?q=` .

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Mi a Bing Entity Search API?](overview.md)

## <a name="see-also"></a>Lásd még 

További információ a fejlécekről, a paraméterekről, a piaci kódokról, a válaszok objektumairól és a hibákról: [Bing Entity Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) -útmutató.
