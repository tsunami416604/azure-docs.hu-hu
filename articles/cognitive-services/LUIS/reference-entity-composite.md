---
title: Összetett entitástípus - LUIS
titleSuffix: Azure Cognitive Services
description: Az összetett entitás más entitásokból áll, például előre összeállított entitásokból, egyszerű, reguláris kifejezésből és listaentitásokból. A különálló entitások egy egész entitást alkotnak.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: a5a1ad467074ee0aa55d14d50ae153ac68304e6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "71695165"
---
# <a name="composite-entity"></a>Összetett entitás 

Az összetett entitás más entitásokból áll, például előre összeállított entitásokból, egyszerű, reguláris kifejezésből és listaentitásokból. A különálló entitások egy egész entitást alkotnak. 

**Ez az entitás akkor illeszkedik, ha az adatok:**

* Kapcsolódnak egymáshoz. 
* Kapcsolódik egymáshoz a kimondott szövegkörnyezetben.
* Használjon különböző entitástípusokat.
* Az ügyfélalkalmazásnak információegységként kell csoportosítania és feldolgoznia.
* Számos felhasználói utterances, amelyek gépi tanulást igényelnek.

![összetett entitás](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Példa JSON

Fontolja meg egy összetett `number` `Location::ToLocation` entitás előre összeállított és a következő utterance (kifejezés):

`book 2 tickets to cairo`

Figyelje `2`meg, hogy `cairo`a szám és a tolok között olyan szavak vannak, amelyek nem részei egyik entitásnak sem. A zöld aláhúzás, a [LUIS-webhely](luis-reference-regions.md) feliratozott utterance (kifejezés) kifejezésében egy összetett entitást jelöl.

![Összetett entitás](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpontra adott válasza](#tab/V2)

Az összetett entitások `compositeEntities` egy tömbben jelennek meg, és `entities` az összetett ben lévő összes entitás is a tömbben jelenik meg:

```JSON
  "entities": [
    {
      "entity": "2 tickets to cairo",
      "type": "ticketinfo",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.9214487
    },
    {
      "entity": "cairo",
      "type": "builtin.geographyV2.city",
      "startIndex": 18,
      "endIndex": 22
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "subtype": "integer",
        "value": "2"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "ticketinfo",
      "value": "2 tickets to cairo",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "builtin.geographyV2.city",
          "value": "cairo"
        }
      ]
    }
  ]
```    

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpontra adott válasza](#tab/V3)

Ez a JSON, ha `verbose=false` be van állítva a lekérdezési karakterláncban:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ]
        }
    ]
}
```

Ez a JSON, ha `verbose=true` be van állítva a lekérdezési karakterláncban:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 5,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2.city",
                        "text": "cairo",
                        "startIndex": 18,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "$instance": {
        "ticketinfo": [
            {
                "type": "ticketinfo",
                "text": "2 tickets to cairo",
                "startIndex": 5,
                "length": 18,
                "score": 0.9214487,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
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
|Előre összeállított entitás - szám|"beépített.szám"|"2"|
|Előre összeállított entitás - GeographyV2|"Hely::Hely"|"Kairó"|

## <a name="next-steps"></a>További lépések

Ebben az [oktatóanyagban](luis-tutorial-composite-entity.md)adjon hozzá egy **összetett entitást** a kinyert adatok kötegeléséhez különböző típusú egyetlen entitásba. Az adatok kötegelésével az ügyfélalkalmazás könnyen kinyerheti a kapcsolódó adatokat a különböző adattípusokban.
