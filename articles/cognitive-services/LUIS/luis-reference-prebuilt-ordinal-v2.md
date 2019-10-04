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
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 48dcbd51190e747859f0172473c94b0caa296071
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677556"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>A 2. sorszámú, előre elkészített entitás a LUIS-alkalmazáshoz
A sorszám v2 sorszáma kibontja a [sorszámot](luis-reference-prebuilt-ordinal.md) , hogy olyan relatív hivatkozásokat adjon meg, mint például a `next`, a `last` és a `previous`. Ezeket a rendszer nem a sorszámmal elkészített, előre összeépített entitás használatával bontja ki.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Az előre létrehozott sorszám v2 entitás feloldása

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 előrejelzési végpont válasza](#tab/V3)

A következő JSON a `verbose` paramétert állítja be a `false` értékre:

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

A következő JSON a `verbose` paramétert állítja be a `true` értékre:

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

* * * 

## <a name="next-steps"></a>További lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

A [százalék](luis-reference-prebuilt-percentage.md), a [telefonszám](luis-reference-prebuilt-phonenumber.md)és a [hőmérséklet](luis-reference-prebuilt-temperature.md) entitások ismertetése. 
