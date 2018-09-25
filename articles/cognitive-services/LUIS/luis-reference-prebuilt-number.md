---
title: A LUIS előre összeállított entitások száma referencia – Azure |} A Microsoft Docs
titleSuffix: Azure
description: Ez a cikk előre összeállított entitások információ a Language Understanding (LUIS) tartalmazza.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 4d14a3490d87693a4785111035b62e7ae59ee669
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041846"
---
# <a name="number-entity"></a>Szám entitás
Számos módon, amelyben a numerikus értékek összeszámolása, express, és írja le az adatokat használják. Ez a cikk ismerteti a lehetséges példa csak néhányat. A LUIS a változások, a felhasználó utterances értelmezi, és egységes numerikus értéket adja vissza. Az entitás már be van tanítva, mert nem kell az alkalmazás leképezések számot tartalmazó példa beszédmódok hozzáadása. 

## <a name="types-of-number"></a>Szám típusú
Szám felügyelje a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) Github-adattár

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

```JSON
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