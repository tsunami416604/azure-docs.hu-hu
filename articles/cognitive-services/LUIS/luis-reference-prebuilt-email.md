---
title: A LUIS előre összeállított entitások e-mail-referencia – Azure |} A Microsoft Docs
titleSuffix: Azure
description: Ez a cikk az e-mail tartalmazza az előre összeállított Entitásadatok a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 07fe8a0d48d59da89ad76f1648b75332d23005a2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074074"
---
# <a name="email-entity"></a>E-mail entitás
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