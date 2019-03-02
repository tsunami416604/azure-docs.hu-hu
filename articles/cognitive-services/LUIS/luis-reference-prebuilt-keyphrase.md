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
ms.date: 02/28/2018
ms.author: diberry
ms.openlocfilehash: cd6a63d4e41c31f91511b208265684fb2fc9e419
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57213347"
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
