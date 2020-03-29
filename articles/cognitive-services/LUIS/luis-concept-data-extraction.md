---
title: Adatok kinyerése - LUIS
description: Adatok kinyerése utterance (kifejezés) szöveg leképezések és entitások. Ismerje meg, hogy milyen típusú adatok nyerhetők ki a nyelvi megértés (LUIS) kinyeréséből.
author: diberry
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 1c1a744c06e5347625fb96518bd809481ee797e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221083"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Adatok kinyerése utterance (kifejezés) szövegből szándékokkal és entitásokkal
A LUIS lehetővé teszi, hogy információt szerezzen be a felhasználó természetes nyelvi utterances. Az információ kinyerése oly módon történik, hogy egy program, alkalmazás vagy csevegőrobot felhasználhatja a műveletekhez. A következő szakaszokban megtudhatja, hogy milyen adatokat ad nak vissza a szándékok és entitások a JSON példákkal.

A legnehezebb enyelegaagép által megtanult adatok, mert azok nem pontos szövegegyezés. A gép által megtanult [entitások](luis-concept-entity-types.md) adatkinyerése a [szerzői ciklus](luis-concept-app-iteration.md) részét kell, hogy legyen, amíg biztos nem abban, hogy megkapja a várt adatokat.

## <a name="data-location-and-key-usage"></a>Az adatok helye és a kulcshasználat
A LUIS a közzétett [végpont](luis-glossary.md#endpoint)adatait biztosítja. A **HTTPS-kérelem** (POST vagy GET) tartalmazza az utterance (kifejezés) valamint néhány választható konfigurációk, például átmeneti vagy éles környezetben.

#### <a name="v2-prediction-endpoint-request"></a>[V2 előrejelzési végpontra vonatkozó kérés](#tab/V2)

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

#### <a name="v3-prediction-endpoint-request"></a>[V3 előrejelzési végpontra vonatkozó kérés](#tab/V3)

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

* * *

Az `appID` elérhető a **LUIS-alkalmazás Beállítások** lapján, valamint az `/apps/`URL-cím egy részén (utána) a LUIS-alkalmazás szerkesztésekor. A `subscription-key` az alkalmazás lekérdezéséhez használt végpontkulcs. Bár használhatja az ingyenes authoring/starter kulcsot a LUIS tanulása közben, fontos, hogy a végpontkulcsot egy olyan kulcsra módosítsa, amely támogatja a [várt LUIS-használatot.](luis-boundaries.md#key-limits) Az `timezoneOffset` egység percekalatt van.

A **HTTPS-válasz** tartalmazza az összes szándék- és entitásinformációt, amelyet a LUIS az átmeneti vagy éles végpont aktuálisan közzétett modellje alapján határozhat meg. A végpont URL-címe megtalálható a LUIS-webhelyen, a **Kezelés** szakaszban, a **Kulcsok és a végpontok** lapon. [LUIS](luis-reference-regions.md)

## <a name="data-from-intents"></a>Szándékokból származó adatok
Az elsődleges adatok a legmagasabb pontozási **szándék neve.** A végpont válasza:

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpontra adott válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpontra adott válasza](#tab/V3)

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

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

* * *

|Adatobjektum|Adattípus|Adatok helye|Érték|
|--|--|--|--|
|Szándék|Sztring|topScoringIntent.intent|"GetStoreInfo"|

Ha a csevegőrobot vagy a LUIS-hívó alkalmazás egynél több szándékpontszám alapján hoz döntést, adja vissza az összes szándék pontszámát.


#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpontra adott válasza](#tab/V2)

Állítsa be a `verbose=true`querystring paramétert, . A végpont válasza:

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpontra adott válasza](#tab/V3)

Állítsa be a `show-all-intents=true`querystring paramétert, . A végpont válasza:

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

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

* * *

A szándékok a legmagasabbtól a legalacsonyabb pontszámig vannak rendezve.

|Adatobjektum|Adattípus|Adatok helye|Érték|Pontszám|
|--|--|--|--|:--|
|Szándék|Sztring|szándékok[0].szándék|"GetStoreInfo"|0.984749258|
|Szándék|Sztring|szándékok[1].szándék|"Nincs"|0.0168218873|

Ha előre összeállított tartományokat ad hozzá, a szándék `Utilties` `Communication` neve jelzi a tartományt, például vagy a szándékot:

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpontra adott válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpontra adott válasza](#tab/V3)

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

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

* * *

|Domain|Adatobjektum|Adattípus|Adatok helye|Érték|
|--|--|--|--|--|
|Segédprogramok|Szándék|Sztring|szándékok[0].szándék|"<b>Közművek</b>. ShowNext"|
|Kommunikáció|Szándék|Sztring|szándékok[1].szándék|<b>Kommunikáció</b>. StartOver"|
||Szándék|Sztring|szándékok[2].szándék|"Nincs"|


## <a name="data-from-entities"></a>Entitásoktól származó adatok
A legtöbb csevegőrobotnak és alkalmazásnak többre van szüksége a szándék nevénél. Ez a további, választható adatok az utterance (kifejezés) felderített entitások származik. Az entitások minden típusa különböző információkat ad vissza az egyezésről.

Egy szó vagy kifejezés egy utterance (kifejezés) egynél több entitás egyezik. Ebben az esetben minden egyező entitás a pontszámmal jelenik meg.

A válasz **entitástömbjében** a rendszer minden entitást visszaad a végpontról:

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpontra adott válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpontra adott válasza](#tab/V3)

```JSON
"entities": {
    "name":["bob jones"],
    "number": [3]
}
```
További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

* * *

## <a name="tokenized-entity-returned"></a>Tokenizált entitás tért vissza

Tekintse át a [jogkivonat-támogatást](luis-language-support.md#tokenization) a LUIS-ban.

## <a name="simple-entity-data"></a>Egyszerű entitásadatok

Egy [egyszerű entitás](reference-entity-simple.md) egy gép által megtanult érték. Ez lehet egy szó vagy kifejezés.

## <a name="composite-entity-data"></a>Összetett entitásadatok

Az [összetett entitás](reference-entity-composite.md) más entitásokból áll, például előre összeállított entitásokból, egyszerű, reguláris kifejezésből és listaentitásokból. A különálló entitások egy egész entitást alkotnak.

## <a name="list-entity-data"></a>Entitásadatok listázása

[A listaentitások](reference-entity-list.md) a kapcsolódó szavak rögzített, zárt készletét és szinonimáit jelölik. A LUIS nem észlel további értékeket a listaentitásokhoz. Az **Ajánlás** funkcióval az aktuális lista alapján megtekintheti az új szavakra vonatkozó javaslatokat. Ha egynél több listaentitás van ugyanazzal az értékkel, akkor a végpontlekérdezésben minden entitás visszakerül.

## <a name="prebuilt-entity-data"></a>Előre összeállított entitásadatok
[Az előre összeállított](luis-concept-entity-types.md) entitások felderítése a nyílt forráskódú [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) projekt használatával egy reguláris kifejezésegyezés alapján található. Az előre összeállított entitások az entitások tömbjében kerülnek `builtin::`visszaadásra, és a típusnevet használják. A következő szöveg egy példa utterance kifejezés a visszaadott előre összeállított entitások:

`Dec 5th send to +1 360-555-1212`

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpontra adott válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpontra adott válasza](#tab/V3)

A querystring paraméter `verbose=true`nélkül:

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

A querystring paraméterrel: `verbose=true`

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

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

* * *
## <a name="regular-expression-entity-data"></a>Reguláris kifejezés entitásadatai

A [reguláris kifejezés entitás](reference-entity-regular-expression.md) kinyeri az entitást a megadott reguláris kifejezésminta alapján.

## <a name="extracting-names"></a>Nevek kibontása
Nevek beszerzése egy utterance (kifejezés) nehéz, mert a név lehet szinte bármilyen kombinációja betűk és szavak. Attól függően, hogy milyen típusú nevet von ki, számos lehetősége van. A következő javaslatok nem szabályok, hanem további irányelvek.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Előre összeállított PersonName és GeographyV2 entitások hozzáadása

[A PersonName](luis-reference-prebuilt-person.md) és [a GeographyV2](luis-reference-prebuilt-geographyV2.md) entitások bizonyos [nyelvi kultúrákban elérhetők.](luis-reference-prebuilt-entities.md)

### <a name="names-of-people"></a>Személyek nevei

Az emberek neve lehet némi formában attól függően, hogy a nyelv és a kultúra. Használjon előre összeállított **[personName](luis-reference-prebuilt-person.md)** entitást vagy **[egy egyszerű entitást,](luis-concept-entity-types.md#simple-entity)** amelynek vezeték- és [vezetéknév-szerepkörei](luis-concept-roles.md) vannak.

Ha az egyszerű entitást használja, győződjön meg róla, hogy példákat, amelyek a first és a vezetéknév az utterance (kifejezés) különböző részein, a különböző hosszúságú utterances és utterances minden szándék között, beleértve a Nincs szándék. [Tekintse át](luis-how-to-review-endoint-utt.md) a végpont kimondott szövegrendszeresen a címkét a neveket, amelyek nem előre jelzett helyesen.

### <a name="names-of-places"></a>Helyek nevei

A helynevek beállítva vannak, és ismertek, például városok, megyék, államok, tartományok és országok/régiók. Az előre összeállított entitás **[geographyV2](luis-reference-prebuilt-geographyv2.md)** használatával kinyerheti a helyadatokat.

### <a name="new-and-emerging-names"></a>Új és új onnan létrebújó nevek

Egyes alkalmazásoknak képesnek kell lenniük új és újonnan megjelenő nevek, például termékek vagy vállalatok megkeresésére. Az ilyen típusú nevek a legnehezebb adatkinyerési típusok. Kezdje egy **[egyszerű entitással,](luis-concept-entity-types.md#simple-entity)** és adjon hozzá egy [kifejezéslistát](luis-concept-feature.md). [Tekintse át](luis-how-to-review-endoint-utt.md) a végpont kimondott szövegrendszeresen a címkét a neveket, amelyek nem előre jelzett helyesen.

## <a name="pattern-roles-data"></a>Mintaszerepkörök adatai
A szerepkörök az entitások környezetfüggő különbségei.


#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpontra adott válasza](#tab/V2)

Az entitás `Location`neve két `Origin` szerepkörrel, és `Destination`.

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpontra adott válasza](#tab/V3)

A V3-ban a **szerepkör neve** az objektum elsődleges neve.

Az entitás `Location`neve két `Origin` szerepkörrel, és `Destination`.

A querystring paraméter `verbose=true`nélkül:

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

A querystring paraméterrel: `verbose=true`

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

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

* * *

## <a name="patternany-entity-data"></a>Pattern.any entitás adatok

[Pattern.any](reference-entity-pattern-any.md) egy változó hosszúságú helyőrző csak a minta sablon utterance (kifejezés) megjelölésére, ahol az entitás kezdődik és végződik.

## <a name="sentiment-analysis"></a>Hangulatelemzés
Ha a hangulatelemzés konfigurálva van, a LUIS json válasz hangulatelemzést is tartalmaz. További információ a véleményelemzésről a [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) dokumentációjában.

### <a name="sentiment-data"></a>Hangulatadatok
A hangulatadatok az adatok pozitív (1-hez közelebbi) vagy negatív (0-hoz közelebbi) hangulatát jelző 1 és 0 közötti pontszám.

Amikor a `en-us`kultúra , a válasz:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

Minden más kultúra esetében a válasz:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Kulcskifejezés-kivonási entitás adatai
A kulcskifejezés-kivonási entitás a [Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)által biztosított utterance (kifejezés) kulcskifejezéseket adja vissza.


#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpontra adott válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpontra adott válasza](#tab/V3)

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

A querystring paraméter `verbose=true`nélkül:

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ]
}
```

A querystring paraméterrel: `verbose=true`

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

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

* * *


## <a name="data-matching-multiple-entities"></a>Több entitásnak megfelelő adategyeztetés

A LUIS az utterance (kifejezés) felderített összes entitást adja vissza. Ennek eredményeképpen előfordulhat, hogy a csevegőrobotnak az eredmények alapján kell döntenie. Egy utterance (kifejezés) egy utterance (kifejezés) kifejezés:

`book me 2 adult business tickets to paris tomorrow on air france`

A LUIS-végpont felderítheti ugyanazokat az adatokat a különböző entitásokban.

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpontra adott válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpontra adott válasza](#tab/V3)

Lekérdezési karakterlánc-paraméter nélkül. `verbose=true`

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

Querystring `verbose=true` paraméterként.


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

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

* * *

## <a name="data-matching-multiple-list-entities"></a>Több listaentitásnak megfelelő adat

Ha egy szó vagy kifejezés egynél több listaentitásnak felel meg, a végpontlekérdezés minden Lista entitást visszaad.

A lekérdezés `when is the best time to go to red rock?`, és az `red` alkalmazás a szó több listában, LUIS felismeri az összes entitást, és a JSON-végpont válasz részeként egy tömbentitást ad vissza:

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpontra adott válasza](#tab/V2)

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



#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpontra adott válasza](#tab/V3)

Anélkül, `verbose=true` hogy a lekérdezési karakterlánc:

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


A `verbose=true` lekérdezési karakterláncban:

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

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

* * *

## <a name="next-steps"></a>További lépések

További információ az [entitások hozzáadása](luis-how-to-add-entities.md) a LUIS-alkalmazáshoz való hozzáadásáról.
