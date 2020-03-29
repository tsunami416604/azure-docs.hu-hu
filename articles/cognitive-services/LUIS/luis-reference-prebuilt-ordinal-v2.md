---
title: Ordinális V2 előre elkészített entitás - LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a V2-es csoportnak a nyelvi ismeretek (LUIS) előre összeállított entitásadatait tartalmazza.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 5e852313db75e598da647ea0f985e2ee18af16de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270484"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazás ordinális V2 előre elkészített entitása
A ordinális V2 szám kibővíti [az Ordinal-t,](luis-reference-prebuilt-ordinal.md) hogy relatív hivatkozásokat adjon meg, mint `next`például a , `last`és `previous`a. Ezek nem kibontása a ordinális előre összeállított entitás használatával.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Megoldás előre összeállított V2-es elrendelési entitáshoz

A következő entitásobjektumok at adják vissza a lekérdezéshez:

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON `verbose` paraméter a `false`következő:

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)

A következő JSON `verbose` paraméter a `true`következő:

```json
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
```
#### <a name="v2-response"></a>[V2 válasz](#tab/V2)

A következő példa a **builtin.ordinalV2** entitás felbontását mutatja be.

```json
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
```
* * *

## <a name="next-steps"></a>További lépések

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

További információ a [százalékos,](luis-reference-prebuilt-percentage.md) [telefonszám](luis-reference-prebuilt-phonenumber.md), és [a hőmérséklet](luis-reference-prebuilt-temperature.md) entitások.
