---
title: Gépi megtanult entitás típusa – LUIS
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 2289e8ac7744a7b4cbee300e77efda89d29ee2f5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74017218"
---
# <a name="machine-learned-entity"></a>Gépi megtanult entitás 



**Az entitás jól illeszkedik a szöveges adatként:**


![lista entitás](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>Példa JSON-ban

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

Ismerje meg a [List](reference-entity-list.md) entitást és a [reguláris kifejezés](reference-entity-regular-expression.md) entitást.