---
title: Előre összeállított entitások dimenziója - LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk dimenzió előre összeállított entitásadatokat tartalmaz a nyelvi ismeretek (LUIS) című témakörben.
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 3c7bebb38d0331c76eeaaa1d9d6464c568a61b83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270632"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Előre összeállított entitás dimenziója egy LUIS-alkalmazáshoz
Az előre összeállított dimenzióentitás a LUIS alkalmazáskulturális környezettől függetlenül különböző dimenziótípusokat észlel. Mivel ez az entitás már be van tanítva, nem kell hozzáadnia a dimenziókat tartalmazó példautterances az alkalmazás leképezések. A [dimenzióentitást számos kultúrában támogatják.](luis-reference-prebuilt-entities.md)

## <a name="types-of-dimension"></a>Dimenziótípusok

A dimenzió kezelése a [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) GitHub-tárházból történik.

## <a name="resolution-for-dimension-entity"></a>Feloldás dimenzióentitáshoz

A következő entitásobjektumok at adják vissza a lekérdezéshez:

`10 1/2 miles of cable`

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON `verbose` paraméter a `false`következő:

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)
A következő JSON `verbose` paraméter a `true`következő:

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ],
    "$instance": {
        "dimension": [
            {
                "type": "builtin.dimension",
                "text": "10 1/2 miles",
                "startIndex": 0,
                "length": 12,
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

A következő példa a **builtin.dimension** entitás felbontását mutatja be.

```json
{
    "entity": "10 1/2 miles",
    "type": "builtin.dimension",
    "startIndex": 0,
    "endIndex": 11,
    "resolution": {
    "unit": "Mile",
    "value": "10.5"
    }
}
```
* * *

## <a name="next-steps"></a>További lépések

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

További információ az [e-mail,](luis-reference-prebuilt-email.md) [a szám](luis-reference-prebuilt-number.md)és a [levelezőentitásokról.](luis-reference-prebuilt-ordinal.md)
