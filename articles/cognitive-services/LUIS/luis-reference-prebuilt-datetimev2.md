---
title: DatetimeV2-előkészített entitások – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk datetimeV2 rendelkezik előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/07/2020
ms.author: diberry
ms.openlocfilehash: 30132983f37323e798efd330f5cc8f15c0a9d2b6
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270737"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>DatetimeV2 előre összeépített entitása egy LUIS-alkalmazáshoz

A **datetimeV2** előre elkészített entitása Kinyeri a dátum-és időértékeket. Ezek az értékek ügyfélprogramok használhat szabványos formában oldható fel. Ha egy Kimondás olyan dátummal vagy időponttal rendelkezik, amely nem fejeződött be, LUIS a végponti válaszban a _múltbeli és a jövőbeli értékeket_ is tartalmazza. Az entitás már be van tanítva, mert nem kell való az alkalmazás leképezések datetimeV2 tartalmazó példa beszédmódok hozzáadása.

## <a name="types-of-datetimev2"></a>DatetimeV2 típusai
A DatetimeV2 kezelése a [felismerők – Text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) tárházból történik.

## <a name="example-json"></a>Példa JSON-ban

A következő kifejezés és a részleges JSON-válasza alább látható.

`8am on may 2nd 2019`

#### <a name="v3-response"></a>[V3 válasz](#tab/1-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/1-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.datetime",
                "text": "8am on may 2nd 2019",
                "startIndex": 0,
                "length": 19,
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

#### <a name="v2-response"></a>[V2 válasz](#tab/1-3)

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
 ```

|Tulajdonság neve |Typ vlastnosti és leírás|
|---|---|
|Entitás|**karakterlánc** – a kinyert szöveg a dátum, az idő, a dátumtartomány vagy az időtartomány típusával.|
|type|**String** – a datetimeV2 egyik [altípusa](#subtypes-of-datetimev2)
|startIndex|**int** – az entitás megkezdésének teljes alapjául szolgáló index.|
|endIndex|**int** – az az index, amelyben az entitás véget ér.|
|feloldás|Olyan `values` tömböt tartalmaz, amely egy, kettő vagy négy [feloldási értékkel](#values-of-resolution)rendelkezik.|
|vége|Egy időpont vagy dátumtartomány záró értéke a `value`formátummal megegyező formátumban. Csak akkor használatos, ha `type` `daterange`, `timerange`vagy `datetimerange`|

* * *

## <a name="subtypes-of-datetimev2"></a>A datetimeV2 altípus

A **datetimeV2** előre összeállított entitás a következő altípusokkal rendelkezik, és az egyes példák az alábbi táblázatban találhatók:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>Feloldási értékét
* A tömb egy elemmel rendelkezik, ha a dátum vagy idő az utterance (kifejezés) teljesen megadott és egyértelmű.
* A tömb két elemet tartalmaz, nem egyértelmű datetimeV2 érték esetén. Félreérthetőség tartalmazza az adott évig, idő vagy időtartomány hiánya. Példák a [kétértelmű dátumok](#ambiguous-dates) megtekintésére. Ha az idő nem egyértelmű a de. vagy fog sérülni, mindkét értékek szerepelnek.
* A tömb négy elemmel rendelkezik, ha az utterance (kifejezés) félreérthetőség két olyan elemet tartalmaz. Ez magában foglalja, amelyek:
  * Dátum vagy dátumtartomány, amely nem egyértelmű helyrendszerszerepkörökre év
  * Egy idő- vagy nem egyértelmű reggel esetleges szabályozási hiányosságok elhárítását időtartomány or P.M. Ha például 3. április 3:00.

A `values` tömb minden eleme a következő mezőkkel rendelkezhet:

|Tulajdonság neve|Tulajdonságleírás|
|--|--|
|Timex|az idő, a dátum vagy a dátumtartomány TIMEX formátumban kifejezve, amely az [ISO 8601 szabványt](https://en.wikipedia.org/wiki/ISO_8601) követi, valamint a jegyzet TIMEX3 attribútumait a TimeML nyelv használatával. Ez a jegyzet a [TIMEX irányelvek](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf)alapján van leírva.|
|mod|az érték használatának leírására szolgáló kifejezés, például `before`, `after`.|
|type|Az altípus, amely a következő elemek egyike lehet: `datetime`, `date`, `time`, `daterange`, `timerange`, `datetimerange`, `duration`, `set`.|
|érték|**Választható.** Egy datetime objektum a következő formátumban: éééé-hh-dd (Date), óó: PP: mm (Time) éééé-hh-nn óó: PP: mm (datetime). Ha `type` `duration`, az érték a másodpercek száma (időtartam) <br/> Csak akkor használatos, ha `type` `datetime` vagy `date`, `time`vagy időtartam.|

## <a name="valid-date-values"></a>Érvényes dátumértéket

A **datetimeV2** a következő tartományok közötti dátumokat támogatja:

| Min | Max |
|----------|-------------|
| 1900. január 1-től.   | 2099. December 31-én. |

## <a name="ambiguous-dates"></a>Nem egyértelmű dátumok

Ha a dátuma a múltban vagy későbbi, a LUIS biztosít mindkét értéket. Ilyen például, az utterance (kifejezés), amely tartalmazza a hónap és év nélkül.

Például a következő Kimondás miatt:

`May 2nd`

* Ha a mai dátum 2017 május 3., akkor a LUIS értékekként nyújt "2017-05-02" és "2018-05-02".
* Ha a mai dátum 2017 május 1-től, LUIS biztosít mind a "2016-05-02", mind a "2017-05-02" értéket.

Az alábbi példa bemutatja a feloldás "május 2" entitás. Ez a megoldás feltételezi, hogy a mai dátum 2017 május 2. és 2018 május 1. között egy dátumot.
A `timex` mezőben `X` rendelkező mezők a nem explicit módon megadott dátum részei.

## <a name="date-resolution-example"></a>Példa a felbontásra


A következő kifejezés és a részleges JSON-válasza alább látható.

`May 2nd`

#### <a name="v3-response"></a>[V3 válasz](#tab/2-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/2-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "May 2nd",
                "startIndex": 0,
                "length": 7,
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

#### <a name="v2-response"></a>[V2 válasz](#tab/2-3)

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-numeric-date"></a>Dátum tartomány feloldási példák a numerikus dátuma

A `datetimeV2` entitás kibontja a dátum és idő tartományokat. A `start` és `end` mezők a tartomány elejét és végét határozzák meg. A teljes `May 2nd to May 5th`a LUIS az aktuális évhez és a következő évre **daterange** értékeket biztosít. A `timex` mezőben a `XXXX` érték jelzi az év kétértelmű értékét. `P3D` azt jelzi, hogy az időtartam három nap.

A következő kifejezés és a részleges JSON-válasza alább látható.

`May 2nd to May 5th`

#### <a name="v3-response"></a>[V3 válasz](#tab/3-1)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ]
}
```


#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/3-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "May 2nd to May 5th",
                "startIndex": 0,
                "length": 18,
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

#### <a name="v2-response"></a>[V2 válasz](#tab/3-3)

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-day-of-week"></a>Dátum tartomány feloldási példák a hét napja

Az alábbi példa azt szemlélteti, hogyan használja a LUIS a **datetimeV2** a teljes `Tuesday to Thursday`feloldásához. Ebben a példában az aktuális dátum későbbi, június 19-én. A LUIS mindkét **daterange** tartalmazza, amelyek megelőzik és követik az aktuális dátumot.

A következő kifejezés és a részleges JSON-válasza alább látható.

`Tuesday to Thursday`

#### <a name="v3-response"></a>[V3 válasz](#tab/4-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/4-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "Tuesday to Thursday",
                "startIndex": 0,
                "length": 19,
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

#### <a name="v2-response"></a>[V2 válasz](#tab/4-3)

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="ambiguous-time"></a>Nem egyértelmű idő
Az értékek tömb van ideje két elemet, ha a az idő, vagy az időtartomány nem egyértelmű. Ha egy nem egyértelmű ideje, mind a reggel rendelkeznie és a délután időpontok.

## <a name="time-range-resolution-example"></a>Idő tartomány feloldási példa

DatetimeV2 JSON-válasz módosult az API v3-ben. Az alábbi példa azt szemlélteti, hogyan használja a LUIS a **datetimeV2** -t az időtartományhoz tartozó teljes érték feloldásához.

Változások az API v2-ből:
* `datetimeV2.timex.type` tulajdonságot a rendszer már nem adja vissza, mert a szülő szinten adja vissza, `datetimev2.type`.
* A `datetimeV2.value` tulajdonság át lett nevezve `datetimeV2.timex`re.

A következő kifejezés és a részleges JSON-válasza alább látható.

`from 6pm to 7pm`

#### <a name="v3-response"></a>[V3 válasz](#tab/5-1)

A következő JSON a `verbose` paraméterrel van beállítva `false`:

```JSON

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/5-2)

A következő JSON a `verbose` paraméterrel van beállítva `true`:

```json

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.timerange",
                "text": "from 6pm to 7pm",
                "startIndex": 0,
                "length": 15,
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
#### <a name="v2-response"></a>[V2 válasz](#tab/5-3)

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

* * *

## <a name="time-resolution-example"></a>Példa az időbeli felbontásra

A következő kifejezés és a részleges JSON-válasza alább látható.

`8am`

#### <a name="v3-response"></a>[V3 válasz](#tab/6-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/6-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.time",
                "text": "8am",
                "startIndex": 0,
                "length": 3,
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
#### <a name="v2-response"></a>[V2 válasz](#tab/6-3)

```json
"entities": [
  {
    "entity": "8am",
    "type": "builtin.datetimeV2.time",
    "startIndex": 0,
    "endIndex": 2,
    "resolution": {
      "values": [
        {
          "timex": "T08",
          "type": "time",
          "value": "08:00:00"
        }
      ]
    }
  }
]
```

* * *

## <a name="deprecated-prebuilt-datetime"></a>Elavult előre összeállított dátum és idő

A `datetime` előre összeépített entitás elavult, és a **datetimeV2**váltja fel.

Ha az `datetime`t a LUIS-alkalmazás `datetimeV2`ával szeretné helyettesíteni, hajtsa végre a következő lépéseket:

1. Nyissa meg a LUIS webes felület **entitások** paneljét.
2. Törölje a **datetime** előre elkészített entitást.
3. Kattintson az **előre összeépített entitás hozzáadása** lehetőségre.
4. Válassza a **datetimeV2** elemet, majd kattintson a **Mentés**gombra.

## <a name="next-steps"></a>Következő lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

Ismerje meg a [dimenziót](luis-reference-prebuilt-dimension.md), az [e-mail](luis-reference-prebuilt-email.md) -entitásokat és a [számokat](luis-reference-prebuilt-number.md).

