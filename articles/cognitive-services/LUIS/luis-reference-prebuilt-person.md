---
title: PersonName előre összeállított entitások
titleSuffix: Azure Cognitive Services
description: Ez a cikk tartalmaz personName előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 7b748c507d5c848cc83a8a0c55cb7b05903bc542
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958526"
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
