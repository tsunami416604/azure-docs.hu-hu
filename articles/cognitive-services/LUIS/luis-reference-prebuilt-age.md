---
title: Kor előre összeállított entitások
titleSuffix: Azure
description: Ez a cikk tartalmaz kora előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: b8239688000f0ce32ca2c2be054b1443bbb698b5
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65069136"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazásokon előre összeállított entitások életkor
Az előre összeállított kora entitás age értékét egyaránt numerikusan és tekintetében nap, hét, hónap és év rögzíti. Az entitás már be van tanítva, mert nem kell kora, az alkalmazás leképezések tartalmazó példa beszédmódok hozzáadása. Kor entitás támogatott [számos országban](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Kor típusai
Kor felügyelje a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) GitHub-adattár

## <a name="resolution-for-prebuilt-age-entity"></a>Előre összeállított kora entitás feloldása

### <a name="api-version-2x"></a>API-verzió 2.x

Az alábbi példa bemutatja a feloldása a **builtin.age** entitás.

```json
{
  "query": "A 90 day old utilities bill is quite late.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8236133
  },
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Az előzetes API verzió 3.x

A következő JSON-ja az a `verbose` paraméter beállítása `false`:

```json
{
    "query": "A 90 day old utilities bill is quite late.",
    "prediction": {
        "normalizedQuery": "a 90 day old utilities bill is quite late.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.558252
            }
        },
        "entities": {
            "age": [
                {
                    "number": 90,
                    "unit": "Day"
                }
            ]
        }
    }
}
```

A következő JSON-ja az a `verbose` paraméter beállítása `true`:

```json
{
    "query": "A 90 day old utilities bill is quite late.",
    "prediction": {
        "normalizedQuery": "a 90 day old utilities bill is quite late.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.558252
            }
        },
        "entities": {
            "age": [
                {
                    "number": 90,
                    "unit": "Day"
                }
            ],
            "$instance": {
                "age": [
                    {
                        "type": "builtin.age",
                        "text": "90 day old",
                        "startIndex": 2,
                        "length": 10,
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

További információ a [pénznem](luis-reference-prebuilt-currency.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), és [dimenzió](luis-reference-prebuilt-dimension.md) entitásokat. 
