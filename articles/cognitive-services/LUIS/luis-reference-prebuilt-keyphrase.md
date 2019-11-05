---
title: Keyphrase előre elkészített entitás – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Language Understanding (LUIS) keyphrase előre felépített entitási információit tartalmazza.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 51d1bd515651824545d486207ad4a74476aa7092
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491282"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>keyPhrase előre összeépített entitása egy LUIS-alkalmazáshoz
A keyPhrase entitás számos kulcsfontosságú kifejezés kibontását írja le. Nem kell hozzáadnia például a keyPhrase tartalmazó hosszúságú kimondott szöveg az alkalmazáshoz. A keyPhrase entitás [számos kultúrában](luis-language-support.md#languages-supported) támogatott a [text Analytics](../text-analytics/overview.md) funkcióinak részeként. 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Az előre elkészített keyPhrase entitás feloldása

A lekérdezés a következő entitás-objektumokat adja vissza:

`where is the educational requirements form for the development and engineering group`

#### <a name="v3-responsetabv3"></a>[V3 válasz](#tab/V3)

A következő JSON a `verbose` paraméterrel van beállítva `false`:

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 részletes válasz](#tab/V3-verbose)
A következő JSON a `verbose` paraméterrel van beállítva `true`:

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
#### <a name="v2-responsetabv2"></a>[V2 válasz](#tab/V2)

Az alábbi példa a **beépített. keyPhrase** entitás feloldását mutatja be.

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

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

Ismerje meg a [százalékos](luis-reference-prebuilt-percentage.md), a [szám](luis-reference-prebuilt-number.md)-és az [életkor](luis-reference-prebuilt-age.md) entitásokat.
