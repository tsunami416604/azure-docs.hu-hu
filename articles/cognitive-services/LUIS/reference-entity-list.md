---
title: Entitás típusának listázása – LUIS
description: Az entitások listája a kapcsolódó szavak rögzített, lezárt készletét jelöli a szinonimákkal együtt. A LUIS nem deríti fel a további értékek a lista entitásokat. Az ajánlott funkció használatával megtekintheti az új szavakra vonatkozó javaslatokat az aktuális lista alapján.
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 795d16bc2e0c4223ff3ac283a72493923d3ab355
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297237"
---
# <a name="list-entity"></a>Listaentitás

Az entitások listája a kapcsolódó szavak rögzített, lezárt készletét jelöli a szinonimákkal együtt. A LUIS nem deríti fel a további értékek a lista entitásokat. Az **ajánlott** funkció használatával megtekintheti az új szavakra vonatkozó javaslatokat az aktuális lista alapján. Ha egynél több lista entitás ugyanazzal az értékkel, a végpont lekérdezés minden entitás adja vissza.

Egy lista entitás nem gépi megtanult. Egy pontos egyezés egyeztetése. A LUIS bármely lista egy elemének való egyezés a válasz egy egységként jelöli meg.

**Az entitás jól illeszkedik a szöveges adatként:**

* Ismert készlet.
* Nem változik gyakran. Ha módosítania kell a listát, vagy ha szeretné, hogy a lista kibontható legyen, egy egyszerű entitás jobb választás egy kifejezési listával.
* A készlet nem haladja meg a LUIS maximális [határait](luis-boundaries.md) ezen entitástípus esetében.
* A kiírásban szereplő szöveg a kis-és nagybetűk megkülönböztetése egy szinonimával vagy a kanonikus névvel. LUIS nem használja az egyezésen felüli listát. A zavaros egyezés, a létrehozás, a többes szám és az egyéb változatok nem oldhatók meg egy listával rendelkező entitással. A változatok kezeléséhez érdemes [mintát](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) használni a választható szöveges szintaxissal.

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

Tegyük fel, hogy az alkalmazás rendelkezik egy `Cities`nevű listával, amely lehetővé teszi a városi nevek (például a Sea-Tac), a repülőtéri kód (SEA), a postai irányítószám (98101) és a telefonos körzetszám (206) változatának módosítását.

|Listaelem|Elemet a szinonimák|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

Az előző részletben a `paris` szó a párizsi elemre van leképezve a `Cities` lista entitás részeként. A lista entitás illeszkedik mind a cikk normalizált nevét, valamint az elemet a szinonimák.

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)


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


## <a name="next-steps"></a>További lépések

Ebből az [oktatóanyagból](tutorial-list-entity.md)megtudhatja, hogyan használhatja a **lista entitást** az ismert elemek listájának pontos egyezésének kinyeréséhez.
