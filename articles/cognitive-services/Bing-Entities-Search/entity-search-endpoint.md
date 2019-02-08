---
title: A Bing Entity Search API-végpont
titlesuffix: Azure Cognitive Services
description: További tudnivalók a Bing Entity Search API-végpont, és a kéréseket küldjön.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 3c2aa4b22c8e679f73692978d9e1f8009f11a46b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875229"
---
# <a name="bing-entity-search-api-endpoint"></a>A Bing Entity Search API-végpont


A Bing Entity Search API tartalmaz egy végpontot, amely entitásokat ad vissza, a webről lekérdezés alapján. A keresési eredmények a rendszer JSON formátumban adja vissza.

## <a name="get-entity-results-from-the-endpoint"></a>A végpontról entitás eredményeinek beolvasása

Használatával eredmények lekérése entitás a **Bing-API**, küldjön egy `GET` kérelem a következő végpont. Használat [fejlécek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers) és [lekérdezési paramétereket](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters) szabhatja testre a keresési kérelmet. Keresési kérelmek használatával küldhető a `?q=` paraméter.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Mi az a Bing Entity Search API?](overview.md)

## <a name="see-also"></a>Lásd még 

A fejlécek, paraméterek, piaci kódok, válaszobjektumok, hibák és további kapcsolatos további információkért lásd: a [Bing Entity Search API 7-es verziója](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) áttekintésével foglalkozó cikkben.
