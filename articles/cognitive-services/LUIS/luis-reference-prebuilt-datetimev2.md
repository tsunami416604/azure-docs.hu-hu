---
title: DateTimeV2 előre összeállított entitások - LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk datetimeV2 előre összeállított entitásadatokat tartalmaz a nyelvi ismeretek (LUIS) című témakörben.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270737"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>DateTimeV2 előre összeállított entitás egy LUIS-alkalmazáshoz

A **datetimeV2** előre összeállított entitás kinyeri a dátum és az idő értékeit. Ezek az értékek az ügyfélprogramok számára szabványosított formátumban oldódnak fel. Ha egy utterance (kifejezés) egy dátum vagy idő, amely nem teljes, luis tartalmazza _a korábbi és a jövőbeli értékeket_ a végpont válasz. Mivel ez az entitás már be van tanítva, nem kell hozzáadnia a datetimeV2-t tartalmazó példautterances az alkalmazás leképezések.

## <a name="types-of-datetimev2"></a>A datetimeV2 típusai
A DateTimeV2 kezelése a [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) GitHub-tárházból történik.

## <a name="example-json"></a>Példa JSON

A következő utterance (kifejezés) és a részleges JSON-válasz alább látható.

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

|Tulajdonság neve |Tulajdonság típusa és leírása|
|---|---|
|Entitás|**karakterlánc** – az utterance (kifejezés) kinyert szöveg dátum, idő, dátumtartomány vagy időtartomány típusával.|
|type|**string** - A [datetimeV2](#subtypes-of-datetimev2) egyik altípusa
|startIndex|**int** - Az index az utterance (kifejezés) amelyben az entitás kezdődik.|
|endIndex|**int** - Az index az utterance (kifejezés) amelyben az entitás véget ér.|
|Felbontás|Egy, `values` kettő vagy négy [felbontási értékkel](#values-of-resolution)bíró tömbbel rendelkezik.|
|befejezés|Egy idő vagy dátumtartomány záróértéke a `value`. Csak akkor `type` `daterange`használható, ha , `timerange`, vagy`datetimerange`|

* * *

## <a name="subtypes-of-datetimev2"></a>A datetimeV2 altípusai

A **datetimeV2** előre összeállított entitás a következő altípusokkal rendelkezik, és az alábbi táblázatban példák találhatók:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>A felbontás értékei
* A tömb egy elemet tartalmaz, ha a dátum vagy az idő az utterance (kifejezés) teljesen meg van adva, és egyértelmű.
* A tömb nek két eleme van, ha a datetimeV2 érték nem egyértelmű. A kétértelműség magában foglalja az adott év, idő vagy időtartomány hiányát. Példákat a [Nem egyértelmű dátumok](#ambiguous-dates) ban talál. Amikor az idő nem egyértelmű a délelőtt. vagy P.M., mindkét érték szerepel.
* A tömb négy elemet tartalmaz, ha az utterance (kifejezés) két elem kétértelműség. Ez a kétértelműség olyan elemeket tartalmaz, amelyek:
  * Az évhez képest nem egyértelmű dátum- vagy dátumtartomány
  * Egy idő- vagy időtartomány, ami nem egyértelmű, mint a sem. vagy délután. Például április 3:00.

A tömb `values` minden eleme a következő mezőkkel rendelkezhet:

|Tulajdonság neve|Tulajdonság leírása|
|--|--|
|Timex|time, date, or date range expressed in TIMEX format that follows the [ISO 8601 standard](https://en.wikipedia.org/wiki/ISO_8601) and the TIMEX3 attributes for annotation using the TimeML language. Ezt a jegyzetet a [TIMEX irányelvei ismertetik.](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf)|
|Mod|az érték (például `before`a ) `after`használatának leírására használt kifejezés .|
|type|Az altípus, amely a következő elemek `datetime` `date`egyike `time` `daterange`lehet: , , , `timerange`, `datetimerange`, , `duration`, , . `set`|
|érték|**Választható.** Datetime objektum a Format yyyy-MM-dd (dátum), ÓÓ:mm:ss (idő) yyyy-MM-dd Óó:mm:ss (datetime). Ha `type` `duration`van, akkor a másodpercek száma (időtartam) <br/> Csak akkor `type` `datetime` `date`használható, `time`ha vagy , vagy "időtartam.|

## <a name="valid-date-values"></a>Érvényes dátumértékek

A **datetimeV2** a következő tartományok közötti dátumokat támogatja:

| Min | Max |
|----------|-------------|
| 1900. január 1.   | 2099. december 31. |

## <a name="ambiguous-dates"></a>Nem egyértelmű dátumok

Ha a dátum lehet a múltban vagy a jövőben, a LUIS mindkét értéket tartalmazza. Egy példa egy utterance (kifejezés) amely magában foglalja a hónap és a dátum nélkül az év.

Például a következő utterance (kifejezés):

`May 2nd`

* Ha a mai dátum 2017.
* Ha a mai dátum 2017 május 1-je, a LUIS a "2016-05-02" és a "2017-05-02" értéket is tartalmazza.

A következő példa a "május 2" entitás felbontását mutatja be. Ez az állásfoglalás feltételezi, hogy a mai dátum 2017.
A `X` mezőben `timex` lévő mezők a dátum azon részei, amelyek nincsenek kifejezetten megadva az utterance (kifejezés) mezőben.

## <a name="date-resolution-example"></a>Példa dátumfeloldásra


A következő utterance (kifejezés) és a részleges JSON-válasz alább látható.

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

## <a name="date-range-resolution-examples-for-numeric-date"></a>Dátumtartomány-feloldási példák numerikus dátumhoz

Az `datetimeV2` entitás kinyeri a dátum- és időtartományokat. A `start` `end` és a mezők a tartomány elejét és végét határozzák meg. Az utterance `May 2nd to May 5th`(kifejezés) esetén a LUIS **daterange** értékeket biztosít az aktuális és a következő évre vonatkozóan. A `timex` mezőben `XXXX` az értékek az év kétértelműségét jelzik. `P3D`azt jelzi, hogy az időtartam három napos.

A következő utterance (kifejezés) és a részleges JSON-válasz alább látható.

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

## <a name="date-range-resolution-examples-for-day-of-week"></a>Dátumtartomány-megoldási példák a hét napjára

A következő példa bemutatja, hogyan használja a `Tuesday to Thursday`LUIS **a datetimeV2-t** az utterance (kifejezés) feloldásához. Ebben a példában az aktuális dátum június 19. A LUIS az aktuális dátumot megelőző és követő két dátumtartomány **dátumtartományértékeit** tartalmazza.

A következő utterance (kifejezés) és a részleges JSON-válasz alább látható.

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
Az értéktömb nek két időeleme van, ha az idő vagy az időtartomány nem egyértelmű. Ha van egy kétértelmű idő, értékek mind a a.m. és P.M. Alkalommal.

## <a name="time-range-resolution-example"></a>Példa az időtartomány felbontása

DateTimeV2 JSON válasz megváltozott az API V3. A következő példa bemutatja, hogyan használja a LUIS **a datetimeV2-t** az időtartományt tartalmazó utterance (kifejezés) feloldásához.

Változások az API V2-ről:
* `datetimeV2.timex.type`tulajdonság ot a rendszer már nem adja `datetimev2.type`vissza, mert a rendszer szülőszinten adja vissza, a .
* A `datetimeV2.value` tulajdonság átlett `datetimeV2.timex`nevezve a névre.

A következő utterance (kifejezés) és a részleges JSON-válasz alább látható.

`from 6pm to 7pm`

#### <a name="v3-response"></a>[V3 válasz](#tab/5-1)

A következő JSON `verbose` paraméter a `false`következő:

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

A következő JSON `verbose` paraméter a `true`következő:

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

## <a name="time-resolution-example"></a>Példa időfeloldásra

A következő utterance (kifejezés) és a részleges JSON-válasz alább látható.

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

## <a name="deprecated-prebuilt-datetime"></a>Előre összeállított előre összeállított dátumidő

Az `datetime` előre összeállított entitás elavult, és helyébe **datetimeV2**.

A `datetime` LUIS-alkalmazásban a lecseréléshez `datetimeV2` hajtsa végre az alábbi lépéseket:

1. Nyissa meg a LUIS webes felület **entitások** ablaktábláját.
2. Törölje a **datetime** előre összeállított entitást.
3. Kattintson **az Előre összeállított entitás hozzáadása elemre.**
4. Válassza a **datetimeV2 lehetőséget,** majd kattintson a **Mentés gombra.**

## <a name="next-steps"></a>További lépések

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

További információ a [dimenzióról,](luis-reference-prebuilt-dimension.md)az [e-mail](luis-reference-prebuilt-email.md) entitásokról és [a számról.](luis-reference-prebuilt-number.md)

