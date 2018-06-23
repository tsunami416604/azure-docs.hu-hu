---
title: Előre elkészített LUIS entitások hivatkozás - Azure phone |} Microsoft Docs
titleSuffix: Azure
description: A cikkben telefonszám előre elkészített entitás-információ a nyelvi ismertetése (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 0f72b807b9b0ec110a80d67babb1c45902b8c810
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321882"
---
# <a name="phonenumber-entity"></a>Telefonszám entitás
A `phonenumber` entitás telefonszámokat, beleértve az országhívószám számos bontja ki. Az entitás már be van tanítva, mert nem kell példa utterances hozzáadni az alkalmazáshoz. A `phonenumber` entitás támogatott `en-us` kulturális környezet csak. 

## <a name="types-of-phonenumber"></a>Telefonszám típusai
Telefonszám felügyelete a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) Github-adattár

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>Előre elkészített phonenumber entitás felbontás
A következő példa bemutatja a feloldása a **builtin.phonenumber** entitás.

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

További tudnivalók a [százalékos](luis-reference-prebuilt-percentage.md), [szám](luis-reference-prebuilt-number.md), és [hőmérséklet](luis-reference-prebuilt-temperature.md) entitásokat. 