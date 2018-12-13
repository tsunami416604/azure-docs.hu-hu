---
title: Keyphrase előre összeállított entitások
titleSuffix: Azure
description: Ez a cikk tartalmaz keyphrase előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 684bba0c2f0c6fbaf05ce25ce543da413f1b71e2
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141220"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>keyPhrase előre összeállított entitások a LUIS-alkalmazások
keyPhrase kulcskifejezéseket különböző kigyűjti az utterance (kifejezés). Nem kell az alkalmazás keyPhrase tartalmazó példa beszédmódok hozzáadása. támogatott keyPhrase entitás [számos országban](luis-language-support.md#languages-supported) részeként a [szövegelemzés](../text-analytics/overview.md) funkciókat. 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Előre összeállított keyPhrase entitás feloldása
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
