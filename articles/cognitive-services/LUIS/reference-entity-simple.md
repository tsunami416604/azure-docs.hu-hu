---
title: Egyszerű entitástípus - LUIS
titleSuffix: Azure Cognitive Services
description: Egy egyszerű entitás egyetlen fogalmat ír le a gép megtanult környezetből. Kifejezéslista hozzáadása egyszerű entitás használata esetén az eredmények javítása érdekében.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 8b92aa6057c81ec9442372c5b85918cb92196d61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74894762"
---
# <a name="simple-entity"></a>Egyszerű entitás

Egy egyszerű entitás egy általános entitás, amely leírja az egyetlen fogalom, és a gép által tanult környezetben tanult. Mivel az egyszerű entitások általában nevek, például vállalatnevek, terméknevek vagy más névkategóriák, adjon hozzá egy [kifejezéslistát,](luis-concept-feature.md) ha egy egyszerű entitást használ a használt nevek jelének növelésére.

**A gazdálkodó egység akkor illeszkedik, ha:**

* Az adatok nem következetesen formázva, de ugyanazt jelzik.

![egyszerű entitás](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Példa JSON

`Bob Jones wants 3 meatball pho`

Az előző utterance `Bob Jones` (kifejezés) egy `Customer` egyszerű entitás van címkézve.

A végpontról visszaadott adatok közé tartozik az entitás neve, a felderített szöveg az utterance (kifejezés), a felderített szöveg helyét, és a pontszám:

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpontra adott válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpontra adott válasza](#tab/V3)

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
> [Mintaszintaxis megismerése](reference-pattern-syntax.md)