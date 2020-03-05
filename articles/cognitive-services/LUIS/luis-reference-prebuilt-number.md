---
title: Előre elkészített entitások száma – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk előre összeállított entitások információ a Language Understanding (LUIS) tartalmazza.
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273465"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazáshoz tartozó előre összeépített entitások száma
Számos módon, amelyben a numerikus értékek összeszámolása, express, és írja le az adatokat használják. Ez a cikk ismerteti a lehetséges példa csak néhányat. A LUIS a változások, a felhasználó utterances értelmezi, és egységes numerikus értéket adja vissza. Az entitás már be van tanítva, mert nem kell az alkalmazás leképezések számot tartalmazó példa beszédmódok hozzáadása.

## <a name="types-of-number"></a>Szám típusú
A számot a rendszer a [felismerők – Text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) adattárból kezeli

## <a name="examples-of-number-resolution"></a>Példák a szám felbontás

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


A LUIS egy **`builtin.number`** entitás felismert értékét tartalmazza a visszaadott JSON-válasz `resolution` mezőjében.

## <a name="resolution-for-prebuilt-number"></a>Feloldási előre összeállított maximális száma

A lekérdezés a következő entitás-objektumokat adja vissza:

`order two dozen eggs`

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON a `verbose` paraméterrel van beállítva `false`:

```json
"entities": {
    "number": [
        24
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)

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
#### <a name="v2-response"></a>[V2 válasz](#tab/V2)

Az alábbi példa bemutatja, amely tartalmazza a felbontást az utterance (kifejezés) "két tucat" értékének 24, LUIS, a JSON-választ.

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

## <a name="next-steps"></a>Következő lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

A [Pénznem](luis-reference-prebuilt-currency.md), a [sorszám](luis-reference-prebuilt-ordinal.md)és a [százalék](luis-reference-prebuilt-percentage.md)megismerése.
