---
title: A LUIS előre összeállított entitások e-mail-referencia – Azure |} A Microsoft Docs
titleSuffix: Azure
description: Ez a cikk az e-mail tartalmazza az előre összeállított Entitásadatok a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 080c4d08348265ea891dd02ff3aa2fe8ba2ad2f1
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221487"
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
      "endIndex": 55
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

További információ a [szám](luis-reference-prebuilt-number.md), [sorszámnál](luis-reference-prebuilt-ordinal.md), és [százalékos](luis-reference-prebuilt-percentage.md). 
