---
title: A LUIS előre összeállított entitások kor referencia – Azure |} A Microsoft Docs
titleSuffix: Azure
description: Ez a cikk tartalmaz kora előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 63ff00c29548af2f26a2b7b5e5ac68b5397671ec
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441825"
---
# <a name="age-entity"></a>Kor entitás
Az előre összeállított kora entitás age értékét egyaránt numerikusan és tekintetében nap, hét, hónap és év rögzíti. Az entitás már be van tanítva, mert nem kell kora, az alkalmazás leképezések tartalmazó példa beszédmódok hozzáadása. Kor entitás támogatott [számos országban](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Kor típusai
Kor felügyelje a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) Github-adattár

## <a name="resolution-for-prebuilt-age-entity"></a>Előre összeállított kora entitás feloldása
Az alábbi példa bemutatja a feloldása a **builtin.age** entitás.

```JSON
{
  "query": "A 90 day old utilities bill is quite late.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8236133
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8236133
    }
  ],
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

További információ a [pénznem](luis-reference-prebuilt-currency.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), és [dimenzió](luis-reference-prebuilt-dimension.md) entitásokat. 