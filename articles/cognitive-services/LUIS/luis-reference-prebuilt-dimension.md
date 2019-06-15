---
title: Dimenzió előre összeállított entitások
titleSuffix: Azure
description: Ez a cikk előre összeállított entitások információ a Language Understanding (LUIS) dimenzióban.
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: e77cd2290981de62ba7fce7f04174cd0c5ec2af3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072186"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazásokon előre összeállított entitások dimenzió
Az előre összeállított dimenzió entitás észleli a különféle típusú dimenziókat, függetlenül a LUIS alkalmazás kulturális környezet. Az entitás már be van tanítva, mert nem kell az alkalmazás-leképezések a dimenziókat tartalmazó példa beszédmódok hozzáadása. Dimenzió entitás támogatott [számos országban](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Dimenzió típusai

Dimenzió felügyelje a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) GitHub-adattár


## <a name="resolution-for-dimension-entity"></a>Dimenzió entitás feloldása

### <a name="api-version-2x"></a>API-verzió 2.x

Az alábbi példa bemutatja a feloldása a **builtin.dimension** entitás.

```json
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

### <a name="preview-api-version-3x"></a>Az előzetes API verzió 3.x

A következő JSON-ja az a `verbose` paraméter beállítása `false`:

```json
{
    "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
    "prediction": {
        "normalizedQuery": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.400049
            }
        },
        "entities": {
            "dimension": [
                {
                    "number": 10.5,
                    "unit": "Mile"
                }
            ]
        }
    }
}
```

A következő JSON-ja az a `verbose` paraméter beállítása `true`:

```json
{
    "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
    "prediction": {
        "normalizedQuery": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.400049
            }
        },
        "entities": {
            "dimension": [
                {
                    "number": 10.5,
                    "unit": "Mile"
                }
            ],
            "$instance": {
                "dimension": [
                    {
                        "type": "builtin.dimension",
                        "text": "10 1/2 miles",
                        "startIndex": 19,
                        "length": 12,
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

További információ a [e-mail](luis-reference-prebuilt-email.md), [szám](luis-reference-prebuilt-number.md), és [sorszámnál](luis-reference-prebuilt-ordinal.md) entitások. 
