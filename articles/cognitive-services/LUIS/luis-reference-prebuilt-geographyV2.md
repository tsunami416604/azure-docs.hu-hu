---
title: Földrajzi hely V2 előre összeállított entitások - LUIS-referencia
titleSuffix: Azure Cognitive Services
description: Ez a cikk tartalmaz geographyV2 előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 3559bc02944f88f486104d4d9553f0c45a1f1754
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983412"
---
# <a name="geographyv2-entity"></a>GeographyV2 entitás
Az előre összeállított geographyV2 entitás helyek észleli. Az entitás már be van tanítva, mert nem kell az alkalmazás leképezések GeographyV2 tartalmazó példa beszédmódok hozzáadása. Angol nyelven támogatott GeographyV2 entitás [kulturális környezet](luis-reference-prebuilt-entities.md).

## <a name="subtypes"></a>Altípus
A földrajzi helyeket altípus rendelkezik:

|Altípus|Cél|
|--|--|
|`poi`|hasznos helyekre vezető útvonalak|
|`city`|Város neve|
|`countryRegion`|ország vagy régió neve|
|`continent`|kontinens neve|
|`state`|állam vagy megye neve|


## <a name="resolution-for-geographyv2-entity"></a>GeographyV2 entitás feloldása
Az alábbi példa bemutatja a feloldása a **builtin.geographyV2** entitás.

```JSON
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.8008023
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.8008023
        }
    ],
    "entities": [
        {
            "entity": "the sphinx",
            "type": "builtin.geographyV2.poi",
            "startIndex": 18,
            "endIndex": 27
        },
        {
            "entity": "gizah",
            "type": "builtin.geographyV2.city",
            "startIndex": 32,
            "endIndex": 36
        },
        {
            "entity": "egypt",
            "type": "builtin.geographyV2.countryRegion",
            "startIndex": 38,
            "endIndex": 42
        },
        {
            "entity": "africa",
            "type": "builtin.geographyV2.continent",
            "startIndex": 47,
            "endIndex": 52
        },
        {
            "entity": "texas",
            "type": "builtin.geographyV2.state",
            "startIndex": 72,
            "endIndex": 76
        },
        {
            "entity": "carol",
            "type": "builtin.personName",
            "startIndex": 0,
            "endIndex": 4
        }
    ]
} 
```

## <a name="next-steps"></a>További lépések

További információ a [e-mail](luis-reference-prebuilt-email.md), [szám](luis-reference-prebuilt-number.md), és [sorszámnál](luis-reference-prebuilt-ordinal.md) entitások. 