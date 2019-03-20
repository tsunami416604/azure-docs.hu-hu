---
title: Adatok kinyerése
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ismerje meg, milyen típusú adatok kinyerhetők a Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 3d9c2a694562977c2a012d9faa9c282c6b8fff9d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099831"
---
# <a name="data-extraction-from-intents-and-entities"></a>Adatok kinyerése a szándékok és entitások felismerésére
A LUIS teszi lehetővé a felhasználó a természetes nyelvű utterances lekérni adatait. Az adatokat oly módon, hogy használat szerint a program, alkalmazás vagy csevegőrobot műveletet ki kell olvasni. A következő szakaszban megtudhatja, milyen adatokat küld vissza, a szándékok és entitások példákkal a JSON.

Kinyerni kívánt nagyon nehéz adatok a gép megtanult adatok, mert ez nem egy pontos egyezés egyeztetése. Adatok kinyerése, a gép megtanult [entitások](luis-concept-entity-types.md) részét kell a [ciklus szerzői](luis-concept-app-iteration.md) amíg nem biztos benne, hogy a várt adatokat kap.

## <a name="data-location-and-key-usage"></a>Adatok helye és a kulcsot használat
A LUIS biztosít a közzétett adatait [végpont](luis-glossary.md#endpoint). A **HTTPS-kérést** (POST vagy GET) tartalmazza az utterance (kifejezés), valamint az egyes választható konfigurációk, például átmeneti és éles környezetekben.

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

A `appID` érhető el a **beállítások** lapján a LUIS-alkalmazásokon, valamint az URL-cím (után `/apps/`) Ha szerkeszti a LUIS-alkalmazás. A `subscription-key` a végpont kulcs az alkalmazás lekérdezésekhez használja fel. Bár használhatja az ingyenes szerzői/alapszintű kulcs közben, hogy tanulási LUIS, fontos, hogy módosítsa a végpont kulcs, amely támogatja a kulcs a [várható LUIS használati](luis-boundaries.md#key-limits). A `timezoneOffset` egység érték perc.

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

A [egyszerű entitás](luis-concept-entity-types.md) egy gép megtanult érték. Lehet, hogy egy szót vagy kifejezést.

`Bob Jones wants 3 meatball pho`

Az előző utterance (kifejezés), a `Bob Jones` van jelölve egy egyszerű `Customer` entitás.

A végpont által visszaadott szerepel az entitás nevét, a felderített szöveget az utterance (kifejezés), a helyét a felderített szöveg és a pontszám:

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|Objektum|Entitás neve|Érték|
|--|--|--|
|Egyszerű entitás|"Ügyfél"|"Belinszky jones"|

## <a name="hierarchical-entity-data"></a>A hierarchikus adatok

[Hierarchikus](luis-concept-entity-types.md) entitások gép megtudhatta, és tartalmazhat egy szót vagy kifejezést. Gyermekek környezet azonosítja. Ha egy szülő-gyermek kapcsolat a pontos egyezés egyeztetése keres, használja egy [lista](#list-entity-data) entitás.

`book 2 tickets to paris`

Az előző utterance (kifejezés), a `paris` feliratú egy `Location::ToLocation` gyermeke a `Location` hierarchikus entitás.

A végpont által visszaadott szerepel az entitás nevét és a gyermek nevét, a felderített szöveget az utterance (kifejezés), a helyét a felderített szöveget, és a pontszám:

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Location::ToLocation",
    "startIndex": 18,
    "endIndex": 22,
    "score": 0.6866132
  }
]
```

|Objektum|Szülő|Gyermek|Érték|
|--|--|--|--|
|A hierarchikus|Hely|ToLocation|"Párizs"|

## <a name="composite-entity-data"></a>Összetett Entitásadatok
[Összetett](luis-concept-entity-types.md) entitások gép megtudhatta, és tartalmazhat egy szót vagy kifejezést. Vegyük példaként egy összetett entitást, előre elkészített `number` és `Location::ToLocation` az a következő utterance (kifejezés):

`book 2 tickets to paris`

Figyelje meg, hogy `2`, száma, és `paris`, a ToLocation van közöttük, amelyek nem részei a bármelyikével szavakat. A zöld, szerepel a címkézett utterance (kifejezés) az aláhúzás a [LUIS](luis-reference-regions.md) -webhely azt jelzi, hogy egy összetett entitást.

![Összetett entitást](./media/luis-concept-data-extraction/composite-entity.png)

A visszaadott összetett entitások egy `compositeEntities` is ad a tömb és az összetett lévő entitások a `entities` tömb:

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 18,
      "endIndex": 22,
      "score": 0.956998169
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "2 tickets to paris",
      "type": "Order",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.7714499
    }
  ],
  "compositeEntities": [
    {
      "parentType": "Order",
      "value": "2 tickets to paris",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Location::ToLocation",
          "value": "paris"
        }
      ]
    }
  ]
```    

|Objektum|Entitás neve|Érték|
|--|--|--|
|Előre összeállított entitások - szám|"builtin.number"|"2"|
|Hierarchikus entitás - helye|"Location::ToLocation"|"Párizs"|

## <a name="list-entity-data"></a>Entitások adatainak listája

A [lista](luis-concept-entity-types.md) entitás nem gép megtanult. Egy pontos egyezés egyeztetése. Egy listát a listában, és ezekhez az elemekhez tartozó szinonimák jelöli. A LUIS bármely lista egy elemének való egyezés a válasz egy egységként jelöli meg. A szinonimát egynél több lista is lehet.

Tegyük fel, hogy az alkalmazás rendelkezik egy nevű lista `Cities`, ami lehetővé teszi a városok nevei, beleértve a város, repülőtér (Sea-tac), a repülőtér kódja (SEA), a postai irányítószám (98101) és a telefonszám körzetszámát (206) változata.

|Listaelem|Elemet a szinonimák|
|---|---|
|Seattle|tenger-tac, tenger 98101, 206, + 1 |
|Párizs|CDG, leltár, roissy 75001, 1, +33|

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
[Reguláris kifejezés](luis-concept-entity-types.md) entitások felderített alapján egy reguláris kifejezés egyeztetése adnia az entitás létrehozásakor kifejezés használatával. Használata esetén `kb[0-9]{6}` reguláris kifejezés definícióját, a következő JSON-választ az egy példa utterance (kifejezés) a lekérdezés a visszaadott reguláris kifejezés entitásokkal `When was kb123456 published?`:

```JSON
{
  "query": "when was kb123456 published?",
  "topScoringIntent": {
    "intent": "FindKBArticle",
    "score": 0.933641255
  },
  "intents": [
    {
      "intent": "FindKBArticle",
      "score": 0.933641255
    },
    {
      "intent": "None",
      "score": 0.04397359
    }
  ],
  "entities": [
    {
      "entity": "kb123456",
      "type": "KB number",
      "startIndex": 9,
      "endIndex": 16
    }
  ]
}
```

## <a name="extracting-names"></a>Nevek kibontása
Nevének lekérése az utterance (kifejezés) azért nehéz, mert a neve betűket és szavakat szinte bármilyen kombinációja lehet. Milyen típusú neve meg van kinyeréséhez, függően több lehetőség közül választhat. Az alábbi javaslatokat nem tartoznak szabályok, de további útmutatást.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Előre összeállított PersonName és GeographyV2 entitások hozzáadása

[PersonName](luis-reference-prebuilt-person.md) és [GeographyV2](luis-reference-prebuilt-geographyV2.md) entitások érhetők el az egyes [nyelvi kulturális környezetek](luis-reference-prebuilt-entities.md). 

### <a name="names-of-people"></a>Személyek nevét
Emberek neve nem lehet néhány kisebb formátum nyelvi és kulturális környezet függően. Vezetéknév és utónév gyermekeként vagy egy hierarchikus entitás használata, vagy egy egyszerű entitás a szerepkörök az utónév és Vezetéknév. Ügyeljen arra, hogy példákkal szemlélteti, hogy az első és utolsó az utterance (kifejezés), a különböző hosszúságú kimondott szöveg és a kimondott szöveg különböző részein nevét használja az összes szándék fog vonatkozni, akár a egy sem szándék. [Felülvizsgálat](luis-how-to-review-endoint-utt.md) végpont utterances rendszeresen bármely címkenevek nem jelzett megfelelően.

### <a name="names-of-places"></a>Helyek nevei
Hely neve beállítása és ismert, például a város, megyék, államok, megyék és országok. Ha az alkalmazás egy helyen tudja készletét használja, fontolja meg egy lista entitást. Ha meg kell keresnie az összes helyezze el a neveket, hozzon létre egy egyszerű entitás, és adja meg a különböző példákat. Adja hozzá a hely nevének megerősítése milyen helyen nevek néz ki az alkalmazásban kifejezés listáját. [Felülvizsgálat](luis-how-to-review-endoint-utt.md) végpont utterances rendszeresen bármely címkenevek nem jelzett megfelelően.

### <a name="new-and-emerging-names"></a>Új és újonnan megjelenő neve
Bizonyos alkalmazásokhoz kell tudni új és újonnan felbukkanó nevek, például a termékek vagy cégek keresése. Az ilyen típusú nevek az adatok kinyerése a legbonyolultabb típusa. Egy egyszerű entitás előtaggal kell kezdődnie, és adja hozzá a kifejezések listáját. [Felülvizsgálat](luis-how-to-review-endoint-utt.md) végpont utterances rendszeresen bármely címkenevek nem jelzett megfelelően.

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
Pattern.any entitásokat is használt sablon utterances, a változó hosszúságú entitások egy [minta](luis-concept-patterns.md).

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

## <a name="data-matching-multiple-list-entities"></a>Több lista entitás megfelelő adatok

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
