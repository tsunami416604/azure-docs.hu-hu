---
title: Hangulat elemzése – LUIS
titleSuffix: Azure Cognitive Services
description: Ha hangulatelemzés van konfigurálva, a LUIS json-válasz tartalmazza a hangulatelemzés.
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270427"
---
# <a name="sentiment-analysis"></a>Hangulatelemzés
Ha hangulatelemzés van konfigurálva, a LUIS json-válasz tartalmazza a hangulatelemzés. További információ az [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) dokumentációjában található érzelmek elemzéséről.


## <a name="resolution-for-sentiment"></a>A hangulat feloldása

Véleményadatok egy 1 és 0 a pozitív jelző közötti pontszámot (közelebb 1) vagy negatív (0 közelebb) az adatok a róluk szóló véleményeket.

#### <a name="english-language"></a>[Angol nyelv](#tab/english)

Ha a kulturális környezet `en-us`, a válasz a következőket:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Egyéb nyelvek](#tab/other-languages)

Minden más országban a válasz a következő:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Következő lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

