---
title: Előre összeállított entitások százalékos aránya
titleSuffix: Azure
description: Ez a cikk tartalmaz százalékos előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 75538519b7d43aa702e15ce3c22ea4acc73ade87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072197"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Százalék a LUIS-alkalmazásokon előre összeállított entitások
Percenkénti egységeinek törtrészeként, százalékos számok szerepelhetnek `3 1/2`, vagy százalékként, `2%`. Az entitás már be van tanítva, mert nem kell az alkalmazás leképezések százalékos tartalmazó példa beszédmódok hozzáadása. Százalékos entitás támogatott [számos országban](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Százalékos típusai
Százalékos felügyelje a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) GitHub-adattár

## <a name="resolution-for-prebuilt-percentage-entity"></a>Előre összeállított százalékos entitás feloldása

### <a name="api-version-2x"></a>API-verzió 2.x

Az alábbi példa bemutatja a feloldása a **builtin.percentage** entitás.

```json
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

### <a name="preview-api-version-3x"></a>Az előzetes API verzió 3.x

A következő JSON-ja az a `verbose` paraméter beállítása `false`:

```json
{
    "query": "set a trigger when my stock goes up 2%",
    "prediction": {
        "normalizedQuery": "set a trigger when my stock goes up 2%",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.541765451
            }
        },
        "entities": {
            "percentage": [
                2
            ]
        }
    }
}
```

A következő JSON-ja az a `verbose` paraméter beállítása `true`:

```json
{
    "query": "set a trigger when my stock goes up 2%",
    "prediction": {
        "normalizedQuery": "set a trigger when my stock goes up 2%",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.541765451
            }
        },
        "entities": {
            "percentage": [
                2
            ],
            "$instance": {
                "percentage": [
                    {
                        "type": "builtin.percentage",
                        "text": "2%",
                        "startIndex": 36,
                        "length": 2,
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

További információ a [sorszámnál](luis-reference-prebuilt-ordinal.md), [szám](luis-reference-prebuilt-number.md), és [hőmérséklet](luis-reference-prebuilt-temperature.md) entitásokat. 
