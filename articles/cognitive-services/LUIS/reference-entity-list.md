---
title: Entitás típusának listázása – LUIS
titleSuffix: Azure Cognitive Services
description: Az entitások listája a kapcsolódó szavak rögzített, lezárt készletét jelöli a szinonimákkal együtt. A LUIS nem észlel további értékeket a List entitások számára. Az ajánlott funkció használatával megtekintheti az új szavakra vonatkozó javaslatokat az aktuális lista alapján.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 4313a1d644750c0961298bbee3ae211946de360a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849768"
---
# <a name="list-entity"></a>Listaentitás

Az entitások listája a kapcsolódó szavak rögzített, lezárt készletét jelöli a szinonimákkal együtt. A LUIS nem észlel további értékeket a List entitások számára. Az **ajánlott** funkció használatával megtekintheti az új szavakra vonatkozó javaslatokat az aktuális lista alapján. Ha egynél több azonos értékű listaelem szerepel, a rendszer az összes entitást visszaadja a végponti lekérdezésben.

Egy lista entitás nem gépi megtanult. Ez egy pontos szöveges egyezés. LUIS a válaszban entitásként jelöli meg a listában szereplő elemek bármelyikének egyezését.

**Az entitás jól illeszkedik a szöveges adatként:**

* Ismert készlet.
* Nem változik gyakran. Ha módosítania kell a listát, vagy ha szeretné, hogy a lista kibontható legyen, egy egyszerű entitás jobb választás egy kifejezési listával.
* A készlet nem haladja meg a LUIS maximális [határait](luis-boundaries.md) ezen entitástípus esetében.
* A kimondott szöveg egy része pontosan megegyezik egy szinonimával vagy a kanonikus névvel. LUIS nem használja a listát a pontos szöveges egyezéseken felül. A zavaros egyezés, a kis-és nagybetűk megkülönböztetése, a kivezetés, a pluralizmus és az egyéb változatok nem oldhatók meg egy listával rendelkező entitással. A változatok kezeléséhez érdemes [mintát](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) használni a választható szöveges szintaxissal.

![entitás listázása](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>Példa. JSON a List entitásba való importáláshoz

  A következő. JSON formátum használatával importálhat értékeket egy meglévő listára:

  ```JSON
  [
      {
          "canonicalForm": "Blue",
          "list": [
              "navy",
              "royal",
              "baby"
          ]
      },
      {
          "canonicalForm": "Green",
          "list": [
              "kelly",
              "forest",
              "avacado"
          ]
      }
  ]
  ```

## <a name="example-json-response"></a>Példa JSON-válaszra

Tegyük fel, hogy az alkalmazás rendelkezik egy `Cities`nevű listával, amely lehetővé teszi a városi nevek (például a Sea-Tac), a repülőtéri kód (SEA), a postai irányítószám (98101) és a telefonos körzetszám (206) változatának módosítását.

|Listaelem|Elemek szinonimái|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

Az előző részletben a `paris` szó a párizsi elemre van leképezve a `Cities` lista entitás részeként. A lista entitás megegyezik az elem normalizált nevével és az elem szinonimákkal.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 előrejelzési végpont válasza](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 előrejelzési végpont válasza](#tab/V3)


Ez a JSON, ha `verbose=false` van beállítva a lekérdezési karakterláncban:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Ez a JSON, ha `verbose=true` van beállítva a lekérdezési karakterláncban:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

|Adatobjektum|Entitás neve|Value (Díj)|
|--|--|--|
|Entitás listázása|`Cities`|`paris`|


## <a name="next-steps"></a>Következő lépések

Ebből az [oktatóanyagból](tutorial-list-entity.md)megtudhatja, hogyan használhatja a **lista entitást** az ismert elemek listájának pontos egyezésének kinyeréséhez.
