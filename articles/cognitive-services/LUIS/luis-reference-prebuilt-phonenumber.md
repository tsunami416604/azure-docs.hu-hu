---
title: Előre összeépített telefonszámok – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a telefonszám előre összeállított entitások-információ a Language Understanding (LUIS) tartalmazza.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a640412fb98fb1d4215bd34c205d4fba253cd7e0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560167"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazáshoz tartozó előre összeépített entitás
A `phonenumber` entitás kinyeri a telefonszámokat, beleértve az országkódot különböző. Az entitás már be van tanítva, mert nem kell példa beszédmódok hozzáadása az alkalmazáshoz. A `phonenumber` entitás támogatott `en-us` culture csak. 

## <a name="types-of-a-phone-number"></a>A telefonszám típusa
`Phonenumber`felügyelt a [felismerők – Text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) adattárból

## <a name="resolution-for-this-prebuilt-entity"></a>Az előre elkészített entitás feloldása

### <a name="api-version-2x"></a>API 2. x verziója

Az alábbi példa bemutatja a feloldása a **builtin.phonenumber** entitás.

```json
{
  "query": "my mobile is 1 (800) 642-7676",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Előzetes verziójú API 3. x

A következő JSON `verbose` a paraméter `false`értéke:

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ]
        }
    }
}
```

A következő JSON `verbose` a paraméter `true`értéke:

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ],
            "$instance": {
                "phonenumber": [
                    {
                        "type": "builtin.phonenumber",
                        "text": "1 (800) 642-7676",
                        "startIndex": 13,
                        "length": 16,
                        "score": 1,
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

További információ a [százalékos](luis-reference-prebuilt-percentage.md), [szám](luis-reference-prebuilt-number.md), és [hőmérséklet](luis-reference-prebuilt-temperature.md) entitásokat. 
