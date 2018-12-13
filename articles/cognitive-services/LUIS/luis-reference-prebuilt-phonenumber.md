---
title: Telefon száma előre összeállított entitások
titleSuffix: Azure
description: Ez a cikk a telefonszám előre összeállított entitások-információ a Language Understanding (LUIS) tartalmazza.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 3a538886b0137e1069af1fa680374b749a9b3f92
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53165409"
---
# <a name="phonenumber-prebuilt-entity-for-a-luis-app"></a>Telefonszám előre összeállított entitások a LUIS-alkalmazások
A `phonenumber` entitás kinyeri a telefonszámokat, beleértve az országkódot különböző. Az entitás már be van tanítva, mert nem kell példa beszédmódok hozzáadása az alkalmazáshoz. A `phonenumber` entitás támogatott `en-us` culture csak. 

## <a name="types-of-phonenumber"></a>Telefonszám típusai
Telefonszám felügyelje a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) GitHub-adattár

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>Előre összeállított phonenumber entitás feloldása
Az alábbi példa bemutatja a feloldása a **builtin.phonenumber** entitás.

```json
{
  "query": "my mobile is 00 44 161 1234567",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
      "entity": "00 44 161 1234567",
      "type": "builtin.phonenumber",
      "startIndex": 13,
      "endIndex": 29,
      "resolution": {
        "value": "00 44 161 1234567"
      }
    }
  ]
}
```


## <a name="next-steps"></a>További lépések

További információ a [százalékos](luis-reference-prebuilt-percentage.md), [szám](luis-reference-prebuilt-number.md), és [hőmérséklet](luis-reference-prebuilt-temperature.md) entitásokat. 