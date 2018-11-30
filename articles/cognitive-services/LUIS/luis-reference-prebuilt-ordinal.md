---
title: A LUIS előre elkészített, entitások sorszámnál referencia – Azure |} A Microsoft Docs
titleSuffix: Azure
description: Ez a cikk sorszámnál előre összeállított entitások információkat a Language Understanding (LUIS) tartalmaz.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: e80b9dc63accec43532116b81e6eb0b9bd52ee22
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52335253"
---
# <a name="ordinal-entity"></a>Sorszám entitás
Sorszámok az objektumon belüli egy numerikus ábrázolását: `first`, `second`, `third`. Az entitás már be van tanítva, mert nem kell tartalmazó sorszámnál való az alkalmazás leképezések példa beszédmódok hozzáadása. Támogatott sorszámnál entitás [számos országban](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Sorszámát típusai
Sorszámát felügyelje a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) Github-adattár

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Előre összeállított sorszámnál entitás feloldása
Az alábbi példa bemutatja a feloldása a **builtin.ordinal** entitás.

```JSON
{
  "query": "Order the second option",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.9993253
  },
  "intents": [
    {
      "intent": "OrderFood",
      "score": 0.9993253
    },
    {
      "intent": "None",
      "score": 0.05046708
    }
  ],
  "entities": [
    {
      "entity": "second",
      "type": "builtin.ordinal",
      "startIndex": 10,
      "endIndex": 15,
      "resolution": {
        "value": "2"
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

További információ a [százalékos](luis-reference-prebuilt-percentage.md), [phonenumber](luis-reference-prebuilt-phonenumber.md), és [hőmérséklet](luis-reference-prebuilt-temperature.md) entitásokat. 