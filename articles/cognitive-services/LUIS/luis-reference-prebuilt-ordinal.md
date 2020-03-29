---
title: Előre elkészített entitás – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a nyelvi ismeretek (LUIS) előre elkészített entitásadatait tartalmazza.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bb3bb27db48255f534e873ed4e93ac62f07016af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273447"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Előre összeállított entitás egy LUIS-alkalmazáshoz
A sorszám egy objektum numerikus ábrázolása a `first`készleten belül: , `second`. `third` Mivel ez az entitás már be van tanítva, nem kell hozzáadnia a célértéket tartalmazó példautterances az alkalmazás leképezések. A ordinális [entitást számos kultúrában támogatják.](luis-reference-prebuilt-entities.md)

## <a name="types-of-ordinal"></a>A soraltípusok
Az Ordinal kezelése a [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) GitHub tárházból történik

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Megoldás előre összeállított ordinális entitáshoz

A következő entitásobjektumok at adják vissza a lekérdezéshez:

`Order the second option`

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON `verbose` paraméter a `false`következő:

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)
A következő JSON `verbose` paraméter a `true`következő:

```json
"entities": {
    "ordinal": [
        2
    ],
    "$instance": {
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "second",
                "startIndex": 10,
                "length": 6,
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

A következő példa a **builtin.ordinal** entitás felbontását mutatja be.

```json
"entities": [
  {
    "entity": "second",
    "type": "builtin.ordinal",
    "startIndex": 10,
    "endIndex": 15,
    "resolution": {
      "value": "2"
    }
  }
]
```
* * *

## <a name="next-steps"></a>További lépések

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

Ismerje meg az [OrdinalV2,](luis-reference-prebuilt-ordinal-v2.md) [telefonszám](luis-reference-prebuilt-phonenumber.md), és [a hőmérséklet](luis-reference-prebuilt-temperature.md) entitások.
