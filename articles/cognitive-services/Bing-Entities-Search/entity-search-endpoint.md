---
title: A Bing Entity Search API-végpont
titlesuffix: Azure Cognitive Services
description: További tudnivalók a Bing Entity Search API-végpont, és a kéréseket küldjön.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: cae65c8fb7deb9f68a297de2058a86249b60136b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753980"
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
