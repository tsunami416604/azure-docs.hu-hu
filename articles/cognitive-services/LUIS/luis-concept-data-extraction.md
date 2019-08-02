---
title: Adatbányászat – LUIS
titleSuffix: Azure Cognitive Services
description: Adatok kinyerése a teljes szövegből szándékok és entitások alapján. Ismerje meg, hogy milyen típusú adatok nyerhetők ki Language Understandingból (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: 055cd25f534de5d3cc3ccbe44df88e7111e101a3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560751"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Adatok kinyerése a teljes szövegből szándékok és entitások alapján
A LUIS teszi lehetővé a felhasználó a természetes nyelvű utterances lekérni adatait. Az adatokat oly módon, hogy használat szerint a program, alkalmazás vagy csevegőrobot műveletet ki kell olvasni. A következő szakaszban megtudhatja, milyen adatokat küld vissza, a szándékok és entitások példákkal a JSON.

A kinyerni kívánt legnehezebb adatok a géppel megtanult adatok, mert nem pontos szöveges egyezés. A gépi megtanult entitások kinyerésének a [létrehozási ciklus](luis-concept-app-iteration.md) részét kell képeznie, amíg biztos abban, hogy megkapja a várt adatmennyiséget. [](luis-concept-entity-types.md)

## <a name="data-location-and-key-usage"></a>Adatok helye és a kulcsot használat
A LUIS biztosít a közzétett adatait [végpont](luis-glossary.md#endpoint). A **HTTPS-kérést** (POST vagy GET) tartalmazza az utterance (kifejezés), valamint az egyes választható konfigurációk, például átmeneti és éles környezetekben.

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

A `appID` a Luis-alkalmazás **Beállítások** lapján, valamint `/apps/`a Luis-alkalmazás szerkesztésekor az URL-cím részeként is elérhető. A `subscription-key` a végpont kulcs az alkalmazás lekérdezésekhez használja fel. Habár a LUIS megismerése közben ingyenes authoring/Starter-kulcsot is használhat, fontos, hogy módosítsa a végpont kulcsát egy olyan kulcsra, amely támogatja a [várt Luis](luis-boundaries.md#key-limits)-használatot. A `timezoneOffset` egység érték perc.

A **HTTPS válaszokat** tartalmazza az összes, vagy az aktuális közzétett modell alapján a leképezés és entitás adatait, LUIS megadhatja, hogy az átmeneti és éles végpontot. A végpont URL-cím található a [LUIS](luis-reference-regions.md) webhelyén, a a **kezelés** részben, a a **kulcsokat és a végpontok** lap.

## <a name="data-from-intents"></a>Leképezések adatait
Az elsődleges adatokat a felső pontozási **leképezés neve**. Használatával a `MyStore` [rövid](luis-quickstart-intents-only.md), a végpont válasz:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

|Objektum|Adattípus|Az adatok helye|Érték|
|--|--|--|--|
|Szándék|Karakterlánc|topScoringIntent.intent|"GetStoreInfo"|

Ha a csevegőrobot, vagy a LUIS-hívása alkalmazás meghozta a döntést, egynél több leképezés pontszám alapján, lépjen vissza a leképezések pontszámok a lekérdezési karakterlánc paraméter által `verbose=true`. A végpont válasz a következő:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

A leképezések vannak rendezve legmagasabbtól a legalacsonyabb pontszámot.

|Objektum|Adattípus|Az adatok helye|Érték|Pontszám|
|--|--|--|--|:--|
|Szándék|Karakterlánc|[0]. szándék .intent|"GetStoreInfo"|0.984749258|
|Szándék|Karakterlánc|[1]. szándék .intent|"Nincs"|0.0168218873|

Előre összeállított tartományok ad hozzá, ha a leképezés neve azt jelzi, hogy a tartományhoz, mint például `Utilties` vagy `Communication` valamint célja:

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

|Domain|Objektum|Adattípus|Az adatok helye|Érték|
|--|--|--|--|--|
|Közművek|Szándék|Karakterlánc|[0]. szándék .intent|"<b>Segédprogramok</b>. ShowNext"|
|Kommunikáció|Szándék|Karakterlánc|[1]. szándék .intent|<b>Kommunikációs</b>. StartOver"|
||Szándék|Karakterlánc|[a(z) 2]. szándék .intent|"Nincs"|


## <a name="data-from-entities"></a>Entitások adatainak
A legtöbb csevegőrobotok és alkalmazások kell több, mint a leképezés neve. Ez további, opcionális adatokat az utterance (kifejezés) a felderített entitások származik. Minden entitás típusa az egyezés különböző adatait adja vissza.

Egy egyetlen slovo nebo frázi v az utterance (kifejezés) meg tudja több entitást. Ebben az esetben minden ilyen entitás a pontszámot is visszaad.

Minden entitás rendszer adja vissza a **entitások** a válasz a végpontról tömb:

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

## <a name="tokenized-entity-returned"></a>tokenekre entitást adott vissza
Több [kulturális környezetek](luis-language-support.md#tokenization) az a entitás objektumot ad vissza a `entity` érték [tokenekre](luis-glossary.md#token). A startIndex és a LUIS az adott vissza a célentitás objektum endIndex nem feleltethető meg az új, tokenekre érték hanem ahhoz, hogy programozott módon bontsa ki a nyers entitás az eredeti lekérdezésre. 

Ha például a német, a word `das Bauernbrot` be van tokenekre `das bauern brot`. A tokenekre érték `das bauern brot`, adja vissza, és az eredeti érték programozott módon lehet meghatározni a startIndex és az eredeti lekérdezés, így endIndex `das Bauernbrot`.

## <a name="simple-entity-data"></a>Egyszerű Entitásadatok

A [egyszerű entitás](reference-entity-simple.md) egy gép megtanult érték. Lehet, hogy egy szót vagy kifejezést.

## <a name="composite-entity-data"></a>Összetett Entitásadatok

Az [összetett entitások](reference-entity-composite.md) más entitásokból állnak, mint például az előre összeépített entitások, az egyszerű, a reguláris kifejezések és a listázási entitások. A különálló entitások teljes entitás űrlap. 

## <a name="list-entity-data"></a>Entitások adatainak listája

Az [entitások listája](reference-entity-list.md) a kapcsolódó szavak rögzített, lezárt készletét jelöli a szinonimákkal együtt. A LUIS nem deríti fel a további értékek a lista entitásokat. Használja a **javasoljuk** funkció, amellyel új szavak javaslatokat tekintse meg az aktuális lista alapján. Ha egynél több lista entitás ugyanazzal az értékkel, a végpont lekérdezés minden entitás adja vissza. 

## <a name="prebuilt-entity-data"></a>Előre összeállított entitások adatainak
[Előre összeállított](luis-concept-entity-types.md) entitások egy reguláris kifejezés egyeztetése a nyílt forráskódú használatával alapján felderített [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text) projekt. Előre összeállított entitások entitások tömbben visszaküldi, és használja a típusnév előtaggal `builtin::`. A következő szöveg egy példa utterance (kifejezés) a visszaadott előre összeállított entitások a következő:

`Dec 5th send to +1 360-555-1212`

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
```

## <a name="regular-expression-entity-data"></a>Entitásadatok reguláris kifejezés

A [reguláris kifejezés entitás](reference-entity-regular-expression.md) kinyeri az entitást az Ön által megadott reguláris kifejezési minta alapján.

## <a name="extracting-names"></a>Nevek kibontása
Nevének lekérése az utterance (kifejezés) azért nehéz, mert a neve betűket és szavakat szinte bármilyen kombinációja lehet. Attól függően, hogy milyen típusú nevet szeretne kinyerni, több lehetőség közül választhat. A következő javaslatok nem szabályok, hanem további irányelvek.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Előre elkészített PersonName-és GeographyV2-entitások hozzáadása

A [PersonName](luis-reference-prebuilt-person.md) és a [GeographyV2](luis-reference-prebuilt-geographyV2.md) entitások bizonyos [nyelvi kultúrákban](luis-reference-prebuilt-entities.md)is elérhetők. 

### <a name="names-of-people"></a>Személyek nevét

Emberek neve nem lehet néhány kisebb formátum nyelvi és kulturális környezet függően. Használjon egy előre elkészített **[personName](luis-reference-prebuilt-person.md)** -entitást vagy egy olyan [](luis-concept-roles.md) **[egyszerű entitást](luis-concept-entity-types.md#simple-entity)** , amely a vezetéknév és az utónév szerepkörrel rendelkezik. 

Ha az egyszerű entitást használja, ügyeljen arra, hogy olyan példákat adjon meg, amelyek az utónév és a vezetéknév különböző részeiben használják a hosszúságú kimondott szöveg, és hosszúságú kimondott szöveg az összes szándékot, beleértve a none szándékot is. [Felülvizsgálat](luis-how-to-review-endoint-utt.md) végpont utterances rendszeresen bármely címkenevek nem jelzett megfelelően.

### <a name="names-of-places"></a>Helyek nevei

A helyek nevei be vannak állítva és ismertek, például városok, megyék, Államok, tartományok és országok/régiók. Az előre elkészített entitások **[geographyV2](luis-reference-prebuilt-geographyv2.md)** kinyerheti a hely adatait.

### <a name="new-and-emerging-names"></a>Új és újonnan megjelenő neve

Bizonyos alkalmazásokhoz kell tudni új és újonnan felbukkanó nevek, például a termékek vagy cégek keresése. Az ilyen típusú nevek a legnehezebb kinyerési típus. Kezdje egy **[egyszerű entitással](luis-concept-entity-types.md#simple-entity)** , és adjon hozzá egy [kifejezést tartalmazó listát](luis-concept-feature.md). [Felülvizsgálat](luis-how-to-review-endoint-utt.md) végpont utterances rendszeresen bármely címkenevek nem jelzett megfelelően.

## <a name="pattern-roles-data"></a>A minta szerepkörök adatok
Szerepkörök az entitások környezetfüggő különbségek.

```JSON
{
  "query": "move bob jones from seattle to redmond",
  "topScoringIntent": {
    "intent": "MoveAssetsOrPeople",
    "score": 0.9999998
  },
  "intents": [
    {
      "intent": "MoveAssetsOrPeople",
      "score": 0.9999998
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 6.12244548E-07
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 6.12244548E-07
    },
    {
      "intent": "FindForm",
      "score": 1.1E-09
    }
  ],
  "entities": [
    {
      "entity": "bob jones",
      "type": "Employee",
      "startIndex": 5,
      "endIndex": 13,
      "score": 0.922820568,
      "role": ""
    },
    {
      "entity": "seattle",
      "type": "Location",
      "startIndex": 20,
      "endIndex": 26,
      "score": 0.948008537,
      "role": "Origin"
    },
    {
      "entity": "redmond",
      "type": "Location",
      "startIndex": 31,
      "endIndex": 37,
      "score": 0.7047979,
      "role": "Destination"
    }
  ]
}
```

## <a name="patternany-entity-data"></a>Entitásadatok pattern.any

[Minta.](reference-entity-pattern-any.md) a változó hosszúságú helyőrző csak a minta sablonjának megjelölésére szolgál, amely jelzi, hogy az entitás hol kezdődik és végződik.  

## <a name="sentiment-analysis"></a>Hangulatelemzés
Ha hangulatelemzés van konfigurálva, a LUIS json-válasz tartalmazza a hangulatelemzés. További információ a hangulatelemzés az [Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) dokumentációját.

### <a name="sentiment-data"></a>Véleményadatok
Véleményadatok egy 1 és 0 a pozitív jelző közötti pontszámot (közelebb 1) vagy negatív (0 közelebb) az adatok a róluk szóló véleményeket.

Kulturális környezet esetén `en-us`, a válasz:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

Minden más országban a válasz a következő:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>A kulcsfontosságú kifejezések kinyerése Entitásadatok
A kulcsfontosságú kifejezések kinyerése entitás kulcskifejezéseket adja vissza az utterance (kifejezés), által biztosított [Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

```JSON
{
  "query": "Is there a map of places with beautiful views on a favorite trail?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.764368951
  },
  "intents": [
    ...
  ],
  "entities": [
    {
      "entity": "beautiful views",
      "type": "builtin.keyPhrase",
      "startIndex": 30,
      "endIndex": 44
    },
    {
      "entity": "map of places",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 23
    },
    {
      "entity": "favorite trail",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 64
    }
  ]
}
```

## <a name="data-matching-multiple-entities"></a>Az adatokat több entitás megfelelő

A LUIS felderítését az utterance (kifejezés) az összes entitásokat ad vissza. Ennek eredményeképpen a csevegőrobot szükségessé eredményei alapján döntéseket. Az utterance (kifejezés) az utterance (kifejezés) számos entitás is rendelkeznek:

`book me 2 adult business tickets to paris tomorrow on air france`

A LUIS-végpont felderítése is ugyanazokat az adatokat különböző entitások:

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

## <a name="data-matching-multiple-list-entities"></a>Több lista entitások megfeleltetése

Ha egy szót vagy kifejezést megegyezik a listában több entitást, a végpont lekérdezés minden egyes lista entitás adja vissza.

A lekérdezés `when is the best time to go to red rock?`, és az alkalmazás még szó `red` egynél több listában LUIS felismeri az összes entitást és a JSON-végpont válasz részeként entitásokat tömbjét adja vissza: 

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

Lásd: [entitások hozzáadása](luis-how-to-add-entities.md) további információ entitások hozzáadása a LUIS-alkalmazás.
