---
title: Előre elkészített LUIS entitások hőmérséklet referencia - Azure |} Microsoft Docs
titleSuffix: Azure
description: A cikkben hőmérséklet előre elkészített entitás információ a nyelvi ismertetése (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 3cc16e7ec87775407c4261655d8f680cc0903e81
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321840"
---
# <a name="temperature-entity"></a>Hőmérséklet-entitás
Hőmérséklet hőmérséklet-típusok különböző bontja ki. Az entitás már be van tanítva, mert nem kell az alkalmazás hőmérséklet tartalmazó példa utterances hozzáadása. Hőmérséklet-entitás támogatott [számos kultúra](luis-reference-prebuilt-entities.md). 

## <a name="types-of-temperature"></a>Hőmérséklet típusai
Hőmérséklet felügyelete a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) Github-adattár

## <a name="resolution-for-prebuilt-temperature-entity"></a>Előre elkészített hőmérséklet entitás felbontás
A következő példa bemutatja a feloldása a **builtin.temperature** entitás.

```JSON
{
  "query": "set the temperature to 30 degrees",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.85310787
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.85310787
    }
  ],
  "entities": [
    {
      "entity": "30 degrees",
      "type": "builtin.temperature",
      "startIndex": 23,
      "endIndex": 32,
      "resolution": {
        "unit": "Degree",
        "value": "30"
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

További tudnivalók a [százalékos](luis-reference-prebuilt-percentage.md), [szám](luis-reference-prebuilt-number.md), és [kora](luis-reference-prebuilt-age.md) entitásokat. 