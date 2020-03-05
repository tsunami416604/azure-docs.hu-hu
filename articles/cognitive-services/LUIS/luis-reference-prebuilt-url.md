---
title: URL-alapú előre összeépített entitások – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk URL-címet tartalmaz az előre összeállított Entitásadatok a Language Understanding (LUIS).
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270347"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazás URL-re épülő előkészített entitása
Entitás URL-cím tartománynév vagy IP-címek URL-címek kibontása. Az entitás már be van tanítva, mert nem kell az alkalmazás URL-címeket tartalmazó példa beszédmódok hozzáadása. Az URL-entitás csak `en-us` kulturális környezetekben támogatott.

## <a name="types-of-urls"></a>URL-címeinek típusai
Az URL-cím a [felismerők – Text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) adattárból kezelhető

## <a name="resolution-for-prebuilt-url-entity"></a>Előre összeállított URL-cím entitás feloldása

A lekérdezés a következő entitás-objektumokat adja vissza:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON a `verbose` paraméterrel van beállítva `false`:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)

A következő JSON a `verbose` paraméterrel van beállítva `true`:

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

A következő példa a https://www.luis.ai megoldását mutatja be, amely a mesterséges intelligenciára épülő, nagyszerű kognitív szolgáltatások példája

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

## <a name="next-steps"></a>Következő lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

További információ a [sorszámok](luis-reference-prebuilt-ordinal.md), a [számok](luis-reference-prebuilt-number.md)és a [hőmérsékleti](luis-reference-prebuilt-temperature.md) entitásokról.
