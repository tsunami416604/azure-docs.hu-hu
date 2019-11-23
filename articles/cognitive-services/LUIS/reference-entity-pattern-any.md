---
title: Minta. bármely entitás típusa – LUIS
titleSuffix: Azure Cognitive Services
description: Minta. a változó hosszúságú helyőrző csak a minta sablonjának megjelölésére szolgál, amely jelzi, hogy az entitás hol kezdődik és végződik.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 2239387ffff4c30e1183721a528e666199316bed
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695094"
---
# <a name="patternany-entity"></a>Pattern.any entitás 

Minta. a változó hosszúságú helyőrző csak a minta sablonjának megjelölésére szolgál, amely jelzi, hogy az entitás hol kezdődik és végződik.  

Minta. minden entitást meg kell adni a [minta](luis-how-to-model-intent-pattern.md) sablon példákban, nem pedig a leképezési felhasználói példákban.

**Az entitás jól illeszkedik, ha:**

* Az entitás végének összetéveszthető a kihagyás hátralévő szövegével. 

## <a name="usage"></a>Használat

Adott ügyfélalkalmazás, amely a title (cím) alapján keres könyveket, a minta. bármely Kinyeri a teljes címet. A sablon teljes használata minta használatával. a könyvben végzett kereséshez a `Was {BookTitle} written by an American this year[?]`. 

A következő táblázatban az egyes sorok a teljes verzió két változatával rendelkeznek. A legelső Kimondás, hogy a LUIS kezdetben hogyan látja a megnyilatkozás módját. Nem egyértelmű, hogy a könyv címe hol kezdődik és végződik. Az alsó lemondás egy mintát használ. bármely entitás, amely az entitás kezdetét és végét jelöli. 

|Az entitások kimondása félkövérrel|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Volt **az az ember, aki eltartotta a feleségét egy kalapban és más** , az idén amerikai klinikai mesében?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>**Félig aludt a béka pizsamában,** amelyet egy amerikai idén írt?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>**A Lemon Cake különleges szomorúsága volt:** az idén egy Amerikai Egyesült Államok által írt regény?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>Volt **egy Wocket a zsebemben!** írta egy Amerikai Egyesült államokbeli év?|
||



## <a name="example-json"></a>Példa JSON-ban

Vegye figyelembe a következő lekérdezést:

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

A beágyazott űrlap neve kinyerhető mintázatként. bármelyik:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 előrejelzési végpont válasza](#tab/V2)

```JSON
"entities": [
  {
    "entity": "understand your responsibilities as a member of the community",
    "type": "FormName",
    "startIndex": 18,
    "endIndex": 78,
    "role": ""
  }
```


#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 előrejelzési végpont válasza](#tab/V3)

Ez a JSON, ha `verbose=false` van beállítva a lekérdezési karakterláncban:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Ez a JSON, ha `verbose=true` van beállítva a lekérdezési karakterláncban:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ],
    "$instance": {
        "FormName": [
            {
                "type": "FormName",
                "text": "Understand your responsibilities as a member of the community",
                "startIndex": 18,
                "length": 61,
                "modelTypeId": 7,
                "modelType": "Pattern.Any Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 

## <a name="next-steps"></a>Következő lépések

Ebben az [oktatóanyagban](luis-tutorial-pattern-any.md)használja a **mintát. bármely** entitást, amellyel kinyerheti az adatok hosszúságú kimondott szöveg, ahol a hosszúságú kimondott szöveg megfelelően formázottak, és az adatok végének könnyen összetéveszthető a kimaradás többi szavával.