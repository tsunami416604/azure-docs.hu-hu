---
title: A LUIS előre összeállított entitások dimenzió hivatkozási – Azure |} A Microsoft Docs
titleSuffix: Azure
description: Ez a cikk előre összeállított entitások információ a Language Understanding (LUIS) dimenzióban.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 3c923e7791c58255690100b04700577eb5c3f5dd
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237264"
---
# <a name="dimension-entity"></a>Dimenzió entitás
Az előre összeállított dimenzió entitás észleli a különféle típusú dimenziókat, függetlenül a LUIS alkalmazás kulturális környezet. Az entitás már be van tanítva, mert nem kell az alkalmazás-leképezések a dimenziókat tartalmazó példa beszédmódok hozzáadása. Dimenzió entitás támogatott [számos országban](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Dimenzió típusai

Dimenzió felügyelje a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) Github-adattár


## <a name="resolution-for-dimension-entity"></a>Dimenzió entitás feloldása
Az alábbi példa bemutatja a feloldása a **builtin.dimension** entitás.

```JSON
{
  "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.762141049
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.762141049
    }
  ],
  "entities": [
    {
      "entity": "10 1/2 miles",
      "type": "builtin.dimension",
      "startIndex": 19,
      "endIndex": 30,
      "resolution": {
        "unit": "Mile",
        "value": "10.5"
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

További információ a [e-mail](luis-reference-prebuilt-email.md), [szám](luis-reference-prebuilt-number.md), és [sorszámnál](luis-reference-prebuilt-ordinal.md) entitások. 