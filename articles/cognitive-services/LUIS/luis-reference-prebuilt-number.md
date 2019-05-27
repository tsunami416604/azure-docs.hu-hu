---
title: Előre összeállított entitások száma
titleSuffix: Azure
description: Ez a cikk előre összeállított entitások információ a Language Understanding (LUIS) tartalmazza.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: d4f707d4bf9bac5e2208eadb94983af368b9f521
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "65072261"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazásokon előre összeállított entitások száma
Számos módon, amelyben a numerikus értékek összeszámolása, express, és írja le az adatokat használják. Ez a cikk ismerteti a lehetséges példa csak néhányat. A LUIS a változások, a felhasználó utterances értelmezi, és egységes numerikus értéket adja vissza. Az entitás már be van tanítva, mert nem kell az alkalmazás leképezések számot tartalmazó példa beszédmódok hozzáadása. 

## <a name="types-of-number"></a>Szám típusú
Szám felügyelje a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) GitHub-adattár

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


LUIS tartalmazza a felismert értékét egy **`builtin.number`** az entitás a `resolution` mezőjét, a JSON-választ adja vissza.

## <a name="resolution-for-prebuilt-number"></a>Feloldási előre összeállított maximális száma


### <a name="api-version-2x"></a>API-verzió 2.x

Az alábbi példa bemutatja, amely tartalmazza a felbontást az utterance (kifejezés) "két tucat" értékének 24, LUIS, a JSON-választ.

```json
{
  "query": "order two dozen eggs",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.105443209
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.105443209
    },
    {
      "intent": "OrderFood",
      "score": 0.9468431361
    },
    {
      "intent": "Help",
      "score": 0.000399122015
    },
  ],
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
}
```

### <a name="preview-api-version-3x"></a>Az előzetes API verzió 3.x

A következő JSON-ja az a `verbose` paraméter beállítása `false`:

```json
{
    "query": "order two dozen eggs",
    "prediction": {
        "normalizedQuery": "order two dozen eggs",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "number": [
                24
            ]
        }
    }
}
```

A következő JSON-ja az a `verbose` paraméter beállítása `true`:

```json
{
    "query": "order two dozen eggs",
    "prediction": {
        "normalizedQuery": "order two dozen eggs",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
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
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>További lépések

További információ a [pénznem](luis-reference-prebuilt-currency.md), [sorszámnál](luis-reference-prebuilt-ordinal.md), és [százalékos](luis-reference-prebuilt-percentage.md). 
