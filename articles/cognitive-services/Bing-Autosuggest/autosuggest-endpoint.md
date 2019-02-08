---
title: Bing Autosuggest-végpont
titlesuffix: Azure Cognitive Services
description: A Bing Autosuggest API-végpont összefoglalása.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 9e80e81a79f45f132baf07af7c2e7be8f98ceec4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857549"
---
# <a name="bing-autosuggest-endpoint"></a>Bing Autosuggest-végpont

A **a Bing Autosuggest API** tartalmaz egy végpont, amely egy részleges keresési kifejezést a javasolt lekérdezések listáját adja vissza.

## <a name="endpoint"></a>Végpont

Javasolt lekérdezések, a Bing API-val beszerzéséhez küldjön egy `GET` kérelem a következő végpont. A fejlécek és URL-paraméterek használatával további specifikációk meghatározása.

**Végpont:** Értéket ad vissza JSON eredményként, amely a felhasználói bevitel határozzák meg a keresési javaslatok `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

További információk a fejlécek, paraméterek, piaci kódok, válaszobjektumok, hibák stb, tekintse meg a [a Bing Autosuggest API 7-es verziója](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference) referencia.

## <a name="response-json"></a>Válasz JSON

Az olyan automatikus kiegészítés irányuló kérelemre adott válasz JSON-objektumként eredményeit tartalmazza. Elemzési eredmények példát talál a [oktatóanyag](tutorials/autosuggest.md) és [forráskódját](tutorials/autosuggest-source.md).

## <a name="next-steps"></a>További lépések

A **Bing** API-kat támogatja a keresési műveleteket, amelyek a típusuk eredményeket adja vissza. Végpontok keresése az összes eredmény JSON-válasz objektumként adja vissza.
Minden végpontok lekérdezések támogatására, amelyeket egy adott nyelvhez és/vagy a helyet a földrajzi hosszúság, szélesség és keresési radius vissza.

Minden végpont által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes referenciaoldalait.
Az automatikus kiegészítési API-t használó alapszintű kérések példákért lásd [Autosuggest útmutatóink](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).
