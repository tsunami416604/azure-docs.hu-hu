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
ms.date: 07/09/2018
ms.author: diberry
ms.openlocfilehash: 4141c5aea93b308a7149b91001fbf1c88d629d79
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037983"
---
# <a name="keyphrase-entity"></a>keyPhrase entitás
keyPhrase kulcskifejezéseket különböző kigyűjti az utterance (kifejezés). Nem kell az alkalmazás keyPhrase tartalmazó példa beszédmódok hozzáadása. támogatott keyPhrase entitás [számos országban](luis-supported-languages.md#languages-supported) részeként a [szövegelemzés](../text-analytics/overview.md) funkciókat. 

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