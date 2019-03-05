---
title: Előre összeállított entitások százalékos aránya
titleSuffix: Azure
description: Ez a cikk tartalmaz százalékos előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: b55aca79047a224a9e1a474afdabf43e2701872d
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341742"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Százalék a LUIS-alkalmazásokon előre összeállított entitások
Percenkénti egységeinek törtrészeként, százalékos számok szerepelhetnek `3 1/2`, vagy százalékként, `2%`. Az entitás már be van tanítva, mert nem kell az alkalmazás leképezések százalékos tartalmazó példa beszédmódok hozzáadása. Százalékos entitás támogatott [számos országban](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Százalékos típusai
Százalékos felügyelje a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) GitHub-adattár

## <a name="resolution-for-prebuilt-percentage-entity"></a>Előre összeállított százalékos entitás feloldása
Az alábbi példa bemutatja a feloldása a **builtin.percentage** entitás.

```json
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
