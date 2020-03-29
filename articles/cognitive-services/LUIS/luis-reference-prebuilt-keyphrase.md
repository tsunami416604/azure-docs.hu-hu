---
title: Előre elkészített kulcsmondat - LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk kulcsmondat előre összeállított entitás adatokat tartalmaz a nyelvi megértés (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 53be1b13f1e2744e143a4be0777e3a8e3135460e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270526"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>KeyPhrase előre elkészített entitás egy LUIS-alkalmazáshoz
A keyPhrase entitás kinyeri a különböző kulcsfontosságú kifejezéseket egy utterance (kifejezés) kibontása. Nem kell hozzáadnia a keyPhrase-et tartalmazó példautterances az alkalmazáshoz. A keyPhrase entitás [számos kultúrában](luis-language-support.md#languages-supported) támogatott a [szövegelemzési](../text-analytics/overview.md) funkciók részeként.

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Megoldás előre összeállított keyPhrase entitáshoz

A következő entitásobjektumok at adják vissza a lekérdezéshez:

`where is the educational requirements form for the development and engineering group`

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON `verbose` paraméter a `false`következő:

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)
A következő JSON `verbose` paraméter a `true`következő:

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "educational requirements",
                "startIndex": 13,
                "length": 24,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "development",
                "startIndex": 51,
                "length": 11,
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

A következő példa a **builtin.keyPhrase** entitás felbontását mutatja be.

```json
"entities": [
    {
        "entity": "development",
        "type": "builtin.keyPhrase",
        "startIndex": 51,
        "endIndex": 61
    },
    {
        "entity": "educational requirements",
        "type": "builtin.keyPhrase",
        "startIndex": 13,
        "endIndex": 36
    }
]
```
* * *

## <a name="next-steps"></a>További lépések

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

További információ a [százalékos,](luis-reference-prebuilt-percentage.md) [a szám](luis-reference-prebuilt-number.md)és a [korentitásokról.](luis-reference-prebuilt-age.md)
