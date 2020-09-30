---
title: Hangulat elemzése – LUIS
titleSuffix: Azure Cognitive Services
description: Ha a hangulati elemzés konfigurálva van, a LUIS JSON-válasz tartalmazza a hangulat elemzését.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: 7c3c0f603b9e7d83e40f43ab00d31f4ca63f2b4f
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535372"
---
# <a name="sentiment-analysis"></a>Hangulatelemzés
Ha a hangulati elemzés konfigurálva van, a LUIS JSON-válasz tartalmazza a hangulat elemzését. További információ az [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) dokumentációjában található érzelmek elemzéséről.

A LUIS Text Analytics v2 protokollt használ. 

## <a name="resolution-for-sentiment"></a>A hangulat feloldása

Az érzelmi adatmennyiség egy 1 és 0 közötti pontszám, amely az adatmennyiség pozitív (közelebbről 1) vagy negatív (közelebbről 0) hangulatát jelzi.

#### <a name="english-language"></a>[Angol nyelv](#tab/english)

Ha a kulturális környezet `en-us` , a válasz:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[További nyelvek](#tab/other-languages)

Az összes többi kulturális környezet esetében a válasz a következő:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>További lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

