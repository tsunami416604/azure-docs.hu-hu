---
title: A LUIS előre összeállított entitások keyphrase referencia – Azure |} A Microsoft Docs
titleSuffix: Azure
description: Ez a cikk tartalmaz keyphrase előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: a543e60c6e77ed9fdb825ad6cb2a936119677671
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334584"
---
# <a name="keyphrase-entity"></a>keyPhrase entitás
keyPhrase kulcskifejezéseket különböző kigyűjti az utterance (kifejezés). Nem kell az alkalmazás keyPhrase tartalmazó példa beszédmódok hozzáadása. támogatott keyPhrase entitás [számos országban](luis-language-support.md#languages-supported) részeként a [szövegelemzés](../text-analytics/overview.md) funkciókat. 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Előre összeállított keyPhrase entitás feloldása
Az alábbi példa bemutatja a feloldása a **builtin.keyPhrase** entitás.

```JSON
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
