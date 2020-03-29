---
title: Pattern.any entitástípus - LUIS
titleSuffix: Azure Cognitive Services
description: Pattern.any egy változó hosszúságú helyőrző csak a minta sablon utterance (kifejezés) megjelölésére, ahol az entitás kezdődik és végződik.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 5164bf55ef8233cf34a470524da3bc852678d79a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75979163"
---
# <a name="patternany-entity"></a>Pattern.any entitás

Pattern.any egy változó hosszúságú helyőrző csak a minta sablon utterance (kifejezés) megjelölésére, ahol az entitás kezdődik és végződik.  

Pattern.any entitások kell jelölni a [Minta](luis-how-to-model-intent-pattern.md) sablon példák, nem a szándék felhasználói példákat.

**A gazdálkodó egység akkor illeszkedik, ha:**

* Az entitás vége összetéveszthető az utterance (kifejezés) fennmaradó szövegével.

## <a name="usage"></a>Használat

Adott egy ügyfélalkalmazás, amely a cím alapján keres könyveket, a pattern.any kinyeri a teljes címet. A sablon utterance kifejezés használatával pattern.any ehhez a könyv keresés `Was {BookTitle} written by an American this year[?]`.

Az alábbi táblázatban minden sor rendelkezik az utterance (kifejezés) két verziója. A felső utterance (kifejezés) hogyan LUIS kezdetben látja az utterance (kifejezés). Nem világos, hogy hol kezdődik és végződik a könyv címe. Az alsó utterance (kifejezés) egy Pattern.any entitást használ az entitás kezdetének és végének jelölésére.

|Kimondott szöveg félkövér entitással|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Volt **az ember, aki összetévesztette a feleségét egy kalap és egyéb klinikai mesék** írta egy amerikai ebben az évben?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Volt **Half Sleep a béka pizsama** írta egy amerikai ebben az évben?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Volt **a különös szomorúság a citromtorta: A regény** írta egy amerikai ebben az évben?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>**Volt egy wocket a zsebemben!** írta egy amerikai ebben az évben?|
||



## <a name="example-json"></a>Példa JSON

Tekintse meg a következő lekérdezést:

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

A beágyazott űrlap nevét kivonat minta.any:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpontra adott válasza](#tab/V2)

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


#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpontra adott válasza](#tab/V3)

Ez a JSON, ha `verbose=false` be van állítva a lekérdezési karakterláncban:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Ez a JSON, ha `verbose=true` be van állítva a lekérdezési karakterláncban:

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

## <a name="next-steps"></a>További lépések

Ebben az [oktatóanyagban](luis-tutorial-pattern.md)használja a **Pattern.any** entitást az utterances adatok kinyeréséhez, ahol az utterances jól formázott, és ahol az adatok végét könnyen összetéveszthető a többi szó az utterance (kifejezés).
