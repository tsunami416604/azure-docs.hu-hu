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
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: f5948dc32fe0d4655a3b5fc1520629535a323c9e
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54882803"
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