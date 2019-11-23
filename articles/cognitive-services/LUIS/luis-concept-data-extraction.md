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
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: ff0a9838d1fcc9db3b6cc25b47c840e01056e6cd
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703151"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Adatok kinyerése a teljes szövegből szándékok és entitások alapján
A LUIS teszi lehetővé a felhasználó a természetes nyelvű utterances lekérni adatait. Az adatokat oly módon, hogy használat szerint a program, alkalmazás vagy csevegőrobot műveletet ki kell olvasni. A következő szakaszban megtudhatja, milyen adatokat küld vissza, a szándékok és entitások példákkal a JSON.

A kinyerni kívánt legnehezebb adatok a géppel megtanult adatok, mert nem pontos szöveges egyezés. A gépi megtanult [entitások](luis-concept-entity-types.md) kinyerésének a [létrehozási ciklus](luis-concept-app-iteration.md) részét kell képeznie, amíg biztos abban, hogy megkapja a várt adatmennyiséget.

## <a name="data-location-and-key-usage"></a>Adatok helye és a kulcsot használat
A LUIS biztosít a közzétett adatait [végpont](luis-glossary.md#endpoint). A **HTTPS-kérést** (POST vagy GET) tartalmazza az utterance (kifejezés), valamint az egyes választható konfigurációk, például átmeneti és éles környezetekben.

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2 előrejelzési végpont kérése](#tab/V2)

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 előrejelzési végpont kérése](#tab/V3)

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

* * * 

A `appID` a LUIS-alkalmazás **Beállítások** lapján, valamint a Luis-alkalmazás szerkesztésekor a (`/apps/`után) URL-cím részeként érhető el. A `subscription-key` a végpont kulcs az alkalmazás lekérdezésekhez használja fel. Habár a LUIS megismerése közben ingyenes authoring/Starter-kulcsot is használhat, fontos, hogy módosítsa a végpont kulcsát egy olyan kulcsra, amely támogatja a [várt Luis-használatot](luis-boundaries.md#key-limits). A `timezoneOffset` egység érték perc.

A **HTTPS válaszokat** tartalmazza az összes, vagy az aktuális közzétett modell alapján a leképezés és entitás adatait, LUIS megadhatja, hogy az átmeneti és éles végpontot. A végpont URL-cím található a [LUIS](luis-reference-regions.md) webhelyén, a a **kezelés** részben, a a **kulcsokat és a végpontok** lap.

## <a name="data-from-intents"></a>Leképezések adatait
Az elsődleges adatokat a felső pontozási **leképezés neve**. A végpont válasz a következő:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 előrejelzési végpont válasza](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

* * * 

|Objektum|Adattípus|Az adatok helye|Érték|
|--|--|--|--|
|Szándék|Sztring|topScoringIntent.intent|"GetStoreInfo"|

Ha a Csevegőrobot vagy a LUIS-Calling alkalmazás egynél több leképezési pontszámon alapuló döntést tesz, adja vissza az összes cél pontszámát.


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 előrejelzési végpont válasza](#tab/V2)

Állítsa be a querystring paramétert, `verbose=true`. A végpont válasz a következő:

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 előrejelzési végpont válasza](#tab/V3)

Állítsa be a querystring paramétert, `show-all-intents=true`. A végpont válasz a következő:

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

* * * 

A leképezések vannak rendezve legmagasabbtól a legalacsonyabb pontszámot.

|Objektum|Adattípus|Az adatok helye|Érték|Pontszám|
|--|--|--|--|:--|
|Szándék|Sztring|[0]. szándék .intent|"GetStoreInfo"|0.984749258|
|Szándék|Sztring|[1]. szándék .intent|"Nincs"|0.0168218873|

Előre összeállított tartományok ad hozzá, ha a leképezés neve azt jelzi, hogy a tartományhoz, mint például `Utilties` vagy `Communication` valamint célja:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 előrejelzési végpont válasza](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

* * * 

|Domain|Objektum|Adattípus|Az adatok helye|Érték|
|--|--|--|--|--|
|Közművek|Szándék|Sztring|[0]. szándék .intent|"<b>Segédprogramok</b>. ShowNext"|
|Kommunikáció|Szándék|Sztring|[1]. szándék .intent|<b>Kommunikációs</b>. StartOver"|
||Szándék|Sztring|[a(z) 2]. szándék .intent|"Nincs"|


## <a name="data-from-entities"></a>Entitások adatainak
A legtöbb csevegőrobotok és alkalmazások kell több, mint a leképezés neve. Ez további, opcionális adatokat az utterance (kifejezés) a felderített entitások származik. Minden entitás típusa az egyezés különböző adatait adja vissza.

Egy egyetlen slovo nebo frázi v az utterance (kifejezés) meg tudja több entitást. Ebben az esetben minden ilyen entitás a pontszámot is visszaad.

Minden entitás rendszer adja vissza a **entitások** a válasz a végpontról tömb:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 előrejelzési végpont válasza](#tab/V3)

```JSON
"entities": {
    "name":["bob jones"],
    "number": [3]
}
```
További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

* * * 

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

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 előrejelzési végpont válasza](#tab/V3)

A querystring paraméter nélkül `verbose=true`:

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ]
}
```

A querystring paraméterrel `verbose=true`:

```json

"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "Dec 5th",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "number": [
            {
                "type": "builtin.number",
                "text": "1 360",
                "startIndex": 17,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "555",
                "startIndex": 23,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "1212",
                "startIndex": 27,
                "length": 4,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 360-555-1212",
                "startIndex": 17,
                "length": 14,
                "score": 1.0,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

* * * 
## <a name="regular-expression-entity-data"></a>Entitásadatok reguláris kifejezés

A [reguláris kifejezés entitás](reference-entity-regular-expression.md) kinyeri az entitást az Ön által megadott reguláris kifejezési minta alapján.

## <a name="extracting-names"></a>Nevek kibontása
Nevének lekérése az utterance (kifejezés) azért nehéz, mert a neve betűket és szavakat szinte bármilyen kombinációja lehet. Attól függően, hogy milyen típusú nevet szeretne kinyerni, több lehetőség közül választhat. A következő javaslatok nem szabályok, hanem további irányelvek.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Előre elkészített PersonName-és GeographyV2-entitások hozzáadása

A [PersonName](luis-reference-prebuilt-person.md) és a [GeographyV2](luis-reference-prebuilt-geographyV2.md) entitások bizonyos [nyelvi kultúrákban](luis-reference-prebuilt-entities.md)is elérhetők. 

### <a name="names-of-people"></a>Személyek nevét

Emberek neve nem lehet néhány kisebb formátum nyelvi és kulturális környezet függően. Használjon egy előre elkészített **[personName](luis-reference-prebuilt-person.md)** -entitást vagy egy olyan **[egyszerű entitást](luis-concept-entity-types.md#simple-entity)** , amely a vezetéknév és az utónév [szerepkörrel](luis-concept-roles.md) rendelkezik. 

Ha az egyszerű entitást használja, ügyeljen arra, hogy olyan példákat adjon meg, amelyek az utónév és a vezetéknév különböző részeiben használják a hosszúságú kimondott szöveg, és hosszúságú kimondott szöveg az összes szándékot, beleértve a none szándékot is. [Felülvizsgálat](luis-how-to-review-endoint-utt.md) végpont utterances rendszeresen bármely címkenevek nem jelzett megfelelően.

### <a name="names-of-places"></a>Helyek nevei

A helyek nevei be vannak állítva és ismertek, például városok, megyék, Államok, tartományok és országok/régiók. Az előre elkészített entitások **[geographyV2](luis-reference-prebuilt-geographyv2.md)** kinyerheti a hely adatait.

### <a name="new-and-emerging-names"></a>Új és újonnan megjelenő neve

Bizonyos alkalmazásokhoz kell tudni új és újonnan felbukkanó nevek, például a termékek vagy cégek keresése. Az ilyen típusú nevek a legnehezebb kinyerési típus. Kezdje egy **[egyszerű entitással](luis-concept-entity-types.md#simple-entity)** , és adjon hozzá egy [kifejezést tartalmazó listát](luis-concept-feature.md). [Felülvizsgálat](luis-how-to-review-endoint-utt.md) végpont utterances rendszeresen bármely címkenevek nem jelzett megfelelően.

## <a name="pattern-roles-data"></a>A minta szerepkörök adatok
Szerepkörök az entitások környezetfüggő különbségek.


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 előrejelzési végpont válasza](#tab/V2)

Az entitás neve `Location`, és két szerepkör, `Origin` és `Destination`.

```JSON
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
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 előrejelzési végpont válasza](#tab/V3)

A v3-as verzióban a **szerepkör neve** az objektum elsődleges neve. 

Az entitás neve `Location`, és két szerepkör, `Origin` és `Destination`.

A querystring paraméter nélkül `verbose=true`:

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "Origin": [
        "seattle"
    ],
    "Destination": [
        "redmond"
    ]
}
```

A querystring paraméterrel `verbose=true`:

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "LocationOrigin": [
        "seattle"
    ],
    "LocationDestination": [
        "redmond"
    ],
    "$instance": {
        "Employee": [
            {
                "type": "Employee",
                "text": "bob jones",
                "startIndex": 5,
                "length": 9,
                "score": 0.982873261,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Origin": [
            {
                "role": "Origin",
                "type": "Location",
                "text": "seattle",
                "startIndex": 20,
                "length": 7,
                "score": 0.9913306,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "redmond",
                "startIndex": 31,
                "length": 7,
                "score": 0.898179531,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]

}
```

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

* * *

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


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 előrejelzési végpont válasza](#tab/V3)

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

A querystring paraméter nélkül `verbose=true`:

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ]
}
```

A querystring paraméterrel `verbose=true`:

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "map of places",
                "startIndex": 11,
                "length": 13,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "beautiful views",
                "startIndex": 30,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "favorite trail",
                "startIndex": 51,
                "length": 14,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

* * *


## <a name="data-matching-multiple-entities"></a>Az adatokat több entitás megfelelő

A LUIS felderítését az utterance (kifejezés) az összes entitásokat ad vissza. Ennek eredményeképpen a csevegőrobot szükségessé eredményei alapján döntéseket. Az utterance (kifejezés) az utterance (kifejezés) számos entitás is rendelkeznek:

`book me 2 adult business tickets to paris tomorrow on air france`

A LUIS-végpont ugyanazokat az adategységeket képes felderíteni, mint a különböző entitásokban.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 előrejelzési végpont válasza](#tab/V3)

`verbose=true` nélkül querystring paraméterként.

```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ]
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ]
}
```

`verbose=true` querystring paraméterként.


```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 8,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "PassengerCategory": [
                    {
                        "type": "PassengerCategory",
                        "text": "adult",
                        "startIndex": 10,
                        "length": 5,
                        "score": 0.9503733,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "TravelClass": [
                    {
                        "type": "TravelClass",
                        "text": "business",
                        "startIndex": 16,
                        "length": 8,
                        "score": 0.950095,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ],
    "$instance": {
        "TicketsOrder": [
            {
                "type": "TicketsOrder",
                "text": "2 adult business",
                "startIndex": 8,
                "length": 16,
                "score": 0.942183256,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Location::LocationTo": [
            {
                "type": "Location::LocationTo",
                "text": "paris",
                "startIndex": 36,
                "length": 5,
                "score": 0.9905354,
                "modelTypeId": 3,
                "modelType": "Hierarchical Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "tomorrow",
                "startIndex": 42,
                "length": 8,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Airline": [
            {
                "type": "Airline",
                "text": "air france",
                "startIndex": 54,
                "length": 10,
                "score": 0.9455415,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

* * *

## <a name="data-matching-multiple-list-entities"></a>Több lista entitások megfeleltetése

Ha egy szót vagy kifejezést megegyezik a listában több entitást, a végpont lekérdezés minden egyes lista entitás adja vissza.

A lekérdezés `when is the best time to go to red rock?`, és az alkalmazás még szó `red` egynél több listában LUIS felismeri az összes entitást és a JSON-végpont válasz részeként entitásokat tömbjét adja vissza: 

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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



#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 előrejelzési végpont válasza](#tab/V3)

A lekérdezési karakterláncban `verbose=true` nélkül:

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ]
        }
    }
}
```


`verbose=true` a lekérdezési karakterláncban:

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ],
            "$instance": {
                "Colors": [
                    {
                        "type": "Colors",
                        "text": "red",
                        "startIndex": 31,
                        "length": 3,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "Cities": [
                    {
                        "type": "Cities",
                        "text": "red rock",
                        "startIndex": 31,
                        "length": 8,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

* * *

## <a name="next-steps"></a>Következő lépések

Lásd: [entitások hozzáadása](luis-how-to-add-entities.md) további információ entitások hozzáadása a LUIS-alkalmazás.
