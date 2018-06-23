---
title: LUIS előre elkészített, személyek sorszám referencia - Azure |} Microsoft Docs
titleSuffix: Azure
description: Ez a cikk ismerteti a sorszám előre elkészített entitás a nyelvi ismertetése (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 2ff9b083e6cabe455baea3ed777dd6cc00b6fbfe
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321966"
---
# <a name="ordinal-entity"></a>Sorszám entitás
Sorszámmal numerikus ábrázolását objektum készleten belül: `first`, `second`, `third`. Az entitás már be van tanítva, mert nem kell sorszáma, hogy az alkalmazás leképezések tartalmazó példa utterances hozzáadása. Támogatott a sorszám entitás [számos kultúra](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Sorszámát típusai
Sorszámát felügyelete a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) Github-adattár

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Előre elkészített sorszám entitás felbontás
A következő példa bemutatja a feloldása a **builtin.ordinal** entitás.

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

További tudnivalók a [százalékos](luis-reference-prebuilt-percentage.md), [phonenumber](luis-reference-prebuilt-phonenumber.md), és [hőmérséklet](luis-reference-prebuilt-temperature.md) entitásokat. 