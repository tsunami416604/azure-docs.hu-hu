---
title: Alkalmazás bővítése futásidőben - LUIS
description: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: c0f9d71f5d89d73d9cdce2a2f646859d8eba3adc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538577"
---
# <a name="extend-app-at-prediction-runtime"></a>Alkalmazás kiterjesztése előrejelzési runtime-on

Az alkalmazás sémája (modellek és funkciók) be tanítása és közzététele az előrejelzési végponton. Ez a közzétett modell az előrejelzési futásidejű. Az előrejelzési futásidejű előrejelzési idő a felhasználó utterances, valamint a felhasználó utterance (kifejezés) továbbíthatja.

Két előrejelzési futásidejű séma módosításai a következők:
* [Külső entitások](#external-entities)
* [Dinamikus listák](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>Külső entitások

A külső entitások lehetővé teszik a LUIS-alkalmazás számára az entitások azonosítását és címkézését futásidőben, amelyek meglévő entitások szolgáltatásaként használhatók. Ez lehetővé teszi, hogy saját különálló és egyéni entitáskibontók használata előtt lekérdezések az előrejelzési végpontra. Mivel ez történik a lekérdezés előrejelzési végpont, nem kell újrabetanítása és közzététele a modell.

Az ügyfél-alkalmazás biztosítja a saját entitás kinyerő kezelő kezelő entitás egyeztető és meghatározza a helyét az adott egyező entitás utterance (kifejezés) az adott entitás, majd elküldi az információt a kéréssel.

A külső entitások bármely entitástípus kiterjesztésének mechanizmusai, miközben továbbra is jelekként használják más modelleknek.

Ez olyan entitás esetében hasznos, amely csak a lekérdezés-előrejelzés imént rendelkezik adatokkal. Ilyen típusú adatok például a felhasználónkénti adatok vagy meghatározott adatok folyamatosan változnak. A LUIS kapcsolattartó entitást kiterjesztheti a felhasználó partnerlistájából származó külső adatokkal.

A külső entitások a V3-as szerzői API részét képezik. További információ az erre a [verzióra való áttérésről.](luis-migration-api-v3.md)

### <a name="entity-already-exists-in-app"></a>Az entitás már létezik az alkalmazásban

A külső `entityName` entitás értéke, átadott a végpont kérés oszlop, már léteznie kell a betanított és közzétett alkalmazás a kérelem megküldésekén. Az entitás típusa nem számít, minden típus támogatott.

### <a name="first-turn-in-conversation"></a>A beszélgetés első fordulója

Fontolja meg az első kimondott szöveget egy csevegőrobot-beszélgetésben, ahol a felhasználó a következő hiányos információkat adja meg:

`Send Hazem a new message`

A kérelem a csevegőrobot a LUIS átadhatja `Hazem` az információkat a POST szervezetben, így közvetlenül illeszkedik, mint a felhasználó egyik kapcsolatok.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Az előrejelzési válasz tartalmazza, hogy a külső entitás, az összes többi előre jelzett entitások, mert a kérelemben van definiálva.

### <a name="second-turn-in-conversation"></a>Második fordulat a beszélgetésben

A következő felhasználói utterance (kifejezés) a chat bot használ egy homályos kifejezés:

`Send him a calendar reminder for the party.`

Ebben a fordulóban a beszélgetés, `him` a kimondott szöveg használ hivatkozásként `Hazem`. A társalgási csevegési robot `him` a POST törzsében leképezheti az `Hazem`első utterance (kifejezés) kinyert entitásértéket.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Az előrejelzési válasz tartalmazza, hogy a külső entitás, az összes többi előre jelzett entitások, mert a kérelemben van definiálva.

### <a name="override-existing-model-predictions"></a>Meglévő modell-előrejelzések felülbírálása

A `preferExternalEntities` beállítások tulajdonság azt határozza meg, hogy ha a felhasználó olyan külső entitást küld, amely átfedésben van egy azonos nevű előre jelzett entitással, a LUIS kiválasztja az átadott entitást vagy a modellben meglévő entitást.

Vegyük például `today I'm free`a lekérdezést. A LUIS `today` datetimeV2-ként észleli a következő választ:

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Ha a felhasználó elküldi a külső entitást:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

Ha `preferExternalEntities` a `false`beállítás a , a LUIS függvény úgy ad vissza választ, mintha a külső entitást nem küldték volna el.

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Ha `preferExternalEntities` a beállítás `true`a , a LUIS függvény a következő választ adja vissza:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Megoldás:

A _választható_ `resolution` tulajdonság visszaadja az előrejelzési választ, amely lehetővé teszi, hogy adja át a külső entitáshoz társított metaadatokat, majd megkapja azt a válaszban.

Az elsődleges cél az előre összeállított entitások kiterjesztése, de ez nem korlátozódik az adott entitástípusra.

A `resolution` tulajdonság lehet szám, karakterlánc, objektum vagy tömb:

* "Dallas"
* {"szöveg": "érték"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>Dinamikus listák

A dinamikus listák lehetővé teszik egy meglévő betanított és közzétett listaentitás kiterjesztését, már a LUIS alkalmazásban.

Akkor használja ezt a funkciót, ha a listaentitás értékeit rendszeresen módosítani kell. Ez a funkció lehetővé teszi egy már betanított és közzétett listaentitás kiterjesztését:

* A lekérdezés-előrejelzési végpont-kérelem időpontjában.
* Egyetlen kérésre.

A listaentitás üres lehet a LUIS alkalmazásban, de léteznie kell. A lista entitás a LUIS alkalmazásban nem változik, de az előrejelzési képesség a végponton kiterjeszti, hogy legfeljebb 2 listák körülbelül 1000 elem.

### <a name="dynamic-list-json-request-body"></a>Dinamikus lista JSON kérelem törzse

Küldje be a következő JSON-törzset, hogy szinonimákat tartalmazó új allistát adjon `LUIS`a `POST` listához, és előre jelezze a szöveg listaentitását, a lekérdezés-előrejelzési kérelemmel:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

Az előrejelzési válasz tartalmazza ezt a listaentitást, az összes többi előre jelzett entitással együtt, mert az a kérelemben van meghatározva.

## <a name="next-steps"></a>További lépések

* [Előrejelzési pontszám](luis-concept-prediction-score.md)
* [Az API V3-módosítások készítése](luis-migration-api-v3.md)
