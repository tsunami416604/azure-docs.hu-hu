---
title: Előre elkészített LUIS entitások URL-cím referencia - Azure |} Microsoft Docs
titleSuffix: Azure
description: Ez a cikk URL-címet tartalmaz előre elkészített entitás információ a nyelvi ismertetése (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 4eacf564a295a568a3e2c8d2f44ad0af3fbbe258
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321959"
---
# <a name="url-entity"></a>Entitás URL-címe
URL-cím entitás URL-címek tartománynév vagy IP-címek bontja ki. Az entitás már be van tanítva, mert nem kell az alkalmazás URL-címeket tartalmazó példa utterances hozzáadása. URL-cím entitás támogatott `en-us` kulturális környezet csak. 

## <a name="types-of-urls"></a>URL-típusok
URL-cím felügyelete a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) Github-adattár

## <a name="resolution-for-prebuilt-url-entity"></a>Előre elkészített URL-cím entitás felbontás
A következő példa bemutatja a feloldása a **builtin.url** entitás.

```JSON
{
  "query": "http://www.luis.ai is a great cognitive services example of artificial intelligence",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.781975448
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.781975448
    }
  ],
  "entities": [
    {
      "entity": "http://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

További tudnivalók a [sorszám](luis-reference-prebuilt-ordinal.md), [szám](luis-reference-prebuilt-number.md), és [hőmérséklet](luis-reference-prebuilt-temperature.md) entitásokat.