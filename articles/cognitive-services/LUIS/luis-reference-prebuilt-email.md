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
ms.openlocfilehash: 1cb12bdc362955da907fb5a5ed64c2a1a43fdc32
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140863"
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