---
title: Előre összeállított entitások telefonszáma - LUIS
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
ms.openlocfilehash: 4e2c8e27c6d4195252c6a5b423fa98b2a4247182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270475"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazás előre összeállított telefonszáma
Az `phonenumber` entitás kinyeri a különböző telefonszámokat, beleértve az országkódot is. Mivel ez az entitás már be van tanítva, nem kell példát hozzáadnia az alkalmazáshoz. Az `phonenumber` entitás csak `en-us` a kultúrában támogatott.

## <a name="types-of-a-phone-number"></a>Telefonszám típusai
`Phonenumber`a [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) GitHub tárházból van kezelve

## <a name="resolution-for-this-prebuilt-entity"></a>Megoldás ehhez az előre összeállított entitáshoz

A következő entitásobjektumok at adják vissza a lekérdezéshez:

`my mobile is 1 (800) 642-7676`

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON `verbose` paraméter a `false`következő:

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)
A következő JSON `verbose` paraméter a `true`következő:

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ],
    "$instance": {

        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 (800) 642-7676",
                "startIndex": 13,
                "length": 16,
                "score": 1.0,
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

A következő példa a **builtin.phonenumber** entitás felbontását mutatja be.

```json
"entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
]
```
* * *

## <a name="next-steps"></a>További lépések

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

További információ a [százalékos,](luis-reference-prebuilt-percentage.md) [a szám](luis-reference-prebuilt-number.md)és a [hőmérséklet](luis-reference-prebuilt-temperature.md) entitásokról.
