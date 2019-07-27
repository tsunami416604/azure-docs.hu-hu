---
title: Előre elkészített entitás százalékos aránya – LUIS
titleSuffix: Azure Cognitive Services
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
ms.openlocfilehash: 9daaa78003b3d789c3c7b746e6c0689b96e89428
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563395"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazáshoz tartozó előre összeépített entitások százalékos aránya
Percenkénti egységeinek törtrészeként, százalékos számok szerepelhetnek `3 1/2`, vagy százalékként, `2%`. Az entitás már be van tanítva, mert nem kell az alkalmazás leképezések százalékos tartalmazó példa beszédmódok hozzáadása. Százalékos entitás támogatott [számos országban](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Százalékos típusai
A százalékos arányt a [felismerők – Text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) adattár kezeli

## <a name="resolution-for-prebuilt-percentage-entity"></a>Előre összeállított százalékos entitás feloldása

### <a name="api-version-2x"></a>API 2. x verziója

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

### <a name="preview-api-version-3x"></a>Előzetes verziójú API 3. x

A következő JSON `verbose` a paraméter `false`értéke:

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

A következő JSON `verbose` a paraméter `true`értéke:

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
