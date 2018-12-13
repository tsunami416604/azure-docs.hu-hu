---
title: CurrentY előre összeállított entitások
titleSuffix: Azure
description: Ez a cikk tartalmazza a pénznem előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: c87bfddb611e3e232d4a43f45ae60412566b98fc
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53161763"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Pénznem a LUIS-alkalmazásokon előre összeállított entitások
Az előre összeállított pénznem entitás számos megnevezések és országokban, függetlenül a LUIS alkalmazás kulturális környezettől pénznem észleli. Az entitás már be van tanítva, mert nem kell az alkalmazás-leképezések a pénznem tartalmazó példa beszédmódok hozzáadása. Pénznem entitás támogatott [számos országban](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Pénznem típusa
Pénznem felügyelje a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) GitHub-adattár

## <a name="resolution-for-currency-entity"></a>Pénznem entitás feloldása
Az alábbi példa bemutatja a feloldása a **builtin.currency** entitás.

```json
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