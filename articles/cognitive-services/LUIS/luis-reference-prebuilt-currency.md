---
title: Előre elkészített LUIS entitások pénznem referencia - Azure |} Microsoft Docs
titleSuffix: Azure
description: A cikkben pénznem előre elkészített entitás információ a nyelvi ismertetése (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 3e20642f6734b0247d23db1a63317eb8b4a96b5e
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321847"
---
# <a name="currency-entity"></a>Pénznem entitás
Az előre elkészített pénznem entitás észleli a pénznem sok megnevezések és országokban, függetlenül attól, LUIS app kulturális környezet. Az entitás már be van tanítva, mert nem kell az alkalmazás leképezések pénznem tartalmazó példa utterances hozzáadása. Támogatott a pénznem entitás [számos kultúra](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Pénznem típusa
Pénznem felügyelete a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) Github-adattár

## <a name="resolution-for-currency-entity"></a>Pénznem entitás felbontás
A következő példa bemutatja a feloldása a **builtin.currency** entitás.

```JSON
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

További tudnivalók a [datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimenzió](luis-reference-prebuilt-dimension.md), és [e-mail](luis-reference-prebuilt-email.md) entitásokat. 