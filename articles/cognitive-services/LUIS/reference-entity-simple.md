---
title: Egyszerű entitás típusa – LUIS
titleSuffix: Azure Cognitive Services
description: Az egyszerű entitások olyan általános entitások, amelyek egyetlen fogalmat ismertetnek, és a gép által megtanult környezetből származnak. Mivel az egyszerű entitások általában nevek, például a cégnevek, a terméknév vagy más kategóriájú nevek, akkor adjon meg egy kifejezési listát, ha egyszerű entitást használ a használt nevek jelzésének növelésére.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 2eb3ff847f9bfc162adfb281d2ac1fad6f8c5093
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695132"
---
# <a name="simple-entity"></a>Egyszerű entitás 

Az egyszerű entitások olyan általános entitások, amelyek egyetlen fogalmat ismertetnek, és a gép által megtanult környezetből származnak. Mivel az egyszerű entitások általában nevek, például a cégnevek, a terméknév vagy más kategóriájú nevek, akkor adjon meg egy [kifejezési listát](luis-concept-feature.md) , ha egyszerű entitást használ a használt nevek jelzésének növelésére. 

**Az entitás jól illeszkedik, ha:**

* Az adat nem konzisztens formátumú, de ugyanazt a dolgot jelzi. 

![egyszerű entitás](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Példa JSON-ban

`Bob Jones wants 3 meatball pho`

Az előző utterance (kifejezés), a `Bob Jones` van jelölve egy egyszerű `Customer` entitás.

A végpont által visszaadott szerepel az entitás nevét, a felderített szöveget az utterance (kifejezés), a helyét a felderített szöveg és a pontszám:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 előrejelzési végpont válasza](#tab/V3)

Ez a JSON, ha `verbose=false` van beállítva a lekérdezési karakterláncban:

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

|Objektum|Entitás neve|Érték|
|--|--|--|
|Egyszerű entitás|`Customer`|`bob jones`|

## <a name="next-steps"></a>További lépések

Ebben az [oktatóanyagban](luis-quickstart-primary-and-secondary-data.md)az **egyszerű entitás**használatával kinyerheti a feldolgozói feladatokból származó, a vállalat által megtanult adatok nevét. A kinyerési pontosság növeléséhez adjon hozzá egy kifejezést az egyszerű entitáshoz tartozó kifejezések [listájához](luis-concept-feature.md) .