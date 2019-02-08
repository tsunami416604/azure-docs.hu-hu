---
title: Sorszámnál előre összeállított entitások
titleSuffix: Azure
description: Ez a cikk sorszámnál előre összeállított entitások információkat a Language Understanding (LUIS) tartalmaz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: f6762c74dbe6b16791e597464584b91e696220f1
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863450"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazásokon sorszámnál előre összeállított entitás
Sorszámok az objektumon belüli egy numerikus ábrázolását: `first`, `second`, `third`. Az entitás már be van tanítva, mert nem kell tartalmazó sorszámnál való az alkalmazás leképezések példa beszédmódok hozzáadása. Támogatott sorszámnál entitás [számos országban](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Sorszámát típusai
Sorszámát felügyelje a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) GitHub-adattár

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Előre összeállított sorszámnál entitás feloldása
Az alábbi példa bemutatja a feloldása a **builtin.ordinal** entitás.

```json
{
  "query": "Order the second option",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.9993253
  },
  "intents": [
    {
      "intent": "OrderFood",
      "score": 0.9993253
    },
    {
      "intent": "None",
      "score": 0.05046708
    }
  ],
  "entities": [
    {
      "entity": "second",
      "type": "builtin.ordinal",
      "startIndex": 10,
      "endIndex": 15,
      "resolution": {
        "value": "2"
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

További információ a [százalékos](luis-reference-prebuilt-percentage.md), [phonenumber](luis-reference-prebuilt-phonenumber.md), és [hőmérséklet](luis-reference-prebuilt-temperature.md) entitásokat. 
