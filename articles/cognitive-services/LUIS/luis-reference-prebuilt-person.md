---
title: PersonName előre összeállított entitások
titleSuffix: Azure Cognitive Services
description: Ez a cikk tartalmaz personName előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 31dce870e99cbe74e2795a3ba661b0caf92dd48e
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140234"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>PersonName előre összeállított entitások a LUIS-alkalmazások
Az előre összeállított personName entitás emberek neveinek észleli. Az entitás már be van tanítva, mert nem kell való az alkalmazás leképezések personName tartalmazó példa beszédmódok hozzáadása. az angol és kínai támogatott personName entitás [kulturális környezetek](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>PersonName entitás feloldása
Az alábbi példa bemutatja a feloldása a **builtin.personName** entitás.

```json
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  }
  "entities": [
    {
      "entity": "Jill Jones",
      "type": "builtin.personName",
      "startIndex": 3,
      "endIndex": 12
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

További információ a [e-mail](luis-reference-prebuilt-email.md), [szám](luis-reference-prebuilt-number.md), és [sorszámnál](luis-reference-prebuilt-ordinal.md) entitások. 