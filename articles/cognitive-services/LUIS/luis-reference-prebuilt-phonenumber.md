---
title: A LUIS előre összeállított entitások phone hivatkozás – Azure |} A Microsoft Docs
titleSuffix: Azure
description: Ez a cikk a telefonszám előre összeállított entitások-információ a Language Understanding (LUIS) tartalmazza.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: bacb09b50c4b1d82daa6be1ef4fc79c88269cf7a
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035453"
---
# <a name="phonenumber-entity"></a>Telefonszám entitás
A `phonenumber` entitás kinyeri a telefonszámokat, beleértve az országkódot különböző. Az entitás már be van tanítva, mert nem kell példa beszédmódok hozzáadása az alkalmazáshoz. A `phonenumber` entitás támogatott `en-us` culture csak. 

## <a name="types-of-phonenumber"></a>Telefonszám típusai
Telefonszám felügyelje a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) Github-adattár

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>Előre összeállított phonenumber entitás feloldása
Az alábbi példa bemutatja a feloldása a **builtin.phonenumber** entitás.

```JSON
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