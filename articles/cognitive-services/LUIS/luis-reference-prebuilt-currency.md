---
title: Előre összeállított pénznem entitás - LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a nyelvismertetés (LUIS) előre összeállított pénznemekkel kapcsolatos entitásadatait tartalmazza.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 56d264fa976967ae4772f50b4aa6f58b30355d6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270777"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Előre összeállított pénznem entitás egy LUIS-alkalmazáshoz
Az előre összeállított pénznementitás számos címletben és országban/régióban észleli a pénznemet, függetlenül a LUIS alkalmazáskultúrától. Mivel ez az entitás már be van tanítva, nem kell pénznemet tartalmazó példautterances hozzáadása az alkalmazás leképezések. A pénznementitás [számos kultúrában](luis-reference-prebuilt-entities.md)támogatott .

## <a name="types-of-currency"></a>Pénznem típusai
A pénznem kezelése a [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) GitHub tárházból történik

## <a name="resolution-for-currency-entity"></a>Pénznementitás feloldása

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON `verbose` paraméter a `false`következő:

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "units": "Dollar"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)
A következő JSON `verbose` paraméter a `true`következő:

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "unit": "Dollar"
        }
    ],
    "$instance": {
        "money": [
            {
                "type": "builtin.currency",
                "text": "$10.99",
                "startIndex": 23,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 válasz](#tab/V2)

A következő példa a **builtin.currency** entitás felbontását mutatja be.

```json
"entities": [
    {
        "entity": "$10.99",
        "type": "builtin.currency",
        "startIndex": 23,
        "endIndex": 28,
        "resolution": {
        "unit": "Dollar",
        "value": "10.99"
        }
    }
]
```
* * *

## <a name="next-steps"></a>További lépések

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

További információ a [datetimeV2,](luis-reference-prebuilt-datetimev2.md) [dimenzió](luis-reference-prebuilt-dimension.md)és [e-mail](luis-reference-prebuilt-email.md) entitásokról.
