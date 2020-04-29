---
title: Összetett entitás típusa – LUIS
titleSuffix: Azure Cognitive Services
description: Az összetett entitások más entitásokból állnak, mint például az előre összeépített entitások, az egyszerű, a reguláris kifejezések és a listázási entitások. A különálló entitások egész entitást alkotnak.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: a5a1ad467074ee0aa55d14d50ae153ac68304e6f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "71695165"
---
# <a name="composite-entity"></a>Összetett entitás 

Az összetett entitások más entitásokból állnak, mint például az előre összeépített entitások, az egyszerű, a reguláris kifejezések és a listázási entitások. A különálló entitások egész entitást alkotnak. 

**Ez az entitás jó illeszkedést biztosít az adattartalomhoz:**

* Kapcsolódnak egymáshoz. 
* Kapcsolódik egymáshoz a kimondott szövegkörnyezetben.
* Többféle típusú entitást használhat.
* Az ügyfélalkalmazás adategységként kell csoportosítani és feldolgoznia.
* Különböző felhasználói hosszúságú kimondott szöveg kell rendelkeznie, amelyek gépi tanulást igényelnek.

![összetett entitás](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Példa JSON-ra

Vegye fontolóra az előre elkészített `number` és `Location::ToLocation` a következő kifejezéssel rendelkező összetett entitást:

`book 2 tickets to cairo`

Figyelje meg `2`, hogy a szám és `cairo`a ToLocation olyan szavakat tartalmaz, amelyek nem részei egyetlen entitásnak sem. A [Luis](luis-reference-regions.md) webhely címkével ellátott kifejezésében használt zöld aláhúzás egy összetett entitást jelöl.

![Összetett entitás](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

Az összetett entitások egy tömbben lesznek visszaadva, és a kompoziton belüli összes `compositeEntities` entitás `entities` a tömbben is megjelenik:

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)

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
|Előre elkészített entitás – szám|"beépített szám"|2|
|Előre elkészített entitás – GeographyV2|"Hely:: ToLocation"|Cairo|

## <a name="next-steps"></a>További lépések

Ebben az [oktatóanyagban](luis-tutorial-composite-entity.md)egy **összetett entitást** ad hozzá, amellyel a különböző típusú kinyert adatmennyiségeket egyetlen tartalmazó entitásba csomagolhatja. Az ügyfélalkalmazások az adatok árukapcsolásával egyszerűen kigyűjthetik a kapcsolódó adatok különböző adattípusokban való kinyerését.
