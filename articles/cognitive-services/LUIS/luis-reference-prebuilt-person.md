---
title: PersonName előre összeállított entitások - LUIS-referencia
titleSuffix: Azure Cognitive Services
description: Ez a cikk tartalmaz personName előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 0bc9d59fa44125429bfcb4e6ca9e68d93b81c04d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46950573"
---
# <a name="personname-entity"></a>PersonName entitás
Az előre összeállított personName entitás emberek neveinek észleli. Az entitás már be van tanítva, mert nem kell való az alkalmazás leképezések personName tartalmazó példa beszédmódok hozzáadása. az angol és kínai támogatott personName entitás [kulturális környezetek](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>PersonName entitás feloldása
Az alábbi példa bemutatja a feloldása a **builtin.personName** entitás.

```JSON
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