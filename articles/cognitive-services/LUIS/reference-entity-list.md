---
title: Listaentitás típusa - LUIS
description: A listaentitások a kapcsolódó szavak rögzített, zárt készletét és szinonimáit jelölik. A LUIS nem észlel további értékeket a listaentitásokhoz. Az Ajánlás funkcióval az aktuális lista alapján megtekintheti az új szavakra vonatkozó javaslatokat.
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 795d16bc2e0c4223ff3ac283a72493923d3ab355
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297237"
---
# <a name="list-entity"></a>Listaentitás

A listaentitások a kapcsolódó szavak rögzített, zárt készletét és szinonimáit jelölik. A LUIS nem észlel további értékeket a listaentitásokhoz. Az **Ajánlás** funkcióval az aktuális lista alapján megtekintheti az új szavakra vonatkozó javaslatokat. Ha egynél több listaentitás van ugyanazzal az értékkel, akkor a végpontlekérdezésben minden entitás visszakerül.

A listaentitás nem gépmegtanult. Ez egy pontos szövegegyezés. A LUIS a válaszban entitásként jelöli meg bármelyik lista bármely elemére való egyezést.

**Az entitás akkor megfelelő, ha a szöveges adatok:**

* Egy ismert készlet.
* Nem változik gyakran. Ha gyakran kell módosítania a listát, vagy azt szeretné, hogy a lista önkitázza, egy kifejezéslistával kiturbózott egyszerű entitás jobb választás.
* A készlet nem haladja meg a LUIS maximális [határait](luis-boundaries.md) ezen entitástípus esetében.
* Az utterance (kifejezés) szöveg egy kis- és nagybetűket nem megkülönböztető egyezés egy szinonimával vagy a gyűjtőnévvel. A LUIS nem használja a listát az egyezésen kívül. Az intelligens megfeleltetés, a származtatás, a többes szám és az egyéb változatok nem oldódnak fel egy listaentitással. A változatok kezeléséhez érdemes lehet egy [mintát](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) használni a választható szövegszintaxissal.

![lista entitás](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>Példa .json a listaentitásba való importáláshoz

  Az értékeket a következő .json formátumban importálhatja egy meglévő listaentitásba:

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

Tegyük fel, hogy `Cities`az alkalmazás rendelkezik egy lista, nevű , amely lehetővé teszi a változatok a város nevét, beleértve a város repülőtér (Sea-tac), repülőtér-kód (ST), irányítószám (98101), és a telefon körzetszám (206).

|Listaelem|Elem szinonimái|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

Az előző utterance (kifejezés) a szó `paris` van leképezve `Cities` a párizsi elem részeként a lista entitás. A listaentitás megfelel az elem normalizált nevének és az elem szinonimáinak is.

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpontra adott válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpontra adott válasza](#tab/V3)


Ez a JSON, ha `verbose=false` be van állítva a lekérdezési karakterláncban:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Ez a JSON, ha `verbose=true` be van állítva a lekérdezési karakterláncban:

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

|Adatobjektum|Entitás neve|Érték|
|--|--|--|
|Lista entitás|`Cities`|`paris`|


## <a name="next-steps"></a>További lépések

Ebben az [oktatóanyagban megtudhatja,](tutorial-list-entity.md)hogyan **használhatja a listaentitást** a szöveg pontos egyezéseinek kinyerésére az ismert elemek listájából.
