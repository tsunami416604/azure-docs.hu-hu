---
title: A LUIS előre összeállított entitások e-mail-referencia – Azure |} A Microsoft Docs
titleSuffix: Azure
description: Ez a cikk az e-mail tartalmazza az előre összeállított Entitásadatok a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2018
ms.author: diberry
ms.openlocfilehash: 82c4f82e047892d1b1093aff8cbfefa8f6d5d22f
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57213119"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>E-mailben a LUIS-alkalmazásokon előre összeállított entitások
E-mailek kinyerése a teljes e-mail-címét az utterance (kifejezés) tartalmaz. Az entitás már be van tanítva, mert nem kell tartalmazó e-mailben az alkalmazás leképezések példa beszédmódok hozzáadása. A támogatott e-mail entitás `en-us` culture csak. 

## <a name="resolution-for-prebuilt-email"></a>Előre összeállított e-mailek felbontás
Az alábbi példa bemutatja a feloldása a **builtin.email** entitás.

```json
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
      "endIndex": 55,
      "resolution": {
        "value": "patti.owens@microsoft.com"
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

További információ a [szám](luis-reference-prebuilt-number.md), [sorszámnál](luis-reference-prebuilt-ordinal.md), és [százalékos](luis-reference-prebuilt-percentage.md). 
