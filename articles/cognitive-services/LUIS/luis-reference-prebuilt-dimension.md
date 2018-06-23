---
title: Előre elkészített LUIS entitások dimenzió hivatkozási - Azure |} Microsoft Docs
titleSuffix: Azure
description: Ez a cikk dimenzió előre elkészített entitás információ a nyelvi ismertetése (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 3b2758f1d68ae3659f2e43cad555d327b21f8732
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321980"
---
# <a name="dimension-entity"></a>Dimenzió entitás
Az előre elkészített dimenzió entitás észleli a különböző típusú dimenziókat, függetlenül a LUIS app kulturális környezet. Az entitás már be van tanítva, mert nem kell az alkalmazás leképezések dimenziókat tartalmazó példa utterances hozzáadása. Támogatott a dimenzió entitás [számos kultúra](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Dimenzió típusai

Dimenzió felügyelete a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) Github-adattár


## <a name="resolution-for-dimension-entity"></a>Megoldási dimenzió entitás
A következő példa bemutatja a feloldása a **builtin.dimension** entitás.

```JSON
{
  "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.762141049
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.762141049
    }
  ],
  "entities": [
    {
      "entity": "10 1/2 miles",
      "type": "builtin.dimension",
      "startIndex": 19,
      "endIndex": 30,
      "resolution": {
        "unit": "Mile",
        "value": "10.5"
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

További tudnivalók a [e-mail](luis-reference-prebuilt-email.md), [szám](luis-reference-prebuilt-number.md), és [sorszám](luis-reference-prebuilt-ordinal.md) entitásokat. 