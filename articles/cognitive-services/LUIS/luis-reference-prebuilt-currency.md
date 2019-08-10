---
title: Pénznem előre elkészített entitás – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk tartalmazza a pénznem előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 867e8f830542aab712b2bfe32f05dd9469c0da49
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932536"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>LUIS-alkalmazás pénznemének előre felépített entitása
Az előre felépített pénznem entitások számos címletben és országban/régióban észlelik a pénznemet, függetlenül a LUIS-alkalmazás kulturális környezettől. Az entitás már be van tanítva, mert nem kell az alkalmazás-leképezések a pénznem tartalmazó példa beszédmódok hozzáadása. Pénznem entitás támogatott [számos országban](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Pénznem típusa
A pénznem kezelése a [felismerők – Text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) adattárból történik.

## <a name="resolution-for-currency-entity"></a>Pénznem entitás feloldása

### <a name="api-version-2x"></a>API 2. x verziója

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



### <a name="preview-api-version-3x"></a>Előzetes verziójú API 3. x

A következő JSON `verbose` a paraméter `false`értéke:

```json
{
    "query": "search for items under $10.99",
    "prediction": {
        "normalizedQuery": "search for items under $10.99",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.605889857
            }
        },
        "entities": {
            "money": [
                {
                    "number": 10.99,
                    "unit": "Dollar"
                }
            ]
        }
    }
}
```

A következő JSON `verbose` a paraméter `true`értéke:

```json
{
    "query": "search for items under $10.99",
    "prediction": {
        "normalizedQuery": "search for items under $10.99",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.605889857
            }
        },
        "entities": {
            "money": [
                {
                    "number": 10.99,
                    "unit": "Dollar"
                }
            ],
            "$instance": {
                "money": [
                    {
                        "type": "builtin.currency",
                        "text": "$10.99",
                        "startIndex": 23,
                        "length": 6,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>További lépések

További információ a [datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimenzió](luis-reference-prebuilt-dimension.md), és [e-mail](luis-reference-prebuilt-email.md) entitásokat. 
