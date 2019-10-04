---
title: Összetett entitás típusa – LUIS
titleSuffix: Azure Cognitive Services
description: Az összetett entitások más entitásokból állnak, mint például az előre összeépített entitások, az egyszerű, a reguláris kifejezések és a listázási entitások. A különálló entitások teljes entitás űrlap.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: a5a1ad467074ee0aa55d14d50ae153ac68304e6f
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695165"
---
# <a name="composite-entity"></a>Összetett entitást 

Az összetett entitások más entitásokból állnak, mint például az előre összeépített entitások, az egyszerű, a reguláris kifejezések és a listázási entitások. A különálló entitások teljes entitás űrlap. 

**Ez az entitás jó illeszkedést biztosít az adattartalomhoz:**

* Kapcsolódnak egymáshoz. 
* Kapcsolódik egymáshoz a kimondott szövegkörnyezetben.
* Többféle típusú entitás használja.
* Az ügyfélalkalmazás adategységként kell csoportosítani és feldolgoznia.
* Különböző felhasználói hosszúságú kimondott szöveg kell rendelkeznie, amelyek gépi tanulást igényelnek.

![Összetett entitást](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Példa JSON-ban

Vegye fontolóra az előre elkészített `number` és `Location::ToLocation` összetett entitást a következő kifejezéssel:

`book 2 tickets to cairo`

Figyelje meg, hogy `2`, száma, és `cairo`, a ToLocation van közöttük, amelyek nem részei a bármelyikével szavakat. A zöld, szerepel a címkézett utterance (kifejezés) az aláhúzás a [LUIS](luis-reference-regions.md) -webhely azt jelzi, hogy egy összetett entitást.

![Összetett entitást](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 előrejelzési végpont válasza](#tab/V2)

A visszaadott összetett entitások egy `compositeEntities` is ad a tömb és az összetett lévő entitások a `entities` tömb:

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 előrejelzési végpont válasza](#tab/V3)

Ez a JSON, ha `verbose=false` van beállítva a lekérdezési karakterláncban:

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

Ez a JSON, ha `verbose=true` van beállítva a lekérdezési karakterláncban:

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


|Objektum|Entitás neve|Érték|
|--|--|--|
|Előre összeállított entitások - szám|"builtin.number"|"2"|
|Előre elkészített entitás – GeographyV2|"Location::ToLocation"|Cairo|

## <a name="next-steps"></a>További lépések

Ebben az [oktatóanyagban](luis-tutorial-composite-entity.md)egy **összetett entitást** ad hozzá, amellyel a különböző típusú kinyert adatmennyiségeket egyetlen tartalmazó entitásba csomagolhatja. A kötegelés az adatokat, az ügyfélalkalmazás is könnyen kinyerheti az különböző adattípusok kapcsolódó adatokat.
