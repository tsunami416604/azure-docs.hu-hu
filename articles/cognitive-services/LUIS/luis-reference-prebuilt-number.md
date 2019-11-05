---
title: Előre elkészített entitások száma – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk Language Understanding (LUIS) számmal előre elkészített entitással kapcsolatos információt tartalmaz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6a59cf83b3912e31b8aae67319902ce516519af8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491295"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazáshoz tartozó előre összeépített entitások száma
A numerikus értékek számos módon vannak meghatározva, kifejezve és leírják az információk részleteit. Ez a cikk csak néhány lehetséges példát tárgyal. LUIS a felhasználói hosszúságú kimondott szöveg lévő eltéréseket értelmezi, és konzisztens numerikus értékeket ad vissza. Mivel ez az entitás már be van tanítva, nem kell hozzáadnia például a számot tartalmazó hosszúságú kimondott szöveg az alkalmazás-leképezésekhez. 

## <a name="types-of-number"></a>Típusú számok
A számot a rendszer a [felismerők – Text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) adattárból kezeli

## <a name="examples-of-number-resolution"></a>Példák a szám feloldására

| Kimondott szöveg        | Entitás   | Felbontás |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


A LUIS egy **`builtin.number`** entitás felismert értékét tartalmazza a visszaadott JSON-válasz `resolution` mezőjében.

## <a name="resolution-for-prebuilt-number"></a>Előre elkészített szám feloldása

A lekérdezés a következő entitás-objektumokat adja vissza:

`order two dozen eggs`

#### <a name="v3-responsetabv3"></a>[V3 válasz](#tab/V3)

A következő JSON a `verbose` paraméterrel van beállítva `false`:

```json
"entities": {
    "number": [
        24
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 részletes válasz](#tab/V3-verbose)

A következő JSON a `verbose` paraméterrel van beállítva `true`:

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
#### <a name="v2-responsetabv2"></a>[V2 válasz](#tab/V2)

Az alábbi példában a LUIS-ből származó JSON-válasz látható, amely magában foglalja a 24 érték felbontását a "két tucat" kifejezéshez.

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

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

A [Pénznem](luis-reference-prebuilt-currency.md), a [sorszám](luis-reference-prebuilt-ordinal.md)és a [százalék](luis-reference-prebuilt-percentage.md)megismerése. 
