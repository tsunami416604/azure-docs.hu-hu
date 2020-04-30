---
title: Adatbányászat – LUIS
description: Adatok kinyerése a teljes szövegből szándékok és entitások alapján. Ismerje meg, hogy milyen típusú adatok nyerhetők ki Language Understandingból (LUIS).
author: diberry
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 3cea4a46564210ad8c37fdeda68e24337091d0bb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82100294"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Adatok kinyerése a teljes szövegből szándékok és entitások alapján
A LUIS lehetővé teszi a felhasználók természetes nyelvi hosszúságú kimondott szöveg származó információk beszerzését. Az adatok kinyerése oly módon történik, hogy egy program, egy alkalmazás vagy egy csevegési bot is felhasználja a műveletet. A következő részekben megismerheti, hogy a rendszer milyen adatokhoz adja vissza a leképezéseket és entitásokat a JSON-példákkal.

A kinyerni kívánt legnehezebb adatok a géppel megtanult adatok, mert nem pontos szöveges egyezés. A gépi megtanult [entitások](luis-concept-entity-types.md) kinyerésének a [létrehozási ciklus](luis-concept-app-iteration.md) részét kell képeznie, amíg biztos abban, hogy megkapja a várt adatmennyiséget.

## <a name="data-location-and-key-usage"></a>Adatok helye és kulcshasználat
A LUIS a közzétett [végpont](luis-glossary.md#endpoint)adatait biztosítja. A **https-kérés** (post vagy Get) tartalmazza a kiválasztást, valamint néhány opcionális konfigurációt, például átmeneti vagy éles környezeteket.

#### <a name="v2-prediction-endpoint-request"></a>[V2 előrejelzési végpont kérése](#tab/V2)

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

#### <a name="v3-prediction-endpoint-request"></a>[V3 előrejelzési végpont kérése](#tab/V3)

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

* * *

A `appID` a Luis-alkalmazás **Beállítások** LAPJÁN, valamint a Luis-alkalmazás szerkesztésekor az URL-cím `/apps/`részeként is elérhető. A `subscription-key` az alkalmazás lekérdezéséhez használt végponti kulcs. Habár a LUIS megismerése közben ingyenes authoring/Starter-kulcsot is használhat, fontos, hogy módosítsa a végpont kulcsát egy olyan kulcsra, amely támogatja a [várt Luis-használatot](luis-limits.md#key-limits). Az `timezoneOffset` egység perc.

A **https-válasz** tartalmazza az összes olyan szándékot és entitási információt, amelyet a Luis az előkészítési vagy a termelési végpont aktuális közzétett modellje alapján meghatároz. A végpont URL-címe a [Luis](luis-reference-regions.md) webhelyén, a **kezelés** szakaszban, a **kulcsok és végpontok** oldalon található.

## <a name="data-from-intents"></a>Adatok a szándékokból
Az elsődleges érték a legfontosabb pontozási **leképezés neve**. A végpont válasza:

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)

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

|Adatobjektum|Adattípus|Adatok helye|Érték|
|--|--|--|--|
|Szándék|Sztring|topScoringIntent. szándék|"GetStoreInfo"|

Ha a Csevegőrobot vagy a LUIS-Calling alkalmazás egynél több leképezési pontszámon alapuló döntést tesz, adja vissza az összes cél pontszámát.


#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

Állítsa be a querystring paramétert `verbose=true`. A végpont válasza:

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)

Állítsa be a querystring paramétert `show-all-intents=true`. A végpont válasza:

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

A szándékok a legmagasabbtól a legalacsonyabb pontszámig vannak rendezve.

|Adatobjektum|Adattípus|Adatok helye|Érték|Pontszám|
|--|--|--|--|:--|
|Szándék|Sztring|leképezések [0]. szándék|"GetStoreInfo"|0,984749258|
|Szándék|Sztring|leképezések [1]. szándék|NEz egy|0,0168218873|

Ha előre elkészített tartományokat ad hozzá, a cél neve a tartományt jelöli, `Utilties` például `Communication` a vagy a szándékot:

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)

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

|Domain|Adatobjektum|Adattípus|Adatok helye|Érték|
|--|--|--|--|--|
|Segédprogramok|Szándék|Sztring|leképezések [0]. szándék|"<b>Segédprogramok</b>. ShowNext"|
|Kommunikáció|Szándék|Sztring|leképezések [1]. szándék|<b>Kommunikáció</b>. StartOver"|
||Szándék|Sztring|leképezések [2]. szándék|NEz egy|


## <a name="data-from-entities"></a>Entitásokból származó adatok
A legtöbb csevegőrobotok és alkalmazásnak nagyobbnak kell lennie, mint a leképezés neve. Ez a kiegészítő, opcionális adatok a kiválasztásban felderített entitásokból származnak. Az egyes entitások különböző adatokat adnak vissza a egyezésről.

Egy kifejezésben szereplő egyetlen szó vagy kifejezés több entitással is megegyező lehet. Ebben az esetben minden egyező entitás a pontszámával lesz visszaadva.

A rendszer az összes entitást visszaadja a végpont válaszának **entitások** tömbében:

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)

```JSON
"entities": {
    "name":["bob jones"],
    "number": [3]
}
```
További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

* * *

## <a name="tokenized-entity-returned"></a>Visszaadott jogkivonat-entitás

Tekintse át a LUIS-beli [jogkivonat-támogatást](luis-language-support.md#tokenization) .

## <a name="simple-entity-data"></a>Egyszerű entitások

Egy [egyszerű entitás](reference-entity-simple.md) egy géppel megtanult érték. Szó vagy kifejezés is lehet.

## <a name="composite-entity-data"></a>Összetett entitások adatvédelme

Az [összetett entitások](reference-entity-composite.md) más entitásokból állnak, mint például az előre összeépített entitások, az egyszerű, a reguláris kifejezések és a listázási entitások. A különálló entitások egész entitást alkotnak.

## <a name="list-entity-data"></a>Entitások listájának listázása

Az [entitások listája](reference-entity-list.md) a kapcsolódó szavak rögzített, lezárt készletét jelöli a szinonimákkal együtt. A LUIS nem észlel további értékeket a List entitások számára. Az **ajánlott** funkció használatával megtekintheti az új szavakra vonatkozó javaslatokat az aktuális lista alapján. Ha egynél több azonos értékű listaelem szerepel, a rendszer az összes entitást visszaadja a végponti lekérdezésben.

## <a name="prebuilt-entity-data"></a>Előre elkészített entitások
Az előre [elkészített](luis-concept-entity-types.md) entitásokat a rendszer a nyílt forráskódú [felismerők – Text](https://github.com/Microsoft/Recognizers-Text) projekt használatával reguláris kifejezési egyezés alapján deríti fel. Az előre elkészített entitásokat a rendszer az entitások tömbben adja vissza, és `builtin::`a (z) előtagú nevet használja. A következő szöveg egy példa a visszaadott előre elkészített entitásokra:

`Dec 5th send to +1 360-555-1212`

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)

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
## <a name="regular-expression-entity-data"></a>Reguláris kifejezés típusú entitások

A [reguláris kifejezés entitás](reference-entity-regular-expression.md) kinyeri az entitást az Ön által megadott reguláris kifejezési minta alapján.

## <a name="extracting-names"></a>Nevek kinyerése
A nevek kiírása nehéz, mert a név szinte bármilyen betű és szó kombinációja lehet. Attól függően, hogy milyen típusú nevet szeretne kinyerni, több lehetőség közül választhat. A következő javaslatok nem szabályok, hanem további irányelvek.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Előre elkészített PersonName-és GeographyV2-entitások hozzáadása

A [PersonName](luis-reference-prebuilt-person.md) és a [GeographyV2](luis-reference-prebuilt-geographyV2.md) entitások bizonyos [nyelvi kultúrákban](luis-reference-prebuilt-entities.md)is elérhetők.

### <a name="names-of-people"></a>Személyek nevei

A nyelvtől és a kulturális környezettől függően előfordulhat, hogy a személyek neve némileg eltérő lehet. Használjon egy előre elkészített **[personName](luis-reference-prebuilt-person.md)** -entitást vagy egy olyan **[egyszerű entitást](luis-concept-entity-types.md#simple-entity)** , amely a vezetéknév és az utónév [szerepkörrel](luis-concept-roles.md) rendelkezik.

Ha az egyszerű entitást használja, ügyeljen arra, hogy olyan példákat adjon meg, amelyek az utónév és a vezetéknév különböző részeiben használják a hosszúságú kimondott szöveg, és hosszúságú kimondott szöveg az összes szándékot, beleértve a none szándékot is. Rendszeresen [tekintse át](luis-how-to-review-endoint-utt.md) a végpontok hosszúságú kimondott szöveg a nem megfelelően előre jelzett nevek címkézéséhez.

### <a name="names-of-places"></a>Helyek nevei

A helyek nevei be vannak állítva és ismertek, például városok, megyék, Államok, tartományok és országok/régiók. Az előre elkészített entitások **[geographyV2](luis-reference-prebuilt-geographyv2.md)** kinyerheti a hely adatait.

### <a name="new-and-emerging-names"></a>Új és feltörekvő nevek

Néhány alkalmazásnak képesnek kell lennie új és újonnan megjelenő nevek, például termékek vagy vállalatok megtalálására. Az ilyen típusú nevek a legnehezebb kinyerési típus. Kezdje egy **[egyszerű entitással](luis-concept-entity-types.md#simple-entity)** , és adjon hozzá egy [kifejezést tartalmazó listát](luis-concept-feature.md). Rendszeresen [tekintse át](luis-how-to-review-endoint-utt.md) a végpontok hosszúságú kimondott szöveg a nem megfelelően előre jelzett nevek címkézéséhez.

## <a name="pattern-roles-data"></a>Minta szerepköreinek adatai
A szerepkörök az entitások kontextusbeli különbözősége.


#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

Az `Location`entitás neve két szerepkörrel `Origin` és. `Destination`

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)

A v3-as verzióban a **szerepkör neve** az objektum elsődleges neve.

Az `Location`entitás neve két szerepkörrel `Origin` és. `Destination`

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

## <a name="patternany-entity-data"></a>Minta. az entitások összes adathalmaza

[Minta.](reference-entity-pattern-any.md) a változó hosszúságú helyőrző csak a minta sablonjának megjelölésére szolgál, amely jelzi, hogy az entitás hol kezdődik és végződik.

## <a name="sentiment-analysis"></a>Hangulatelemzés
Ha a hangulati elemzés konfigurálva van, a LUIS JSON-válasz tartalmazza a hangulat elemzését. További információ az [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) dokumentációjában található érzelmek elemzéséről.

### <a name="sentiment-data"></a>Érzelmekkel kapcsolatos adatkezelés
Az érzelmi adatmennyiség egy 1 és 0 közötti pontszám, amely az adatmennyiség pozitív (közelebbről 1) vagy negatív (közelebbről 0) hangulatát jelzi.

Ha a kulturális `en-us`környezet, a válasz:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

Az összes többi kulturális környezet esetében a válasz a következő:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>A Key kifejezés kinyerő entitások adathalmaza
A kulcs kifejezés kinyerő entitása a [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)által megadott, a teljes szövegben szereplő kulcsfontosságú kifejezéseket adja vissza.


#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)

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


## <a name="data-matching-multiple-entities"></a>Több entitásnak megfelelő adathalmaz

LUIS visszaadja az összes felderített entitást a teljes verzióban. Ennek eredményeképpen előfordulhat, hogy a Csevegőrobot az eredmények alapján kell döntést hoznia. A Kimondás számos entitást tartalmazhat:

`book me 2 adult business tickets to paris tomorrow on air france`

A LUIS-végpont ugyanazokat az adategységeket képes felderíteni, mint a különböző entitásokban.

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)

QueryString `verbose=true` paraméter nélkül.

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

`verbose=true` Querystring paraméterként.


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

Ha egy szó vagy kifejezés több listával egyezik, a végpont lekérdezése az egyes listák entitásokat adja vissza.

A lekérdezéshez `when is the best time to go to red rock?`, és az alkalmazás több listában is `red` szerepel, a Luis felismeri az összes entitást, és az entitások tömbjét adja vissza a JSON-végpont válaszának részeként:

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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



#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)

A `verbose=true` lekérdezési sztring nélkül:

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


`verbose=true` A lekérdezési sztringben:

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

## <a name="next-steps"></a>További lépések

Az entitások LUIS-alkalmazáshoz való hozzáadásával kapcsolatos további tudnivalókért tekintse meg az [entitások hozzáadása](luis-how-to-add-entities.md) című témakört.
