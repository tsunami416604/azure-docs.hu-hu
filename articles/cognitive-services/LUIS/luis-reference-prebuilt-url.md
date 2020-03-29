---
title: URL előre összeállított entitások - LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk az előzetes entitásadatokat tartalmazza a nyelvi ismeretek (LUIS) című témakörben.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 49f145ce3e9022826abad5d274dd611bb2cc6530
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270347"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazás előre összeállított URL-címe
Az URL-entitás tartománynevekkel vagy IP-címekkel bontja ki az URL-címeket. Mivel ez az entitás már be van tanítva, nem kell url-eket tartalmazó példautters hozzáadása az alkalmazáshoz. Az URL-entitás `en-us` csak a kulturális környezetben támogatott.

## <a name="types-of-urls"></a>Az URL-címek típusai
Az URL-cím kezelése a [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) GitHub-tárházból történik

## <a name="resolution-for-prebuilt-url-entity"></a>Megoldás előre összeállított URL-entitáshoz

A következő entitásobjektumok at adják vissza a lekérdezéshez:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON `verbose` paraméter a `false`következő:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)

A következő JSON `verbose` paraméter a `true`következő:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ],
    "$instance": {
        "url": [
            {
                "type": "builtin.url",
                "text": "https://www.luis.ai",
                "startIndex": 0,
                "length": 17,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2 válasz](#tab/V2)

A következő példa azt https://www.luis.ai mutatja, a felbontás a egy nagy kognitív szolgáltatások példája a mesterséges intelligencia

```json
"entities": [
    {
        "entity": "https://www.luis.ai",
        "type": "builtin.url",
        "startIndex": 0,
        "endIndex": 17
    }
]
```

* * *

## <a name="next-steps"></a>További lépések

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

További információ a [szint,](luis-reference-prebuilt-ordinal.md) [a szám](luis-reference-prebuilt-number.md)és a [hőmérséklet](luis-reference-prebuilt-temperature.md) entitásokról.
