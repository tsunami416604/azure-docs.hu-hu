---
title: Előre elkészített LUIS entitások number referencia - Azure |} Microsoft Docs
titleSuffix: Azure
description: A cikkben előre elkészített entitás kapcsolatos információk a nyelvi ismertetése (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: aa0b389a0694a3b742259fd42bed08055fbbadbe
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321861"
---
# <a name="number-entity"></a>Szám entitás
Többféleképpen is, amelyekben numerikus értékek számlálása, express, és az adatokat használnak. Ez a cikk ismerteti a lehetséges példák némelyike csak. LUIS a változások, a felhasználó utterances értelmezi, és egységes numerikus értéket adja vissza. Az entitás már be van tanítva, mert nem kell az alkalmazás leképezések számot tartalmazó példa utterances hozzáadása. 

## <a name="types-of-number"></a>Szám típusú
Szám felügyelete a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) Github-adattár

## <a name="examples-of-number-resolution"></a>Példák számú felbontás

| utterance        | Entitás   | Megoldás: |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


LUIS tartalmaz felismert értékének egy **`builtin.number`** entitás a `resolution` mezőjét a JSON-választ ad vissza.

## <a name="resolution-for-prebuilt-number"></a>Az előre elkészített szám felbontás
A következő példa bemutatja, amely tartalmazza a utterance "két tucat" értéke 24, felbontása LUIS, a JSON-választ.

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

További tudnivalók a [pénznem](luis-reference-prebuilt-currency.md), [sorszám](luis-reference-prebuilt-ordinal.md), és [százalékos](luis-reference-prebuilt-percentage.md). 