---
title: Kor előre összeállított entitások
titleSuffix: Azure
description: Ez a cikk tartalmaz kora előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 7644f2c4f4479ae234dc2d4b501be17455230bde
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217849"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazásokon előre összeállított entitások életkor
Az előre összeállított kora entitás age értékét egyaránt numerikusan és tekintetében nap, hét, hónap és év rögzíti. Az entitás már be van tanítva, mert nem kell kora, az alkalmazás leképezések tartalmazó példa beszédmódok hozzáadása. Kor entitás támogatott [számos országban](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Kor típusai
Kor felügyelje a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) GitHub-adattár

## <a name="resolution-for-prebuilt-age-entity"></a>Előre összeállított kora entitás feloldása
Az alábbi példa bemutatja a feloldása a **builtin.age** entitás.

```json
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
