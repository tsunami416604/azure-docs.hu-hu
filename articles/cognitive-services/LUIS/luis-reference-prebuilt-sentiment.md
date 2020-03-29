---
title: Hangulatelemzés - LUIS
titleSuffix: Azure Cognitive Services
description: Ha a hangulatelemzés konfigurálva van, a LUIS json válasz hangulatelemzést is tartalmaz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/22/2019
ms.author: diberry
ms.openlocfilehash: dee07859da97f76d9a2db3ccbb71ae74d8ff4846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270427"
---
# <a name="sentiment-analysis"></a>Hangulatelemzés
Ha a hangulatelemzés konfigurálva van, a LUIS json válasz hangulatelemzést is tartalmaz. További információ a véleményelemzésről a [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) dokumentációjában.


## <a name="resolution-for-sentiment"></a>Állásfoglalás a hangulat

A hangulatadatok az adatok pozitív (1-hez közelebbi) vagy negatív (0-hoz közelebbi) hangulatát jelző 1 és 0 közötti pontszám.

#### <a name="english-language"></a>[Angol nyelv](#tab/english)

Amikor a `en-us`kultúra , a válasz:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Egyéb nyelvek](#tab/other-languages)

Minden más kultúra esetében a válasz:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>További lépések

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

