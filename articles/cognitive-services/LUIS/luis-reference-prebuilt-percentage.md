---
title: Előre összeállított entitás százalékos aránya - LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a nyelvi ismeretek (LUIS) előre összeállított entitásadatait tartalmazza.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 31ea1c36139abcb1e102161ad76a203073ba4dfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270512"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Előre összeállított entitás százalékos aránya egy LUIS-alkalmazáshoz
A százalékos számok törtként `3 1/2`vagy százalékban `2%`is megjelenhetnek. Mivel ez az entitás már be van tanítva, nem kell hozzáadnia a százalékot tartalmazó példautterances az alkalmazás leképezések. A százalékos entitás [számos kultúrában](luis-reference-prebuilt-entities.md)támogatott.

## <a name="types-of-percentage"></a>A százalékos arány típusai
A százalékot a [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) GitHub-tárházból kezeli a rendszer

## <a name="resolution-for-prebuilt-percentage-entity"></a>Szanálás előre összeállított százalékos entitáshoz

A következő entitásobjektumok at adják vissza a lekérdezéshez:

`set a trigger when my stock goes up 2%`

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON `verbose` paraméter a `false`következő:

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)
A következő JSON `verbose` paraméter a `true`következő:

```json
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

A következő példa a **builtin.percentage** entitás felbontását mutatja be.

```json
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
```
* * *

## <a name="next-steps"></a>További lépések

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

További információ a [szint,](luis-reference-prebuilt-ordinal.md) [a szám](luis-reference-prebuilt-number.md)és a [hőmérséklet](luis-reference-prebuilt-temperature.md) entitásokról.
