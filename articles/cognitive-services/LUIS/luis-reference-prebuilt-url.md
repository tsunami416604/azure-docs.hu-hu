---
title: URL-cím előre összeállított entitások
titleSuffix: Azure
description: Ez a cikk URL-címet tartalmaz az előre összeállított Entitásadatok a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 5a1d86c58b6b6b8b54d170459157371e8e73f0e8
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163667"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>URL-címet a LUIS-alkalmazásokon előre összeállított entitások
Entitás URL-cím tartománynév vagy IP-címek URL-címek kibontása. Az entitás már be van tanítva, mert nem kell az alkalmazás URL-címeket tartalmazó példa beszédmódok hozzáadása. URL-cím entitás támogatott `en-us` culture csak. 

## <a name="types-of-urls"></a>URL-címeinek típusai
URL-cím felügyelje a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) GitHub-adattár

## <a name="resolution-for-prebuilt-url-entity"></a>Előre összeállított URL-cím entitás feloldása
Az alábbi példa bemutatja a feloldása a **builtin.url** entitás.

```json
{
  "query": "http://www.luis.ai is a great cognitive services example of artificial intelligence",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.781975448
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.781975448
    }
  ],
  "entities": [
    {
      "entity": "http://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

További információ a [sorszámnál](luis-reference-prebuilt-ordinal.md), [szám](luis-reference-prebuilt-number.md), és [hőmérséklet](luis-reference-prebuilt-temperature.md) entitásokat.