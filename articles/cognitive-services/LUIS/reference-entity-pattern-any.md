---
title: Minta. bármely entitás típusa
titleSuffix: Language Understanding - Azure Cognitive Services
description: Minta. a változó hosszúságú helyőrző csak a minta sablonjának megjelölésére szolgál, amely jelzi, hogy az entitás hol kezdődik és végződik.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: f3e5bd068b5df99bdfabff0bfe18ccebff980fc6
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480164"
---
# <a name="patternany-entity"></a>Pattern.any entitás 

Minta. a változó hosszúságú helyőrző csak a minta sablonjának megjelölésére szolgál, amely jelzi, hogy az entitás hol kezdődik és végződik.  

Minta. minden entitást meg kell adni a [minta](luis-how-to-model-intent-pattern.md) sablon példákban, nem pedig a leképezési felhasználói példákban.

**Az entitás jól illeszkedik, ha:**

* Az entitás végének összetéveszthető a kihagyás hátralévő szövegével. 

## <a name="usage"></a>Használat

Adott ügyfélalkalmazás, amely a title (cím) alapján keres könyveket, a minta. bármely Kinyeri a teljes címet. A sablon kimondása minta `Was {BookTitle} written by an American this year[?]`használatával 

A következő táblázatban az egyes sorok a teljes verzió két változatával rendelkeznek. A legelső Kimondás, hogy a LUIS kezdetben hogyan látja a megnyilatkozás módját. Nem egyértelmű, hogy a könyv címe hol kezdődik és végződik. Az alsó lemondás egy mintát használ. bármely entitás, amely az entitás kezdetét és végét jelöli. 

|Az entitások kimondása félkövérrel|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Volt **az az ember, aki eltartotta a feleségét egy kalapban és más** , az idén amerikai klinikai mesében?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>**Félig aludt a béka pizsamában,** amelyet egy amerikai idén írt?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>A **Lemon Cake adott szomorúsága volt: Az év** egy amerikai regénye?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>Volt **egy Wocket a zsebemben!** írta egy Amerikai Egyesült államokbeli év?|
||

## <a name="example-json"></a>Példa JSON-ban

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

Ebben az [oktatóanyagban](luis-tutorial-pattern-any.md)használja a **mintát. bármely** entitást, amellyel kinyerheti az adatok hosszúságú kimondott szöveg, ahol a hosszúságú kimondott szöveg megfelelően formázottak, és az adatok végének könnyen összetéveszthető a kimaradás többi szavával.