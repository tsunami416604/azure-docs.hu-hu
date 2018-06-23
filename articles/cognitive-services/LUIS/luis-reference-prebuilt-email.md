---
title: E-mail referencia - Azure LUIS előre elkészített entitások |} Microsoft Docs
titleSuffix: Azure
description: Ez a cikk tartalmazza az e-mailek előre elkészített entitás információ a nyelvi ismertetése (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 517e92202bdd5bc8d970306b9e24999fa62a8e43
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321868"
---
# <a name="email-entity"></a>E-mailek entitás
E-mailek kinyerési tartalmaz egy utterance a teljes e-mail címet. Ehhez az entitáshoz már be van tanítva, mert nem kell az alkalmazás leképezések e-mailek tartalmazó példa utterances hozzáadása. E-mailek entitás támogatott `en-us` kulturális környezet csak. 

## <a name="resolution-for-prebuilt-email"></a>Az előre elkészített e-mailek felbontás
A következő példa bemutatja a feloldása a **builtin.email** entitás.

```JSON
{
  "query": "please send the information to patti.owens@microsoft.com",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.811592042
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.811592042
    }
  ],
  "entities": [
    {
      "entity": "patti.owens@microsoft.com",
      "type": "builtin.email",
      "startIndex": 31,
      "endIndex": 55
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

További tudnivalók a [szám](luis-reference-prebuilt-number.md), [sorszám](luis-reference-prebuilt-ordinal.md), és [százalékos](luis-reference-prebuilt-percentage.md). 