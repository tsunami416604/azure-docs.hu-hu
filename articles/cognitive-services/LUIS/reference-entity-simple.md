---
title: Egyszerű entitás típusa – LUIS
titleSuffix: Azure Cognitive Services
description: Az egyszerű entitások a gépi megtanult környezet egyetlen koncepcióját írják le. Ha egyszerű entitást használ az eredmények javítására, vegyen fel egy kifejezést tartalmazó listát.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 8b92aa6057c81ec9442372c5b85918cb92196d61
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "74894762"
---
# <a name="simple-entity"></a>Egyszerű entitás

Az egyszerű entitások olyan általános entitások, amelyek egyetlen fogalmat ismertetnek, és a gép által megtanult környezetből származnak. Mivel az egyszerű entitások általában nevek, például a cégnevek, a terméknév vagy más kategóriájú nevek, akkor adjon meg egy [kifejezési listát](luis-concept-feature.md) , ha egyszerű entitást használ a használt nevek jelzésének növelésére.

**Az entitás jól illeszkedik, ha:**

* Az adat nem konzisztens formátumú, de ugyanazt a dolgot jelzi.

![egyszerű entitás](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Példa JSON-ra

`Bob Jones wants 3 meatball pho`

Az előző lemondás során `Bob Jones` egyszerű `Customer` entitásként van megjelölve.

A végpont által visszaadott adatok között szerepel az entitás neve, a felderített szöveg a Kimondás után, a felderített szöveg helye és a pontszám:

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)

Ez a JSON, ha `verbose=false` be van állítva a lekérdezési karakterláncban:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ]
}```

This is the JSON if `verbose=true` is set in the query string:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ],
    "$instance": {
        "Customer": [
            {
                "type": "Customer",
                "text": "Bob Jones",
                "startIndex": 0,
                "length": 9,
                "score": 0.9339134,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

|Adatobjektum|Entitás neve|Érték|
|--|--|--|
|Egyszerű entitás|`Customer`|`bob jones`|

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A minta szintaxisának megismerése](reference-pattern-syntax.md)