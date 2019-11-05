---
title: PersonName előre elkészített entitás – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Language Understanding (LUIS) personName előre felépített entitási információit tartalmazza.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 9777c62d97c70d4f6a0d0a4d912dea3fa8decd23
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499542"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>PersonName előre összeépített entitása egy LUIS-alkalmazáshoz
Az előre elkészített personName entitás észleli a személyek nevét. Mivel ez az entitás már be van tanítva, nem kell hozzáadnia például a personName-t tartalmazó hosszúságú kimondott szöveg az Application szándékokhoz. az personName entitás angol és kínai [kulturális](luis-reference-prebuilt-entities.md)környezetekben támogatott.

## <a name="resolution-for-personname-entity"></a>PersonName-entitás feloldása

A lekérdezés a következő entitás-objektumokat adja vissza:

`Is Jill Jones in Cairo?`


#### <a name="v3-responsetabv3"></a>[V3 válasz](#tab/V3)


A következő JSON a `verbose` paraméterrel van beállítva `false`:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 részletes válasz](#tab/V3-verbose)
A következő JSON a `verbose` paraméterrel van beállítva `true`:

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
#### <a name="v2-responsetabv2"></a>[V2 válasz](#tab/V2)

Az alábbi példa a **beépített. personName** entitás feloldását mutatja be.

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

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

További információ az [e-mailek](luis-reference-prebuilt-email.md), a [számok](luis-reference-prebuilt-number.md)és a [sorszámok](luis-reference-prebuilt-ordinal.md) entitásokról. 
