---
title: Automatikus kiegészítési végpont |} Microsoft Docs
description: Az automatikus kiegészítési API-végpont összegzését.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 5aaddd09006cb6f1812bb6ae213a2f5e6720fb1b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348946"
---
# <a name="autosuggest-endpoint"></a>Automatikus kiegészítési végpont

A **automatikus kiegészítési API** egy végpontot, javasolt lekérdezések listáját adja vissza egy részleges keresési kifejezést tartalmaz.

## <a name="endpoint"></a>Végpont

Ahhoz, hogy a Bing API használata javasolt lekérdezések, küldjön egy `GET` kérelem a következő végponthoz. A fejlécek és URL-cím paraméterek segítségével további specifikációk meghatározása.

**Végpont:** értéket ad vissza, amely kapcsolódik a felhasználói bemenetet határozza meg a JSON-eredményeket keresése, javaslatok `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

A fejlécek, a paraméterek, a piaci kódok, a válasz objektumok, a hibák, vonatkozó további információért stb, tekintse meg a [Bing automatikus kiegészítési API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference) hivatkozás.

## <a name="response-json"></a>Válasz JSON

Az automatikus javaslatokba irányuló kérelemre adott válasz eredmények JSON-objektumként tartalmazza. További példák a eredmények értelmezése: a [oktatóanyag](tutorials/autosuggest.md) és [forráskód](tutorials/autosuggest-source.md).

## <a name="next-steps"></a>További lépések

A **Bing** API-k támogatják a keresési eredményeket a alapján műveleteket. Az összes keresési végpontok JSON-válasz objektumként adja vissza az eredményeket.
Minden végpontok egy adott nyelvhez és/vagy a hely által a földrajzi hosszúság értéke, a szélességi és a keresési radius visszaadó lekérdezések támogatja.

A végpontok által támogatott paraméterekkel kapcsolatos részletes információkért lásd: az egyes referencia oldalak.
Az automatikus kiegészítési API-val alapvető kérelmek, tekintse meg a [automatikus kiegészítési Quickstarts](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).