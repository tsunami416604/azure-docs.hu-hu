---
title: Előre elkészített hőmérséklet entitás - LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a hőmérséklet előre összeállított entitás adatait tartalmazza a nyelvi megértés (LUIS) című témakörben.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 7e2b48c6353f56ab2269a8718146cb765797adba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270364"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazás hőmérsékletelőre összeállított entitása
A hőmérséklet különböző hőmérsékleti típusokat von ki. Mivel ez az entitás már be van tanítva, nem kell hozzáadnia a hőmérsékletet tartalmazó példautterances az alkalmazáshoz. Hőmérséklet entitás támogatott [számos kultúrában](luis-reference-prebuilt-entities.md).

## <a name="types-of-temperature"></a>A hőmérséklet típusai
A hőmérsékletet a [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) GitHub tárház kezeli

## <a name="resolution-for-prebuilt-temperature-entity"></a>Felbontás előre elkészített hőmérsékleti entitáshoz

A következő entitásobjektumok at adják vissza a lekérdezéshez:

`set the temperature to 30 degrees`


#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON `verbose` paraméter a `false`következő:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)
A következő JSON `verbose` paraméter a `true`következő:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ],
    "$instance": {
        "temperature": [
            {
                "type": "builtin.temperature",
                "text": "30 degrees",
                "startIndex": 23,
                "length": 10,
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

A következő példa a **builtin.temperature** entitás felbontását mutatja be.

```json
"entities": [
    {
        "entity": "30 degrees",
        "type": "builtin.temperature",
        "startIndex": 23,
        "endIndex": 32,
        "resolution": {
        "unit": "Degree",
        "value": "30"
        }
    }
]
```
* * *

## <a name="next-steps"></a>További lépések

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

További információ a [százalékos,](luis-reference-prebuilt-percentage.md) [a szám](luis-reference-prebuilt-number.md)és a [korentitásokról.](luis-reference-prebuilt-age.md)
