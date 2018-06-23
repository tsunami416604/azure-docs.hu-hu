---
title: Előre elkészített LUIS entitások százalékos referencia - Azure |} Microsoft Docs
titleSuffix: Azure
description: A cikkben százalékos előre elkészített entitás információ a nyelvi ismertetése (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 4d502e93f8b6bf3af2da05a499c359faeabb51da
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321910"
---
# <a name="percentage-entity"></a>Százalékos entitás
Százalékos számok jelenhet törtek, `3 1/2`, vagy százalékként, `2%`. Az entitás már be van tanítva, mert nem kell az alkalmazás leképezések százalékos tartalmazó példa utterances hozzáadása. Százalékos entitás támogatott [számos kultúra](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Típusú százalékos aránya
Százalékos felügyelete a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) Github-adattár

## <a name="resolution-for-prebuilt-percentage-entity"></a>Előre elkészített százalékos entitás felbontás
A következő példa bemutatja a feloldása a **builtin.percentage** entitás.

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

További tudnivalók a [sorszám](luis-reference-prebuilt-ordinal.md), [szám](luis-reference-prebuilt-number.md), és [hőmérséklet](luis-reference-prebuilt-temperature.md) entitásokat. 