---
title: A Bing entitáskeresési API-végpont
titleSuffix: Azure Cognitive Services
description: A Bing Entity Search API-nak van egy végpontja, amely egy lekérdezés alapján adja vissza az entitásokat a webről. Ezeket a keresési eredményeket a JSON adja vissza.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: e7f5af42d0bb0079746cc9e64b621adfebd565d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072671"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing entitáskeresési API-végpont


A Bing Entity Search API-nak van egy végpontja, amely egy lekérdezés alapján adja vissza az entitásokat a webről. Ezeket a keresési eredményeket a JSON adja vissza.

## <a name="get-entity-results-from-the-endpoint"></a>Entitáseredmények beszereznie a végpontból

Entitáseredmények bekérése a Bing API `GET` **használatával,** küldjön egy kérést a következő végpontra. A keresési kérelem testreszabásához [használjon fejléceket](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) és [lekérdezési paramétereket.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) A keresési kérelmek a `?q=` paraméter használatával küldhetők el.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Mi a Bing Entity Search API?](overview.md)

## <a name="see-also"></a>Lásd még 

A fejlécekről, paraméterekről, piackódokról, válaszobjektumokról, hibákról és egyebekről további információt a [Bing Entity Search API v7 referenciacikkében](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) talál.
