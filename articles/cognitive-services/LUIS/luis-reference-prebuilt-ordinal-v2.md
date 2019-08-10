---
title: Sorszám v2 előre elkészített entitás – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a sorszámú, előre felépített entitások adatait tartalmazza Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 1579c50f2dcf78e350ce014ffb1516cec4ac85b3
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933484"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>A 2. sorszámú, előre elkészített entitás a LUIS-alkalmazáshoz
A sorszám v2 sorszáma [](luis-reference-prebuilt-ordinal.md) kibontja a sorszámot, `next`hogy `last`relatív hivatkozásokat (például,, és `previous`) adjon meg. Ezeket a rendszer nem a sorszámmal elkészített, előre összeépített entitás használatával bontja ki.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Az előre létrehozott sorszám v2 entitás feloldása

### <a name="api-version-2x"></a>API 2. x verziója

Az alábbi példa a **beépített. ordinalV2** entitás feloldását mutatja be.

```json
{
    "query": "what is the second to last choice in the list",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.823669851
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.823669851
        }
    ],
    "entities": [
        {
            "entity": "the second to last",
            "type": "builtin.ordinalV2.relative",
            "startIndex": 8,
            "endIndex": 25,
            "resolution": {
                "offset": "-1",
                "relativeTo": "end"
            }
        }
    ]
}
```

### <a name="preview-api-version-3x"></a>Előzetes verziójú API 3. x

A következő JSON `verbose` a paraméter `false`értéke:

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ]
        }
    }
}
```

A következő JSON `verbose` a paraméter `true`értéke:

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ],
            "$instance": {
                "ordinalV2": [
                    {
                        "type": "builtin.ordinalV2.relative",
                        "text": "the second to last",
                        "startIndex": 8,
                        "length": 18,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>További lépések

A [százalék](luis-reference-prebuilt-percentage.md), a [telefonszám](luis-reference-prebuilt-phonenumber.md)és a [hőmérséklet](luis-reference-prebuilt-temperature.md) entitások ismertetése. 
