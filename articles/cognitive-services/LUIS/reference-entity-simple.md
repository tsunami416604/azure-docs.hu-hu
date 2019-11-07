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
ms.openlocfilehash: 27eb4d827c1488b0d051505e62286fd66d6a286b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73671519"
---
# <a name="simple-entity"></a>Egyszerű entitás 

Az egyszerű entitások olyan általános entitások, amelyek egyetlen fogalmat ismertetnek, és a gép által megtanult környezetből származnak. Mivel az egyszerű entitások általában nevek, például a cégnevek, a terméknév vagy más kategóriájú nevek, akkor adjon meg egy [kifejezési listát](luis-concept-feature.md) , ha egyszerű entitást használ a használt nevek jelzésének növelésére. 

**Az entitás jól illeszkedik, ha:**

* Az adat nem konzisztens formátumú, de ugyanazt a dolgot jelzi. 

![egyszerű entitás](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Példa JSON-ra

`Bob Jones wants 3 meatball pho`

Az előző Kimondás során `Bob Jones` egyszerű `Customer` entitásként van megjelölve.

A végpont által visszaadott adatok között szerepel az entitás neve, a felderített szöveg a Kimondás után, a felderített szöveg helye és a pontszám:

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

|Adatobjektum|Entitás neve|Érték|
|--|--|--|
|Egyszerű entitás|`Customer`|`bob jones`|

## <a name="next-steps"></a>További lépések

Ebben az [oktatóanyagban](luis-quickstart-primary-and-secondary-data.md)az **egyszerű entitás**használatával kinyerheti a feldolgozói feladatokból származó, a vállalat által megtanult adatok nevét. A kinyerési pontosság növeléséhez adjon hozzá egy kifejezést az egyszerű entitáshoz tartozó kifejezések [listájához](luis-concept-feature.md) .