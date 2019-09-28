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
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 2a9e3d16f745e8f51d1d375a774d7c687e987efe
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350804"
---
# <a name="list-entity"></a>Listaentitás 

Az entitások listája a kapcsolódó szavak rögzített, lezárt készletét jelöli a szinonimákkal együtt. A LUIS nem deríti fel a további értékek a lista entitásokat. Használja a **javasoljuk** funkció, amellyel új szavak javaslatokat tekintse meg az aktuális lista alapján. Ha egynél több lista entitás ugyanazzal az értékkel, a végpont lekérdezés minden entitás adja vissza. 

Egy lista entitás nem gépi megtanult. Egy pontos egyezés egyeztetése. A LUIS bármely lista egy elemének való egyezés a válasz egy egységként jelöli meg. 

**Az entitás jól illeszkedik a szöveges adatként:**

* Ismert készlet.
* Nem változik gyakran. Ha módosítania kell a listát, vagy ha szeretné, hogy a lista kibontható legyen, egy egyszerű entitás jobb választás egy kifejezési listával. 
* A készlet nem haladja meg a LUIS maximális [határait](luis-boundaries.md) ezen entitástípus esetében.
* A kimondott szöveg egy része pontosan megegyezik egy szinonimával vagy a kanonikus névvel. LUIS nem használja a listát a pontos szöveges egyezéseken felül. A zavaros egyezés, a kis-és nagybetűk megkülönböztetése, a kivezetés, a pluralizmus és az egyéb változatok nem oldhatók meg egy listával rendelkező entitással. A változatok kezeléséhez érdemes [mintát](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) használni a választható szöveges szintaxissal.

![lista entitás](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>Példa JSON-ban

Tegyük fel, hogy az alkalmazás rendelkezik egy nevű lista `Cities`, ami lehetővé teszi a városok nevei, beleértve a város, repülőtér (Sea-tac), a repülőtér kódja (SEA), a postai irányítószám (98101) és a telefonszám körzetszámát (206) változata.

|Listaelem|Elemet a szinonimák|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

Az az előző utterance (kifejezés), a word `paris` Párizs elem részeként van leképezve a `Cities` entitás listája. A lista entitás illeszkedik mind a cikk normalizált nevét, valamint az elemet a szinonimák.

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

|Objektum|Entitás neve|Value|
|--|--|--|
|Entitás listázása|`Cities`|`paris`|

Egy másik példa utterance (kifejezés), Párizs szinonima használatával:

`book 2 tickets to roissy`

```JSON
"entities": [
  {
    "entity": "roissy",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 23,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

|Objektum|Entitás neve|Value|
|--|--|--|
|Entitás listázása|`Cities`|`roissy`|

## <a name="next-steps"></a>További lépések

Ebből az [oktatóanyagból](luis-quickstart-intent-and-list-entity.md)megtudhatja, hogyan használhatja a **lista entitást** az ismert elemek listájának pontos egyezésének kinyeréséhez. 
