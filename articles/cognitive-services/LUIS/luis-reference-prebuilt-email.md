---
title: LUIS előre elkészített entitások e-mail-referenciája
titleSuffix: Azure Cognitive Services
description: Ez a cikk az e-mail tartalmazza az előre összeállított Entitásadatok a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4a1bc9ae7ccf48b9dc8b47b57ea43b9259786d01
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677682"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>E-mail-előkészített entitás egy LUIS-alkalmazáshoz
E-mailek kinyerése a teljes e-mail-címét az utterance (kifejezés) tartalmaz. Az entitás már be van tanítva, mert nem kell tartalmazó e-mailben az alkalmazás leképezések példa beszédmódok hozzáadása. A támogatott e-mail entitás `en-us` culture csak. 

## <a name="resolution-for-prebuilt-email"></a>Előre összeállított e-mailek felbontás

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 előrejelzési végpont válasza](#tab/V2)

Az alábbi példa bemutatja a feloldása a **builtin.email** entitás.

```json
{
  "query": "please send the information to patti@contoso.com",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.811592042
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.811592042
    }
  ],
  "entities": [
    {
      "entity": "patti@contoso.com",
      "type": "builtin.email",
      "startIndex": 31,
      "endIndex": 55,
      "resolution": {
        "value": "patti@contoso.com"
      }
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 előrejelzési végpont válasza](#tab/V3)

A következő JSON a `verbose` paramétert állítja be a `false` értékre:

```json
{
    "query": "please send the information to patti@contoso.com",
    "prediction": {
        "normalizedQuery": "please send the information to patti@contoso.com",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5023781
            }
        },
        "entities": {
            "email": [
                "patti@contoso.com"
            ]
        }
    }
}
```


A következő JSON a `verbose` paramétert állítja be a `true` értékre:

```json
{
    "query": "please send the information to patti@contoso.com",
    "prediction": {
        "normalizedQuery": "please send the information to patti@contoso.com",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5023781
            }
        },
        "entities": {
            "email": [
                "patti@contoso.com"
            ],
            "$instance": {
                "email": [
                    {
                        "type": "builtin.email",
                        "text": "patti@contoso.com",
                        "startIndex": 31,
                        "length": 25,
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

További információ a [szám](luis-reference-prebuilt-number.md), [sorszámnál](luis-reference-prebuilt-ordinal.md), és [százalékos](luis-reference-prebuilt-percentage.md). 
