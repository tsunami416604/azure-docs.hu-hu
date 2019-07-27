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
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 9ecf977e1a477eb4b9178a6259fbe26ef18a9ec2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560137"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>A hőmérséklet előre elkészített entitása egy LUIS-alkalmazáshoz
Hőmérséklet kibontja a hőmérséklet-típusok különböző. Az entitás már be van tanítva, mert nem kell az alkalmazás hőmérséklet tartalmazó példa beszédmódok hozzáadása. Hőmérséklet-entitás támogatott [számos országban](luis-reference-prebuilt-entities.md). 

## <a name="types-of-temperature"></a>Hőmérséklet-típusok
A hőmérséklet a felismerők [– text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) adattárból kezelhető

## <a name="resolution-for-prebuilt-temperature-entity"></a>Előre összeállított hőmérséklet entitás feloldása

### <a name="api-version-2x"></a>API 2. x verziója

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

### <a name="preview-api-version-3x"></a>Előzetes verziójú API 3. x

A következő JSON `verbose` a paraméter `false`értéke:

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

A következő JSON `verbose` a paraméter `true`értéke:

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

## <a name="next-steps"></a>További lépések

További információ a [százalékos](luis-reference-prebuilt-percentage.md), [szám](luis-reference-prebuilt-number.md), és [kora](luis-reference-prebuilt-age.md) entitásokat. 
