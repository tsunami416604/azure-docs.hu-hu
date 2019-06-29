---
title: V2 a V3 API-k áttelepítése
titleSuffix: Azure Cognitive Services
description: A 3-as verziójú végpont API-k megváltoztak. Ez az útmutató segítségével megismerheti a 3-as verziójú végpont API-k áttelepítése.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 4c08c95a05d4f22e2338a7264409aec0f64a4755
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442521"
---
# <a name="preview-migrate-to-api-version-3x-for-luis-apps"></a>Előzetes verzió: API-verzió át 3.x LUIS-alkalmazások

A lekérdezés előrejelzési végpont API-k megváltoztak. Ez az útmutató segítségével megismerheti a 3-as verziójú végpont API-k áttelepítése. 

A V3 API a következő új funkciók, többek között jelentős JSON kérelem és/vagy a válasz változásokat tartalmazza: 

* [Külső entitások](#external-entities-passed-in-at-prediction-time)
* [A dinamikus listák](#dynamic-lists-passed-in-at-prediction-time)
* [Előre összeállított entitások JSON-módosítások](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

A lekérdezés előrejelzési végpont [kérelem](#request-changes) és [válasz](#response-changes) jelentős változásokat támogatásához új a fent felsorolt szolgáltatások, többek között az alábbiakat:

* [Válasz objektum módosítása](#top-level-json-changes)
* [Szerepkör neve odkazy nA Entity helyett entitás neve](#entity-role-name-instead-of-entity-name)
* [Beszédmódok entitások megjelölni tulajdonságai](#marking-placement-of-entities-in-utterances)

A következő LUIS funkciókra **nem támogatott** a V3 API-ban:

* A Bing Spell ellenőrzés 7-es verziója

[Referenciadokumentációt](https://aka.ms/luis-api-v3) V3 érhető el.

## <a name="endpoint-url-changes-by-slot-name"></a>Végpont URL-cím módosításainak tárolóhely neve

A v3-as HTTP végpont hívás formátuma megváltozott.

|METÓDUS|URL-cím|
|--|--|
|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict?query=<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|||

## <a name="endpoint-url-changes-by-version-id"></a>Végpont URL-cím módosításainak Verzióazonosító

Ha szeretné lekérdezni a verziójával, akkor először [API-n keresztül közzétenni](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) együtt a `"directVersionPublish":true`. A lekérdezés a végpontot, tárolóhely neve helyett a verzió-Azonosítójára hivatkozik.


|METÓDUS|URL-cím|
|--|--|
|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict?query=<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict|
|||

## <a name="prebuilt-entities-with-new-json"></a>Az új JSON előre összeállított entitások

A v3-as választ objektum változások a következők [előre összeállított entitások](luis-reference-prebuilt-entities.md). 

## <a name="request-changes"></a>Módosítási kérelmek 

### <a name="query-string-parameters"></a>Lekérdezési karakterlánc paraméterei

A V3 API különböző lekérdezési karakterlánc paraméterei rendelkezik.

|Válaszparaméter-név|Típus|Version|Alapértelmezett|Cél|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|Query Store naplófájlban.| 
|`query`|string|Csak a v3|Nincs alapértelmezett érték - szükség a GET-kérés|**A v2-ben**, kell elvégezni, ha az utterance (kifejezés) szerepel a `q` paraméter. <br><br>**A v3-as**, az funkciók átadott a `query` paraméter.|
|`show-all-intents`|boolean|Csak a v3|false|Minden leképezések és a megfelelő pontszámot ad vissza a **prediction.intents** objektum. Leképezések a szülő objektumként adja vissza `intents` objektum. Ez lehetővé teszi a programozott hozzáférést anélkül, hogy a leképezés található tömb: `prediction.intents.give`. A v2-ben ezek visszaadott tömbben. |
|`verbose`|boolean|V2 & V3|false|**A v2-ben**, ha az értéke igaz, az összes előre jelzett leképezések adott vissza. Ha az összes előre jelzett leképezések van szüksége, használja a v3-as param, `show-all-intents`.<br><br>**A v3-as**, ez a paraméter csak biztosít entitás entitás prediction metaadat részleteit.  |



<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>A lekérdezés előrejelzési JSON törzs az a `POST` kérelem

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "overridePredictions": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Tulajdonság|Típus|Version|Alapértelmezett|Cél|
|--|--|--|--|--|
|`dynamicLists`|tömb|Csak a v3|Nem kötelező.|[A dinamikus listák](#dynamic-lists-passed-in-at-prediction-time) lehetővé teszi, hogy terjessze ki egy meglévő betanított és közzétett listája entitás már megtalálható a LUIS-alkalmazás.|
|`externalEntities`|tömb|Csak a v3|Nem kötelező.|[Külső entitások](#external-entities-passed-in-at-prediction-time) lehetővé teszik a LUIS-alkalmazás azonosíthatja és entitások felirat alatt modult, amely a meglévő entitások szolgáltatások használhatók. |
|`options.datetimeReference`|string|Csak a v3|Nincs alapértelmezett érték|Azt határozza meg, [datetimeV2 eltolás](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity).|
|`options.overridePredictions`|boolean|Csak a v3|false|Itt adhatja meg, ha a felhasználó [(amelynek a neve megegyezik a létező entitásba) külső entitás](#override-existing-model-predictions) szolgál, vagy a modellben a létező entitásba előrejelzéshez használatos. |
|`query`|string|Csak a v3|Kötelező.|**A v2-ben**, kell elvégezni, ha az utterance (kifejezés) szerepel a `q` paraméter. <br><br>**A v3-as**, az funkciók átadott a `query` paraméter.|



## <a name="response-changes"></a>Válasz módosítások

A lekérdezésekre adott válaszok JSON módosítani a leggyakrabban használt adatokat nagyobb programozás alapú hozzáférést. 

### <a name="top-level-json-changes"></a>Legfelső szintű JSON-módosítások

A felső JSON-tulajdonság a v2-ben, amikor `verbose` értéke "true", amely visszaadja az összes leképezések és azok pontszámait az `intents` tulajdonság:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

A v3-as felső JSON-tulajdonságokat a következők:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "normalizedQuery": "this is your utterance you want predicted - after normalization",
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

A `intents` objektum rendezetlen listáját. Az első, a gyermek nem feltételezi a `intents` felel meg a `topIntent`. Ehelyett használja a `topIntent` érték található a pontszám:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

A válasz JSON sémaváltozások engedélyezése:

* Törölje az eredeti utterance megkülönböztetése `query`, és az előrejelzési, visszaadott `prediction`.
* Egyszerűbb programozott hozzáférést előre jelzett adatokat. Helyett számbavétele a v2-ben egy tömb, is elérhetők értékek szerint **nevű** szándékok és entitások. Előre jelzett entitás szerepkörök esetében, mert egyedi között a teljes alkalmazás a szerepkör nevét adja vissza.
* Az adattípusok, határozza meg, ha betartását. Numerics már nem karakterláncként adja vissza.
* Első prioritás előrejelzési adatokat, illetve további metaadatokat, közötti különbséget adja vissza a `$instance` objektum. 

### <a name="access-instance-for-entity-metadata"></a>Hozzáférés `$instance` entitás-metaadatok

Entitás-metaadatok van szüksége, ha a lekérdezési karakterláncot kell használnia a `verbose=true` jelzőt és a válasz tartalmazza a metaadatokat a `$instance` objektum. A JSON-válaszok a következő szakaszban látható példák.

### <a name="each-predicted-entity-is-represented-as-an-array"></a>Mindegyik előre jelzett entitás jelölt tömbként

A `prediction.entities.<entity-name>` objektum egy tömböt tartalmaz, mivel minden entitás egynél többször az utterance (kifejezés) a előrejelezhető. 

### <a name="list-entity-prediction-changes"></a>Entitás előrejelzési változások felsorolása

A JSON-t egy lista entitás előrejelzési álló tömb lehet változott:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Minden egyes belső tömb felel meg az utterance (kifejezés) szöveget. A belső objektum egy tömb azért ugyanazt a szöveget egy lista entitás egynél több allista jelenhetnek meg. 

Amikor közötti megfeleltetés a `entities` az objektum a `$instance` objektumot, a rendszer megőrzi az objektumok sorrendje a lista entitás ismeretekkel.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>Entitás szerepkör neve helyett az entitás neve 

A v2-ben a `entities` tömböt adott vissza az előre jelzett entitásokat az egyedi azonosító alatt entitás nevét. A v3-as, ha szerepköröket az entitást használja, és az előrejelzési egy entitás szerepkör az elsődleges azonosító, amely a szerepkör neve. Ez akkor lehetséges, mert a szerepkör entitásnévnek egyedinek kell lennie a teljes alkalmazás, beleértve a más modell (szándék, entitás) nevét.

Az alábbi példában: fontolja meg az utterance (kifejezés), amely tartalmazza a szöveg, amelyet `Yellow Bird Lane`. Ez a szöveg várhatóan meghalad egy egyéni `Location` entitás szerepe `Destination`.

|Szöveg utterance (kifejezés)|Entitás neve|Szerepkörnév|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

A v2-ben az entitást azonosítja a _entitásnév_ a szerepét, az objektum olyan osztályát:

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

A v3-as, az entitás által hivatkozott a _entitás szerepkör_, ha az előrejelzés a szerepkörhöz:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

A v3-as, azonos vezethet a a `verbose` jelző entitás-metaadatok vissza:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

## <a name="external-entities-passed-in-at-prediction-time"></a>Előrejelzési időpontban átadott külső entitások

Külső entitások lehetővé teszik a LUIS-alkalmazás azonosíthatja és entitások felirat alatt modult, amely a meglévő entitások szolgáltatások használhatók. Ez lehetővé teszi, hogy a saját külön és egyéni entitás információkinyerők használja az előrejelzési végponthoz lekérdezések elküldése előtt. Ez történik, a lekérdezés előrejelzési végponton, mert nem kell újratanítása és közzétesszük a modellt.

Az ügyfélalkalmazás a saját entitás információkinyerő biztosít, mivel kezeli az entitás megfelelő és a megtalált entitásokkal belül az utterance (kifejezés) helyének meghatározásához, és elküldi ezt az információt a kérelemmel. 

Külső entitások a mechanizmusa bármely entitástípus kiterjesztése során más modellekhez, például a szerepköröket, az összetett és egyéb jelekkel, továbbra is használja.

Ez akkor hasznos, amelynek a rendelkezésre álló adatok csak futásidőben lekérdezés előrejelzési entitás. Példák ilyen típusú adatok folyamatosan változnak, adatok vagy a specifikus felhasználónként. Egy LUIS Kapcsolattartó entitás a felhasználó névjegylista külső adatok terjeszthetők ki. 

### <a name="entity-already-exists-in-app"></a>Entitás az alkalmazás már létezik

Az érték `entityName` esetében a külső, a végponti kérésből bejegyzés törzse, az átadott entitás már léteznie kell a betanított és a közzétett alkalmazást a kérés időpontjában. Entitás típusa nem számít, minden típusok támogatottak.

### <a name="first-turn-in-conversation"></a>Először kapcsolja be a beszélgetés

Fontolja meg egy első utterance (kifejezés) egy csevegés bot beszélgetésben, ahol a felhasználó beírja a következő hiányos adatok:

`Send Hazem a new message`

A kérés a csevegőrobot, LUIS kapcsolatos információkat a a bejegyzés törzse átadható `Hazem` így közvetlenül a felhasználók partneradatainak egyik program hozzá.

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

Az előrejelzési válasz tartalmazza a külső entitások, az összes többi előre jelzett entitást, mert meg van határozva a kérés.  

### <a name="second-turn-in-conversation"></a>A második kapcsolja be a beszélgetés

A következő felhasználó utterance (kifejezés) a csevegőrobot, egy további országról kifejezést használja:

`Send him a calendar reminder for the party.`

Az előző utterance (kifejezés), használja az utterance (kifejezés) `him` mutató hivatkozásként `Hazem`. A természetes nyelvi csevegőrobot, a bejegyzés törzse, leképezheti `him` az entitás értékre az első utterance kinyert `Hazem`.

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

Az előrejelzési válasz tartalmazza a külső entitások, az összes többi előre jelzett entitást, mert meg van határozva a kérés.  

### <a name="override-existing-model-predictions"></a>Bírálja felül a meglévő modell-előrejelzéseket

A `overridePredictions` beállítások tulajdonsága azt adja meg, hogy ha a felhasználó küld egy külső entitás, amely átfedésben van egy azonos nevű előre jelzett entitással, a LUIS úgy dönt, az átadott entitás vagy az entitás a modellben meglévő. 

Vegyük példaként a lekérdezés `today I'm free`. Észleli a LUIS `today` , a következő választ az egy datetimeV2:

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

Ha a felhasználó küld a külső entitás:

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

Ha a `overridePredictions` értékre van állítva `false`, LUIS választ adja vissza, mint ha a külső entitás nem lettek elküldve. 

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

Ha a `overridePredictions` értékre van állítva `true`, LUIS adja vissza a választ, többek között:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Megoldás:

A _választható_ `resolution` a válaszban visszalépési tulajdonság értéket ad vissza, az előrejelzési válaszként, lehetővé teszi, hogy adja át a külső entitáshoz társított metaadatokat, majd megkapni. 

Az elsődleges célja, hogy előre összeállított entitások kiterjesztése, de ez nem korlátozódik az adott entitás típusa. 

A `resolution` tulajdonságot akkor lehet egy szám, karakterlánc, objektum vagy tömb:

* "Dallas"
* {"text": "value"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Előrejelzési időpontban átadott dinamikus listák

Dinamikus listák lehetővé teszik meglévő betanított és közzétett listája entitás, a LUIS-alkalmazás már a kiterjesztése. 

Ez a funkció akkor használja, ha a lista entitásértékek rendszeresen módosítani kell. Ez a funkció lehetővé teszi, hogy terjessze ki az entitás már betanított és közzétett listája:

* A lekérdezés előrejelzési végpont kérés időpontjában.
* Egyetlen kérelem.

A lista entitás lehet üres. a LUIS-alkalmazás, de az még létezik. A LUIS alkalmazás a lista entitás nem változik, de az előrejelzési lehetővé teszi a végponton körülbelül 1000 tétel listák legfeljebb 2 terjeszteni.

### <a name="dynamic-list-json-request-body"></a>Dinamikus lista JSON-kérelem törzse

A következő JSON-törzse új allistát a szinonimák hozzáadása a listához elküldeni, és előrejelezheti a lista entitás az a szöveg, amelyet `LUIS`, az a `POST` lekérdezés előrejelzési kérelemnél:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntityName":"ProductList",
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

A prediction válasz tartalmazza a lista entitás összes a más entitásokkal előre jelzett, mert meg van határozva a kérés. 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>TimezoneOffset datetimeReference neve:

**A v2-ben**, a `timezoneOffset` [paraméter](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) küldése a előrejelzési kérelemnél, mint a lekérdezési sztring paramétereként, függetlenül attól, ha a kérelem egy GET vagy POST-kérelmet továbbítja a rendszer. 

**A v3-as**, ugyanazokat a funkciókat a POST törzsparaméter példáira `datetimeReference`. 

## <a name="marking-placement-of-entities-in-utterances"></a>Jelölés elhelyezése az entitások a kimondott szöveg

**A v2-ben**, entitás lett megjelölve egy az utterance (kifejezés) az a `startIndex` és `endIndex`. 

**A v3-as**, az entitás meg van jelölve `startIndex` és `entityLength`.

## <a name="deprecation"></a>Elavulás 

A V2 API nem a v3-as előzetes legalább 9 hónapig elavulttá válik. 

## <a name="next-steps"></a>További lépések

Használja a V3 API-dokumentáció frissítésére a meglévő REST-hívások LUIS [végpont](https://aka.ms/luis-api-v3) API-k. 