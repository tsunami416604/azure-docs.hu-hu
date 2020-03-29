---
title: Előre összeállított entitás száma - LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a nyelvismertetés (LUIS) előre összeállított entitásadatait tartalmazza.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 85e3589d7467691e2b9a11879510ab980bbd875a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273465"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Előre összeállított entitás száma egy LUIS-alkalmazáshoz
A numerikus értékek számos módon használhatók az információadatok számszerűsítésére, kifejezésére és leírására. Ez a cikk csak néhány lehetséges példát tartalmaz. A LUIS értelmezi a felhasználói kimondott szöveg változatait, és konzisztens numerikus értékeket ad vissza. Mivel ez az entitás már be van tanítva, nem kell hozzáadnia a számot tartalmazó példautterances az alkalmazás leképezések.

## <a name="types-of-number"></a>A szám típusai
A szám kezelése a [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) GitHub tárházból történik

## <a name="examples-of-number-resolution"></a>Példák a számfelbontásra

| Kimondott szöveg        | Entitás   | Megoldás: |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      |
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


A LUIS tartalmazza egy **`builtin.number`** entitás `resolution` felismert értékét a visszaadott JSON-válasz mezőjében.

## <a name="resolution-for-prebuilt-number"></a>Felbontás előre összeállított számhoz

A következő entitásobjektumok at adják vissza a lekérdezéshez:

`order two dozen eggs`

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON `verbose` paraméter a `false`következő:

```json
"entities": {
    "number": [
        24
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)

A következő JSON `verbose` paraméter a `true`következő:

```json
"entities": {
    "number": [
        24
    ],
    "$instance": {
        "number": [
            {
                "type": "builtin.number",
                "text": "two dozen",
                "startIndex": 6,
                "length": 9,
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

A következő példa a LUIS JSON-válaszát mutatja be, amely tartalmazza a 24-es érték feloldását a "két tucat" utterance (kifejezés) értékhez.

```json
"entities": [
  {
    "entity": "two dozen",
    "type": "builtin.number",
    "startIndex": 6,
    "endIndex": 14,
    "resolution": {
      "subtype": "integer",
      "value": "24"
    }
  }
]
```
* * *

## <a name="next-steps"></a>További lépések

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

További információ a [pénznemről](luis-reference-prebuilt-currency.md), [a szószerint](luis-reference-prebuilt-ordinal.md)és a [százalékos értékről](luis-reference-prebuilt-percentage.md).
