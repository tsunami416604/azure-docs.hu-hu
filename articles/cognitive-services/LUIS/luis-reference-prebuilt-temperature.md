---
title: Hőmérséklet-előkészített entitás – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk tartalmazza a hőmérséklet előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 0c16a60b0ddcde733cfbc7b9c90e07b697526015
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677389"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>A hőmérséklet előre elkészített entitása egy LUIS-alkalmazáshoz
Hőmérséklet kibontja a hőmérséklet-típusok különböző. Az entitás már be van tanítva, mert nem kell az alkalmazás hőmérséklet tartalmazó példa beszédmódok hozzáadása. Hőmérséklet-entitás támogatott [számos országban](luis-reference-prebuilt-entities.md). 

## <a name="types-of-temperature"></a>Hőmérséklet-típusok
A hőmérséklet a [felismerők – Text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) adattárból kezelhető

## <a name="resolution-for-prebuilt-temperature-entity"></a>Előre összeállított hőmérséklet entitás feloldása

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 előrejelzési végpont válasza](#tab/V2)

Az alábbi példa bemutatja a feloldása a **builtin.temperature** entitás.

```json
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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 előrejelzési végpont válasza](#tab/V3)

A következő JSON a `verbose` paramétert állítja be a `false` értékre:

```json
{
    "query": "set the temperature to 30 degrees",
    "prediction": {
        "normalizedQuery": "set the temperature to 30 degrees",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.656305432
            }
        },
        "entities": {
            "temperature": [
                {
                    "number": 30,
                    "unit": "Degree"
                }
            ]
        }
    }
}
```

A következő JSON a `verbose` paramétert állítja be a `true` értékre:

```json
{
    "query": "set the temperature to 30 degrees",
    "prediction": {
        "normalizedQuery": "set the temperature to 30 degrees",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.656305432
            }
        },
        "entities": {
            "temperature": [
                {
                    "number": 30,
                    "unit": "Degree"
                }
            ],
            "$instance": {
                "temperature": [
                    {
                        "type": "builtin.temperature",
                        "text": "30 degrees",
                        "startIndex": 23,
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

* * * 

## <a name="next-steps"></a>További lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

További információ a [százalékos](luis-reference-prebuilt-percentage.md), [szám](luis-reference-prebuilt-number.md), és [kora](luis-reference-prebuilt-age.md) entitásokat. 
