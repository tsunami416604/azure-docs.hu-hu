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
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 5628c0fa3d6b3554fe096a323899770f3a14f97b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857192"
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
        "value": "24"
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

További információ a [pénznem](luis-reference-prebuilt-currency.md), [sorszámnál](luis-reference-prebuilt-ordinal.md), és [százalékos](luis-reference-prebuilt-percentage.md). 
