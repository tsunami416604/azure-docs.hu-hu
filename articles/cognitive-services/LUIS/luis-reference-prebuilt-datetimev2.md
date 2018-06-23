---
title: Előre elkészített LUIS entitások datetimeV2 referencia - Azure |} Microsoft Docs
titleSuffix: Azure
description: Ez a cikk datetimeV2 rendelkezik előre elkészített entitás információ a nyelvi ismertetése (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 261f6f27c39c280efdcd070888d735374a473c85
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321889"
---
# <a name="datetimev2-entity"></a>DatetimeV2 entitás

A **datetimeV2** előre elkészített entitás bontja ki a dátum és idő értékét. Ezek az értékek ügyfélprogramok felhasználásához a szabványos formában oldható fel. Amikor egy utterance dátum és idő, amely nem teljes, tartalmaz-e LUIS _múltbeli és a jövőbeni értékeket_ a végpont-válaszban. Az entitás már be van tanítva, mert nem kell adja hozzá a az alkalmazás leképezések datetimeV2 tartalmazó példa utterances. 

## <a name="types-of-datetimev2"></a>DatetimeV2 típusai
DatetimeV2 felügyelete a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) Github-adattár

## <a name="example-json"></a>Példa JSON 
Az alábbi példa JSON-válasz egy `datetimeV2` altípusa entitás `datetime`. Más típusú datetimeV2 entitások, tekintse meg a [datetimeV2 altípusainak](#subtypes-of-datetimev2)</a>.

```JSON
"entities": [
  {
    "entity": "8am on may 2nd 2017",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 15,
    "endIndex": 30,
    "resolution": {
      "values": [
        {
          "timex": "2017-05-02T08",
          "type": "datetime",
          "value": "2017-05-02 08:00:00"
        }
      ]
    }
  }
]
  ```

## <a name="json-property-descriptions"></a>JSON tulajdonságleírások

|Tulajdonság neve |A tulajdonság típusa és leírása|
|---|---|
|Entitás|**karakterlánc** -dátum, idő, dátumtartomány vagy időtartomány típusú utterance kinyert szöveg.|
|type|**karakterlánc** - egy, a [datetimeV2 altípusainak](#subtypes-of-datetimev2)
|startIndex|**int** -indexe, a utterance, ahol az entitás kezdődik.|
|endIndex értéke|**int** -indexe, a utterance az entitás véget ér.|
|feloldás|Rendelkezik egy `values` tömb, amely rendelkezik egy, kettő vagy négy [feloldási értékének](#values-of-resolution).|
|vége|Egy, vagy a dátumtartományt, ugyanabban a formában, mint a záró értéket `value`. Csak akkor használható, ha `type` van `daterange`, `timerange`, vagy `datetimerange`|

## <a name="subtypes-of-datetimev2"></a>DatetimeV2 altípusainak

A **datetimeV2** előre elkészített entitásnak van a következő altípus, és az egyes példák az alábbi táblázatban szerepelnek:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

## <a name="values-of-resolution"></a>Megoldási értékei
* A tömb egy elemmel rendelkezik, ha a dátum vagy idő a utterance teljesen megadott és egyértelmű.
* A tömb két elemmel rendelkezik, ha a datetimeV2 érték nem egyértelmű. Kétértelműség tartalmazza az adott év, idő vagy időtartomány hiánya. Lásd: [nem egyértelmű dátumok](#ambiguous-dates) példákat. Ha az idő nem egyértelmű az óra vagy kor, mindkét értékek tartoznak.
* A tömb négy elemmel rendelkezik, ha a utterance kétértelműség két olyan elemet tartalmaz. Ez magában foglalja, amelyek:
  * Egy dátumot vagy dátumtartományt, amely nem egyértelmű, hogy év
  * Egy time vagy nem egyértelmű, reggel időtartomány or P.M. Ha például 3. április 3:00.

Egyes elemeinek a `values` tömb lehet a következő mezőket: 

|Tulajdonság neve|Tulajdonság leírása|
|--|--|
|Timex|idő, dátum vagy dátumtartomány alábbi TIMEX formátumban kifejezett a [ISO 8601 szabványnak](https://en.wikipedia.org/wiki/ISO_8601) és a TimeML nyelv jegyzet TIMEX3 attribútumait. A jegyzet ismertetett a [TIMEX irányelvek](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|type|A következő altípus, amely a következők egyike lehet: dátum és idő, dátum, idő, daterange, timerange, datetimerange, időtartam, beállítása.|
|érték|**Nem kötelező.** Egy datetime objektumban óó: pp: (idő) yyyy:MM:dd óó: pp: (datetime) formátumban yyyy:MM:dd (dátum). Ha `type` van `duration`, másodpercben (időtartam) értéke <br/> Csak akkor használható, ha `type` van `datetime` vagy `date`, `time`, vagy "időtartam.|

## <a name="valid-date-values"></a>Érvényes dátumértékek

A **datetimeV2** támogatja a következő tartományok közé eső dátumokat:

| Min | Max |
|----------|-------------|
| 1900. január 1-jétől.   | 2099. December 31. |

## <a name="ambiguous-dates"></a>Nem egyértelmű dátumok

Ha a dátuma a múltban vagy későbbi lehet, LUIS biztosít mindkét értéket. Példa: egy utterance, amely tartalmazza a hónap és dátum az év nélkül.  

Ha például adott "Lehet, hogy 2." utterance:
* 3. Előfordulhat, hogy 2017 mai dátum esetén LUIS biztosít "2017-05-02" és a "2018-05-02" értéket. 
* 1. Előfordulhat, hogy 2017 esetén a mai dátum LUIS biztosít "2016-05-02" és a "2017-05-02" értéket.

A következő példa bemutatja a felbontást az entitás "lehet, hogy 2.". Ez a megoldás feltételezi, hogy a mai dátum közötti 2. Előfordulhat, hogy 2017 és az 1. Előfordulhat, hogy 2018 dátum.
A mezők `X` a a `timex` mező részei a dátum, amely nem a utterance explicit módon megadott.

```JSON
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
            "value": "2017-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2018-05-02"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-numeric-date"></a>Dátum tartomány feloldási példák numerikus dátuma

A `datetimeV2` entitás dátum- és időponttartományok bontja ki. A `start` és `end` mezőben adja meg a kezdő és a tartomány végéig. A "Lehet, hogy 2. 5. lehetséges, hogy a" utterance LUIS biztosít **daterange** az aktuális év és a következő évre értékeit. Az a `timex` mezőben a `XXXX` értékek azt jelzik, év egyértelműek. `P3D` azt jelzi, hogy az adott időszakban érték három nap hosszú.

```JSON
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
            "start": "2017-05-02",
            "end": "2017-05-05"
          },
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2018-05-02",
            "end": "2018-05-05"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-day-of-week"></a>Dátum tartomány megoldás például a hét napja

A következő példa bemutatja, hogyan használja a LUIS **datetimeV2** a utterance "Csütörtökre kedd" megoldásához. Ebben a példában az aktuális dátumot 19. június. LUIS tartalmaz **daterange** mindkét elé, és hajtsa végre az aktuális dátumot dátumtartományok értékeket.

```JSON
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
            "start": "2017-06-13",
            "end": "2017-06-15"
          },
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2017-06-20",
            "end": "2017-06-22"
          }
        ]
      }
    }
  ]
```
## <a name="ambiguous-time"></a>Nem egyértelmű időtartományba
Az értékek tömb rendelkezik idő két elemet, ha az idő vagy időtartomány nem egyértelmű. Ha van egy nem egyértelmű időtartományba értékek rendelkezik-e mind a de. és délután alkalommal.

## <a name="time-range-resolution-example"></a>Idő tartomány megoldás – példa

A következő példa bemutatja, hogyan használja a LUIS **datetimeV2** , amely rendelkezik egy időtartományt a utterance megoldásához.

```
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

## <a name="deprecated-prebuilt-datetime"></a>Elavult előre elkészített dátum és idő

A `datetime` előre elkészített entitás elavult, és helyébe [ `datetimeV2` ](#builtindatetimev2). 

Cserélje le a `datetime` rendelkező `datetimeV2` az LUIS alkalmazásban az alábbi lépésekkel:

1. Nyissa meg a **entitások** LUIS webes interfész ablaktáblán. 
2. Törölje a **datetime** előre elkészített entitás.
3. Kattintson a **előre elkészített entitás hozzáadása**
4. Válassza ki **datetimeV2** kattintson **mentése**.

## <a name="next-steps"></a>További lépések

További tudnivalók a [dimenzió](luis-reference-prebuilt-dimension.md), [e-mail](luis-reference-prebuilt-email.md) entitásokat, és [szám](luis-reference-prebuilt-number.md). 

