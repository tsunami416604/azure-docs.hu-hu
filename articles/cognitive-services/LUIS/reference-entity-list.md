---
title: Entitás típusának listázása – LUIS
titleSuffix: Azure Cognitive Services
description: Az entitások listája a kapcsolódó szavak rögzített, lezárt készletét jelöli a szinonimákkal együtt. A LUIS nem deríti fel a további értékek a lista entitásokat. Az ajánlott funkció használatával megtekintheti az új szavakra vonatkozó javaslatokat az aktuális lista alapján.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 1307e6cfca0debe7623eb775c69527a74584033d
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74011995"
---
# <a name="list-entity"></a>Listaentitás 

Az entitások listája a kapcsolódó szavak rögzített, lezárt készletét jelöli a szinonimákkal együtt. A LUIS nem deríti fel a további értékek a lista entitásokat. Használja a **javasoljuk** funkció, amellyel új szavak javaslatokat tekintse meg az aktuális lista alapján. Ha egynél több lista entitás ugyanazzal az értékkel, a végpont lekérdezés minden entitás adja vissza. 

Egy lista entitás nem gépi megtanult. Egy pontos egyezés egyeztetése. A LUIS bármely lista egy elemének való egyezés a válasz egy egységként jelöli meg. 

**Az entitás jól illeszkedik a szöveges adatként:**

* Ismert készlet.
* Nem változik gyakran. Ha módosítania kell a listát, vagy ha szeretné, hogy a lista kibontható legyen, egy egyszerű entitás jobb választás egy kifejezési listával. 
* A készlet nem haladja meg a LUIS maximális [határait](luis-boundaries.md) ezen entitástípus esetében.
* A kimondott szöveg egy része pontosan megegyezik egy szinonimával vagy a kanonikus névvel. LUIS nem használja a listát a pontos szöveges egyezéseken felül. A zavaros egyezés, a kis-és nagybetűk megkülönböztetése, a kivezetés, a pluralizmus és az egyéb változatok nem oldhatók meg egy listával rendelkező entitással. A változatok kezeléséhez érdemes [mintát](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) használni a választható szöveges szintaxissal.

![lista entitás](./media/luis-concept-entities/list-entity.png)

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

Tegyük fel, hogy az alkalmazás rendelkezik egy nevű lista `Cities`, ami lehetővé teszi a városok nevei, beleértve a város, repülőtér (Sea-tac), a repülőtér kódja (SEA), a postai irányítószám (98101) és a telefonszám körzetszámát (206) változata.

|Listaelem|Elemet a szinonimák|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

Az az előző utterance (kifejezés), a word `paris` Párizs elem részeként van leképezve a `Cities` entitás listája. A lista entitás illeszkedik mind a cikk normalizált nevét, valamint az elemet a szinonimák.

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

|Objektum|Entitás neve|Érték|
|--|--|--|
|Entitás listázása|`Cities`|`paris`|


## <a name="next-steps"></a>Következő lépések

Ebből az [oktatóanyagból](luis-quickstart-intent-and-list-entity.md)megtudhatja, hogyan használhatja a **lista entitást** az ismert elemek listájának pontos egyezésének kinyeréséhez. 
