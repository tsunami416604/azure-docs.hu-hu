---
title: Adatok kinyerése jellemzői LUIS - Azure megértése |} Microsoft Docs
description: Ismerje meg, hogy milyen típusú adatok kiolvasható a nyelvi ismertetése (LUIS)
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 8d8620a1c53037be6f1a33083f41964655a04921
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112116"
---
# <a name="data-extraction"></a>Adatok kinyerése
LUIS lehetővé teszi az adatok lehívása egy felhasználó természetes nyelvű utterances. Az adatokat oly módon, hogy azt használhatja a program, alkalmazás vagy chatbot cselekedhetnek. ki kell olvasni.

A következő szakaszban megtudhatja, milyen adatokat leképezések és a kapcsolódó JSON entitások küld vissza. A nagyon nehéz adatok kibontása a számítógép-megtanulta, adatok, mert nem egy szöveges pontos egyezés. A számítógép által ismert adatok kivonása [entitások](luis-concept-entity-types.md) részét kell a [ciklus szerzői](luis-concept-app-iteration.md) amíg nem biztos benne, hogy a várt adatokat fogad. 

## <a name="data-location-and-key-usage"></a>Adatok helye és a kulcs használata
LUIS biztosít a közzétett adatait [végpont](luis-glossary.md#endpoint). A **HTTPS-kérést** (a POST vagy a GET) tartalmaz a utterance, valamint néhány választható konfigurációk, például az átmeneti vagy üzemi környezetben. 

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

A `appID` érhető el a **beállítások** oldalán a LUIS függvényalkalmazás és az URL-cím (után `/apps/`) LUIS alkalmazást szerkesztésekor. A `subscription-key` lekérdezése az alkalmazásban használt végpont kulcsa. Használhatja az ingyenes szerzői/alapszintű kulcs LUIS való ismerkedés során, fontos, hogy módosítsa a végpont kulcs egy kulcsot, amely támogatja a [várható LUIS használati](luis-boundaries.md#key-limits). A `timezoneOffset` egység érték perc.

A **HTTPS válasz** tartalmazza az összes LUIS megállapíthatja, hogy a célt és egyéb entitások adatokat az aktuális közzétett modell a következők alapján az átmeneti vagy üzemi végpont. A végpont URL-címe megtalálható a [LUIS] [ LUIS] webhely **közzététel** lap. 

## <a name="data-from-intents"></a>Leképezések adatait
Az elsődleges adatok felső pontozási **leképezési neve**. Használja a `MyStore` [gyors üzembe helyezés](luis-quickstart-intents-only.md), a rendszer a végpont választ:

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
|Kísérlet történt|Sztring|topScoringIntent.intent|"GetStoreInfo"|

Ha a chatbot vagy LUIS-hívása alkalmazás lehetővé teszi több leképezési pontszám alapján döntés, térjen vissza a leképezések pontszámok úgy, hogy a lekérdezési karakterlánc paraméter `verbose=true`. A rendszer a végpont választ:

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

A leképezések a legalacsonyabb pontszám a legnagyobb vannak rendezve.

|Objektum|Adattípus|Az adatok helye|Érték|Pontszám|
|--|--|--|--|:--|
|Kísérlet történt|Sztring|[0] leképezések .intent|"GetStoreInfo"|0.984749258|
|Kísérlet történt|Sztring|[1] leképezések .intent|"Nincs"|0.0168218873|

Előre elkészített tartományokat ad hozzá, ha a leképezési neve, mint jelzi-e a tartomány `Utilties` vagy `Communication` és a leképezés:

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
    
|Tartomány|Objektum|Adattípus|Az adatok helye|Érték|
|--|--|--|--|--|
|Közművek|Kísérlet történt|Sztring|[0] leképezések .intent|"<b>Segédprogramok</b>. ShowNext"|
|Kommunikáció|Kísérlet történt|Sztring|[1] leképezések .intent|<b>Kommunikációs</b>. StartOver"|
||Kísérlet történt|Sztring|[2] leképezések .intent|"Nincs"|


## <a name="data-from-entities"></a>Entitások adatait
A legtöbb chatbots és alkalmazásokat kell a több, mint a leképezési neve. Ez további, opcionális adatok felderítését a utterance az entitások származik. Minden entitás típusa a egyeznek különböző információt ad vissza. 

Egy egyetlen szó vagy kifejezés egy utterance is megegyezhet egynél több entitás. Ebben az esetben minden egyező entitást a pontszámot is visszaad. 

Minden entitás vissza a **entitások** a választ kapott a végpont a tömb:

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

## <a name="tokenized-entity-returned"></a>Visszaadott tokenekre entitás
Több [kulturális környezetek](luis-supported-languages.md#tokenization) térjen vissza a forrásentitás-objektum az az `entity` érték [tokenekre bontott](luis-glossary.md#token). A startIndex és a forrásentitás-objektum a LUIS által visszaadott endIndex van leképezve a tokenekre, új érték hanem ahhoz, hogy a nyers entitás programozott módon bontsa ki az eredeti lekérdezést. 

Például a német, a word `das Bauernbrot` van a tokenekre bontott `das bauern brot`. A tokenekre érték `das bauern brot`, ad vissza, és az eredeti érték programozott módon lehet meghatározni a startIndex és az eredeti lekérdezés, felkínálva endIndex `das Bauernbrot`.

## <a name="simple-entity-data"></a>Egyszerű Entitásadatok

A [egyszerű entitás](luis-concept-entity-types.md) gép megtanulta érték. Lehet, hogy egy szót vagy kifejezést. 

`Bob Jones wants 3 meatball pho`

Az előző utterance a `Bob Jones` van egy egyszerű címkézve `Customer` entitás.

A végpont által visszaadott adatokat az entitás nevét, a utterance felderített szöveget, a helyét, valamint a felderített szöveget a pontszám tartalmazza:

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
|Egyszerű entitás|"Ügyfél"|"Belinszky János"|

## <a name="hierarchical-entity-data"></a>A hierarchikus adatok

[Hierarchikus](luis-concept-entity-types.md) entitások gép megtanulta, és egy szót vagy kifejezést tartalmazhat. Gyermekek azonosítja a környezetben. Ha a szülő-gyermek kapcsolat a szöveg pontos egyezés, használja a [lista](#list-entity-data) entitás. 

`book 2 tickets to paris`

Az előző utterance a `paris` lett címkézve egy `Location::ToLocation` gyermeke a `Location` a hierarchikus. 

A végpont által visszaadott adatokat az entitás nevét és a gyermek nevét, a utterance felderített szöveget, a helyét, valamint a felderített szöveget a pontszám tartalmazza: 

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
|--|--|--|--|--|
|A hierarchikus|Hely|ToLocation|"Párizsi"|

## <a name="composite-entity-data"></a>Összetett Entitásadatok
[Összetett](luis-concept-entity-types.md) entitások gép megtanulta, és egy szót vagy kifejezést tartalmazhat. Vegye figyelembe például egy összetett entitást az előre elkészített `number` és `Location::ToLocation` , a következő utterance:

`book 2 tickets to paris`

Figyelje meg, hogy `2`, számot, és `paris`, a ToLocation közöttük, amelyek nem részei, azokat az entitásokat szavak rendelkezik. A zöld aláhúzási, egy címkézett utterance a szerepel a [LUIS] [ LUIS] webhelyen, az azt jelzi, egy összetett entitást.

![Összetett entitás](./media/luis-concept-data-extraction/composite-entity.png)

Összetett entitások vissza egy `compositeEntities` tömb és az összetett belül minden entitásokat is vissza a `entities` tömb:

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
|Előre elkészített entitás - szám|"builtin.number"|"2"|
|Hierarchikus entitás - helye|"Location::ToLocation"|"Párizsi"|

## <a name="list-entity-data"></a>Entitás listaadatai

A [lista](luis-concept-entity-types.md) entitás nem gép-nal. Egy szöveges pontos egyezés. Egy listát olyan cikkekhez szinonimák mellett a listán szereplő elemek jelöli. LUIS listát elemen egyező egy entitásként a válaszul jelöli meg. Egy szinonima egynél több lista lehet. 

Tegyük fel, hogy az alkalmazás még a listáját, és nevű `Cities`, így módosuló város nevek, beleértve a város repülőtéri (tenger-tac), a repülőtéri kód (SZE), a postai irányítószám (98101) és a telefon körzetszám (206). 

|Listaelem|Elem szinonimák|
|---|---|
|Seattle|tenger-tac, tengeri, 98101, 206, + 1 |
|Párizs|CDG, roissy, leltár, 75001, 1, +33|

`book 2 tickets to paris`

Az előző utterance, a word a `paris` Párizsi elem részeként van rendelve a `Cities` entitás listában. A lista entitás mind a cikk normalizált nevet, valamint az elemet már megegyezik. 

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

Egy másik példa utterance, Párizsi szinonimát használja:

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

## <a name="prebuilt-entity-data"></a>Előre elkészített Entitásadatok
[Előre elkészített](luis-concept-entity-types.md) entitások alapján használja a nyílt forráskódú reguláris kifejezéssel egyező felderített [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text) projekt. Előre elkészített entitásokat a entitások tömb vissza, és használja a következő típusnév előtagként `builtin::`. A következő egy példa utterance a visszaadott előre elkészített entitásokkal áll:

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

## <a name="regular-expression-entity-data"></a>Reguláris kifejezés Entitásadatok
[Reguláris kifejezés](luis-concept-entity-types.md) entitások felderített megadta az entitás létrehozásakor eredményező kifejezés reguláris kifejezéssel egyező alapján. Használata esetén `kb[0-9]{6}` az reguláris kifejezés meghatározását, a következő JSON-válasz lesz a visszaadott reguláris kifejezés entitásokkal a lekérdezés egy példa utterance `When was kb123456 published?`:

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
Nevek lekérése egy utterance nem nehéz, mert a név lehet a betűk és szavak szinte bármilyen kombinációját. Attól függően, hogy milyen típusú nevet szeretné bontani több lehetőség közül választhat. Ezek nincsenek szabályok, de további útmutatást. 

### <a name="names-of-people"></a>Azoknak a személyeknek nevét
Népi neve lehet néhány enyhe formátum attól függően, hogy a nyelvi és kulturális környezet. Vagy a hierarchikus entitás első és utolsó nevével gyermekobjektumokkal vagy egy egyszerű entitás szerepkörök első és utolsó neve. Ügyeljen arra, hogy példákat mutatnak be, amely használja az első és utolsó a utterance utterances különböző hosszúságú, és utterances különböző részein közötti összes leképezések, beleértve a nincs szándék. [Felülvizsgálati](label-suggested-utterances.md) végpont utterances rendszeresen nem jelzett megfelelően nevek megjelölésére. 

### <a name="names-of-places"></a>Helyek nevei
Hely neve beállítása és például városokat, országok, állapotok, tartományok és országok ismert. Ha az alkalmazás olyan helyek megkaphat készletét használja, fontolja meg egy listában entitás. Ha található összes helyezze nevek van szüksége, hozzon létre egy egyszerű entitás, és példák számos. Adja meg kifejezést helyneveket megerősítése milyen helyi nevek megjelenését, például az alkalmazásban. [Felülvizsgálati](label-suggested-utterances.md) végpont utterances rendszeresen nem jelzett megfelelően nevek megjelölésére. 

### <a name="new-and-emerging-names"></a>Új és neve
Egyes alkalmazások kell új és nevét, például a termék vagy a vállalatok találja. Ez az adatok kinyerése a legbonyolultabb típusát. Egy egyszerű entitás kezdődnie, és adja hozzá a kódot listáját. [Felülvizsgálati](label-suggested-utterances.md) végpont utterances rendszeresen nem jelzett megfelelően nevek megjelölésére. 

## <a name="pattern-roles-data"></a>Minta szerepkörök adatok
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

## <a name="patternany-entity-data"></a>Pattern.any Entitásadatok
Pattern.any entitások használt sablon utterances, a változó hosszúságú entitások legyenek egy [mintát](luis-concept-patterns.md). 

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
Ha véleményeket elemzés van konfigurálva, a a LUIS json válasz tartalmazza véleményeket elemzés. További információ a céggel kapcsolatos véleményeket elemzés a [Szövegelemzések](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) dokumentációját.

### <a name="sentiment-data"></a>Véleményeket adatok
Véleményeket adatai között 1 vagy 0 az pozitív jelző pontszámot (közelebb 1) vagy negatív (0-ra közelebb) az adatok céggel kapcsolatos véleményeket.

Kulturális környezet esetén `en-us`, a válasz:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

Minden más országban használható a rendszer a választ:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Kulcs kifejezés kibontási Entitásadatok
A kulcs kifejezés kibontási entitás legfontosabb kifejezések adja vissza a utterance által biztosított [Szövegelemzések](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

<!-- TBD: verify JSON-->
```JSON
"keyPhrases": [
    "places",
    "beautiful views",
    "favorite trail"
]
```

## <a name="data-matching-multiple-entities"></a>Több entitás megfelelő adatok
LUIS felderítését a utterance az összes entitásokat ad vissza. Ennek eredményeképpen a chatbot kell a döntést az eredmények alapján. Egy utterance egy utterance sok entitás is van:

`book me 2 adult business tickets to paris tomorrow on air france`

A LUIS végpont fel tud deríteni a különféle entitásokat ugyanazokat az adatokat: 

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

## <a name="next-steps"></a>További lépések

Lásd: [entitások hozzáadása](luis-how-to-add-entities.md) entitások felvétele a LUIS app tájékozódhat.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions