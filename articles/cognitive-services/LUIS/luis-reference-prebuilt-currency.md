---
title: A LUIS előre összeállított entitások pénznem referencia – Azure |} A Microsoft Docs
titleSuffix: Azure
description: Ez a cikk tartalmazza a pénznem előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 89350ad6a49ceef8169d1693dafdedf702992504
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033889"
---
# <a name="currency-entity"></a>Pénznem entitás
Az előre összeállított pénznem entitás számos megnevezések és országokban, függetlenül a LUIS alkalmazás kulturális környezettől pénznem észleli. Az entitás már be van tanítva, mert nem kell az alkalmazás-leképezések a pénznem tartalmazó példa beszédmódok hozzáadása. Pénznem entitás támogatott [számos országban](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Pénznem típusa
Pénznem felügyelje a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) Github-adattár

## <a name="resolution-for-currency-entity"></a>Pénznem entitás feloldása
Az alábbi példa bemutatja a feloldása a **builtin.currency** entitás.

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

További információ a [datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimenzió](luis-reference-prebuilt-dimension.md), és [e-mail](luis-reference-prebuilt-email.md) entitásokat. 