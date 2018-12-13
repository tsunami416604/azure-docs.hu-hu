---
title: Sorszámnál előre összeállított entitások
titleSuffix: Azure
description: Ez a cikk sorszámnál előre összeállított entitások információkat a Language Understanding (LUIS) tartalmaz.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 060314bd56d477bad3dcb3333ba02c5f4786b476
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53082764"
---
# <a name="ordinal-entity"></a>Sorszám entitás
Sorszámok az objektumon belüli egy numerikus ábrázolását: `first`, `second`, `third`. Az entitás már be van tanítva, mert nem kell tartalmazó sorszámnál való az alkalmazás leképezések példa beszédmódok hozzáadása. Támogatott sorszámnál entitás [számos országban](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Sorszámát típusai
Sorszámát felügyelje a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) Github-adattár

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Előre összeállított sorszámnál entitás feloldása
Az alábbi példa bemutatja a feloldása a **builtin.ordinal** entitás.

```json
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