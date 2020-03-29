---
title: Előre összeállított kor entitás - LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk az életkor előre összeállított entitásadatait tartalmazza a nyelvi ismeretek (LUIS) című témakörben.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 1607b80276a9c35bf0ac2f0a00bd2226e2c07f97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270798"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Előre összeállított entitás kora egy LUIS-alkalmazáshoz
Az előre összeállított korentitás numerikusan és napokban, hetekben, hónapokban és években is rögzíti a korértéket. Mivel ez az entitás már be van tanítva, nem kell hozzáadnia a példák kimondott, amely tartalmazza a kor az alkalmazás leképezések. Az age entitás [számos kultúrában](luis-reference-prebuilt-entities.md)támogatott.

## <a name="types-of-age"></a>Az életkor típusai
Az életkor kezelése a [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) GitHub tárházból történik

## <a name="resolution-for-prebuilt-age-entity"></a>Megoldás előre összeállított korentitáshoz



#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON `verbose` paraméter a `false`következő:

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)
A következő JSON `verbose` paraméter a `true`következő:

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ],
    "$instance": {
        "age": [
            {
                "type": "builtin.age",
                "text": "90 day old",
                "startIndex": 2,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2 válasz](#tab/V2)

A következő példa a **builtin.age** entitás felbontását mutatja be.

```json
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
```
* * *

## <a name="next-steps"></a>További lépések

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

További információ a [pénznemről,](luis-reference-prebuilt-currency.md) [a datetimeV2](luis-reference-prebuilt-datetimev2.md)és [a dimenzióentitásokról.](luis-reference-prebuilt-dimension.md)
