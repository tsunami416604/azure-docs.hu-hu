---
title: Hangulat elemzése – LUIS
titleSuffix: Azure Cognitive Services
description: Ha a hangulati elemzés konfigurálva van, a LUIS JSON-válasz tartalmazza a hangulat elemzését.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270427"
---
# <a name="sentiment-analysis"></a>Hangulatelemzés
Ha a hangulati elemzés konfigurálva van, a LUIS JSON-válasz tartalmazza a hangulat elemzését. További információ az [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) dokumentációjában található érzelmek elemzéséről.


## <a name="resolution-for-sentiment"></a>A hangulat feloldása

Az érzelmi adatmennyiség egy 1 és 0 közötti pontszám, amely az adatmennyiség pozitív (közelebbről 1) vagy negatív (közelebbről 0) hangulatát jelzi.

#### <a name="english-language"></a>[Angol nyelv](#tab/english)

Ha a kulturális `en-us`környezet, a válasz:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Egyéb nyelvek](#tab/other-languages)

Az összes többi kulturális környezet esetében a válasz a következő:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>További lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

