---
title: A LUIS előre összeállított entitások százalékos referencia – Azure |} A Microsoft Docs
titleSuffix: Azure
description: Ez a cikk tartalmaz százalékos előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: d445dbf69e3d2163b5d44b894f8795d41fbd34e3
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238964"
---
# <a name="percentage-entity"></a>Százalék entitás
Percenkénti egységeinek törtrészeként, százalékos számok szerepelhetnek `3 1/2`, vagy százalékként, `2%`. Az entitás már be van tanítva, mert nem kell az alkalmazás leképezések százalékos tartalmazó példa beszédmódok hozzáadása. Százalékos entitás támogatott [számos országban](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Százalékos típusai
Százalékos felügyelje a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) Github-adattár

## <a name="resolution-for-prebuilt-percentage-entity"></a>Előre összeállított százalékos entitás feloldása
Az alábbi példa bemutatja a feloldása a **builtin.percentage** entitás.

```JSON
{
  "query": "set a trigger when my stock goes up 2%",
  "topScoringIntent": {
    "intent": "SetTrigger",
    "score": 0.971157849
  },
  "intents": [
    {
      "intent": "SetTrigger",
      "score": 0.971157849
    }
  ],
  "entities": [
    {
      "entity": "2%",
      "type": "builtin.percentage",
      "startIndex": 36,
      "endIndex": 37,
      "resolution": {
        "value": "2%"
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

További információ a [sorszámnál](luis-reference-prebuilt-ordinal.md), [szám](luis-reference-prebuilt-number.md), és [hőmérséklet](luis-reference-prebuilt-temperature.md) entitásokat. 