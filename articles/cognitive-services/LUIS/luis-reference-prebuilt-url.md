---
title: URL-alapú előre összeépített entitások – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Language Understanding (LUIS) URL-címen előre felépített entitásokra vonatkozó információkat tartalmaz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 12831ede2b9d9251f2e02fa396ee7d2fb2d61240
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499500"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazás URL-re épülő előkészített entitása
URL-entitás kinyeri az URL-címeket tartománynevek vagy IP-címekkel. Mivel ez az entitás már be van tanítva, nem kell hozzáadnia például az alkalmazás URL-címeit tartalmazó hosszúságú kimondott szöveg. Az URL-entitás csak `en-us` kulturális környezetekben támogatott. 

## <a name="types-of-urls"></a>URL-típusok
Az URL-cím a [felismerők – Text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) adattárból kezelhető

## <a name="resolution-for-prebuilt-url-entity"></a>Az előre elkészített URL-entitás feloldása

A lekérdezés a következő entitás-objektumokat adja vissza:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-responsetabv3"></a>[V3 válasz](#tab/V3)

A következő JSON a `verbose` paraméterrel van beállítva `false`:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 részletes válasz](#tab/V3-verbose)

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
#### <a name="v2-responsetabv2"></a>[V2 válasz](#tab/V2)

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

## <a name="next-steps"></a>További lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

További információ a [sorszámok](luis-reference-prebuilt-ordinal.md), a [számok](luis-reference-prebuilt-number.md)és a [hőmérsékleti](luis-reference-prebuilt-temperature.md) entitásokról.
