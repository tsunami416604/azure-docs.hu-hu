---
title: PersonName előre összeállított entitás - LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a personName előre összeállított entitásadatait tartalmazza a nyelvi ismeretek (LUIS) nyelvben.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 768c719211e8a8f2133d3798343d076e795a3da0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273423"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>A PersonName előre összeállított entitása egy LUIS-alkalmazáshoz
Az előre összeállított personName entitás felismeri a személyek nevét. Mivel ez az entitás már be van tanítva, nem kell hozzáadnia a personName-t tartalmazó példautterances-t az alkalmazás leképezéseihez. PersonName entitás támogatott angol és kínai [kultúrákban](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>PersonName entitás feloldása

A következő entitásobjektumok at adják vissza a lekérdezéshez:

`Is Jill Jones in Cairo?`


#### <a name="v3-response"></a>[V3 válasz](#tab/V3)


A következő JSON `verbose` paraméter a `false`következő:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)
A következő JSON `verbose` paraméter a `true`következő:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ],
    "$instance": {
        "personName": [
            {
                "type": "builtin.personName",
                "text": "Jill Jones",
                "startIndex": 3,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-response"></a>[V2 válasz](#tab/V2)

A következő példa a **builtin.personName** entitás felbontását mutatja be.

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * *

## <a name="next-steps"></a>További lépések

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

További információ az [e-mail,](luis-reference-prebuilt-email.md) [a szám](luis-reference-prebuilt-number.md)és a [levelezőentitásokról.](luis-reference-prebuilt-ordinal.md)
