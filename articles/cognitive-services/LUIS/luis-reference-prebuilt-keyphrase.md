---
title: Keyphrase előre összeállított entitások
titleSuffix: Azure
description: Ez a cikk tartalmaz keyphrase előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 988609f411ad405b0f1dc244b23fb6db446136a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072003"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>keyPhrase előre összeállított entitások a LUIS-alkalmazások
keyPhrase kulcskifejezéseket különböző kigyűjti az utterance (kifejezés). Nem kell az alkalmazás keyPhrase tartalmazó példa beszédmódok hozzáadása. támogatott keyPhrase entitás [számos országban](luis-language-support.md#languages-supported) részeként a [szövegelemzés](../text-analytics/overview.md) funkciókat. 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Előre összeállított keyPhrase entitás feloldása

### <a name="api-version-2x"></a>API-verzió 2.x

Az alábbi példa bemutatja a feloldása a **builtin.keyPhrase** entitás.

```json
{
  "query": "where is the educational requirements form for the development and engineering group",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.182757929
  },
  "entities": [
    {
      "entity": "development",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 61
    },
    {
      "entity": "educational requirements",
      "type": "builtin.keyPhrase",
      "startIndex": 13,
      "endIndex": 36
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

További információ a [százalékos](luis-reference-prebuilt-percentage.md), [szám](luis-reference-prebuilt-number.md), és [kora](luis-reference-prebuilt-age.md) entitásokat.
