---
title: V2 – V3 API migrálása
titleSuffix: Azure Cognitive Services
description: A 3-as verziójú Endpoint API-k megváltoztak. Ebből az útmutatóból megtudhatja, hogyan telepítheti át a 3. verziójú Endpoint API-kat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: diberry
ms.openlocfilehash: 5b0516f3d610c0a518d6afc461dddebfb68a7c5d
ms.sourcegitcommit: ac29357a47cc05afdf0f84834de5277598f4d87c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70213515"
---
# <a name="preview-migrate-to-api-version-3x-for-luis-apps"></a>Előzetes verzió: Migrálás a 3. x verziójú API-ra a LUIS-alkalmazásokhoz

A lekérdezés-előrejelzési végpont API-jai módosultak. Ebből az útmutatóból megtudhatja, hogyan telepítheti át a 3. verziójú Endpoint API-kat. 

Ez a V3 API a következő új funkciókat biztosítja, amelyek közé tartoznak a JSON-kérelmek jelentős része és/vagy a válasz módosítása: 

* [Külső entitások](#external-entities-passed-in-at-prediction-time)
* [Dinamikus felsorolások](#dynamic-lists-passed-in-at-prediction-time)
* [Előre elkészített entitás JSON-módosításai](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

A lekérdezés-előrejelzési végpontra vonatkozó [kérelem](#request-changes) és [Válasz](#response-changes) jelentős módosításokat tartalmaz a fent felsorolt új funkciók támogatásához, beleértve a következőket:

* [Válasz objektum változásai](#top-level-json-changes)
* [Entitás-szerepkör neve hivatkozás az entitás neve helyett](#entity-role-name-instead-of-entity-name)
* [Entitások megjelölésére szolgáló tulajdonságok a hosszúságú kimondott szöveg](#marking-placement-of-entities-in-utterances)

A következő LUIS-funkciók **nem támogatottak** a V3 API-ban:

* Bing Spell Check v7

A [hivatkozás dokumentációja](https://aka.ms/luis-api-v3) a v3 verzióhoz érhető el.

## <a name="endpoint-url-changes-by-slot-name"></a>Végpont URL-címének módosítása a tárolóhely neve alapján

A v3-végpont HTTP-hívásának formátuma megváltozott.

|METÓDUS|URL|
|--|--|
|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict?query=<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|||

A tárolóhelyek érvényes értékei:

* `production`
* `staging`

## <a name="endpoint-url-changes-by-version-id"></a>A végpont URL-címének módosítása a verzióazonosító alapján

Ha verzió alapján szeretne lekérdezni, először közzé kell tennie az [API-n keresztül](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) a `"directVersionPublish":true`következővel:. A tárolóhely neve helyett a verziószámra hivatkozó végpont lekérdezése.


|METÓDUS|URL|
|--|--|
|GET|https://<b>{region}</b>. API.Cognitive.microsoft.com/Luis/<b>v 3.0 – előzetes</b>verziójú/apps/<b>{app-ID}</b>/Versions/<b>{Version-ID}</b>/Predict? Query =<b>{query}</b>|
|POST|https://<b>{region}</b>. API.Cognitive.microsoft.com/Luis/<b>v 3.0 – előzetes</b>verziójú/apps/<b>{app-ID}</b>/Versions/<b>{Version-ID}</b>/Predict|
|||

## <a name="prebuilt-entities-with-new-json"></a>Előre elkészített entitások új JSON-vel

A v3 válasz objektum változása [előre](luis-reference-prebuilt-entities.md)összeépített entitásokat tartalmaz. 

## <a name="request-changes"></a>Módosítási kérelmek 

### <a name="query-string-parameters"></a>Lekérdezési karakterlánc paraméterei

A V3 API különböző lekérdezési karakterlánc-paraméterekkel rendelkezik.

|Params neve|Type|Version|Alapértelmezett|Cél|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|A lekérdezés tárolása a naplófájlban.| 
|`query`|Karakterlánc|Csak v3|Nincs alapértelmezett – a GET kérelemben szükséges|A **v2-ben**az előre jelzett `q` érték a paraméterben szerepel. <br><br>A **v3**-as verzióban a funkció a `query` paraméterben lesz átadva.|
|`show-all-intents`|boolean|Csak v3|false|Az összes leképezés visszaküldése a megfelelő pontszámmal a **jóslat. szándékok** objektumban. A rendszer a fölérendelt `intents` objektumban lévő objektumokként adja vissza a leképezéseket. Ez lehetővé teszi a programozott hozzáférést anélkül, hogy meg kellene találni a szándékot egy `prediction.intents.give`tömbben:. A v2-ben ezek egy tömbben voltak visszaadva. |
|`verbose`|boolean|V2 & V3|false|Ha **a v2**értéke TRUE (igaz) értékre van állítva, az összes előre jelzett leképezést visszaadja. Ha minden előre jelzett leképezésre van szüksége, használja a v3 paraméterét `show-all-intents`.<br><br>**A v3-** as verzióban ez a paraméter csak az entitások előrejelzését biztosító entitás-metaadatokat tartalmazza.  |



<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>A `POST` kérelemhez tartozó lekérdezés-előrejelzés JSON-törzse

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

|Tulajdonság|Type|Version|Alapértelmezett|Cél|
|--|--|--|--|--|
|`dynamicLists`|array|Csak v3|Nem kötelező.|A [dinamikus listák](#dynamic-lists-passed-in-at-prediction-time) segítségével kiterjesztheti a már meglévő betanított és közzétett lista entitást, amely már a Luis-alkalmazásban van.|
|`externalEntities`|array|Csak v3|Nem kötelező.|A [külső entitások](#external-entities-passed-in-at-prediction-time) lehetővé teszi, hogy a Luis-alkalmazás képes legyen az entitások azonosítására és címkézésére a futtatókörnyezet során, amely funkciókként használható a meglévő entitásokhoz. |
|`options.datetimeReference`|Karakterlánc|Csak v3|Nincs alapértelmezett érték|A [datetimeV2 eltolásának](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)meghatározására szolgál. A datetimeReference formátuma [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.overridePredictions`|boolean|Csak v3|false|Meghatározza, hogy a felhasználó [külső entitása (a meglévő entitás nevével megegyező névvel)](#override-existing-model-predictions) van-e használva, vagy a modellben lévő meglévő entitást használja-e a rendszer az előrejelzéshez. |
|`query`|Karakterlánc|Csak v3|Kötelező.|A **v2-ben**az előre jelzett `q` érték a paraméterben szerepel. <br><br>A **v3**-as verzióban a funkció a `query` paraméterben lesz átadva.|



## <a name="response-changes"></a>Válasz módosításai

A lekérdezési válasz JSON-je úgy módosult, hogy a leggyakrabban használt adattartalomhoz való nagyobb programozási hozzáférést engedélyezzen. 

### <a name="top-level-json-changes"></a>Legfelső szintű JSON-változások

A v2 leggyakoribb JSON- `verbose` tulajdonságai az igaz értékre vannak állítva, amely visszaadja az összes leképezést és azok pontszámait a `intents` tulajdonságban:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

A v3 leggyakoribb JSON-tulajdonságai a következők:

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

<!--
The `alteredQuery` contains spelling corrections. This corresponds to the V2 API property `alteredQuery`.  
-->

Az `intents` objektum egy Rendezetlen lista. Ne tegyük fel, hogy az első `intents` gyermek a megfelel `topIntent`a következőnek:. Ehelyett használja az `topIntent` értéket a pontszám megkereséséhez:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

A válasz JSON-sémájának módosítása az alábbiakat teszi lehetővé:

* Törölje az eredeti Kimondás `query`és a visszaadott `prediction`előrejelzés közötti különbséget.
* Egyszerűbb programozott hozzáférés az előre jelzett adatértékekhez. A v2-ben a tömbön keresztüli számbavétel helyett a szándékok és az entitások nevével is elérheti az értékeket. Az előre jelzett entitási szerepkörök esetében a rendszer a szerepkör nevét adja vissza, mert az a teljes alkalmazásban egyedi.
* Az adattípusok, ha meg vannak határozva, figyelembe veszik azokat. A numerikus értékeket a rendszer már nem adja vissza karakterláncként.
* Az első prioritású előrejelzési információk és az `$instance` objektumban visszaadott további metaadatok megkülönböztetése. 

### <a name="access-instance-for-entity-metadata"></a>Hozzáférés `$instance` az entitás metaadatainak számára

Ha entitás-metaadatokra van szüksége, a lekérdezési karakterláncnak `verbose=true` a jelzőt kell használnia, és a `$instance` válasz tartalmazza a metaadatokat az objektumban. A következő részben a JSON-válaszokban látható példák.

### <a name="each-predicted-entity-is-represented-as-an-array"></a>Minden előre jelzett entitás tömbként jelenik meg

Az `prediction.entities.<entity-name>` objektum egy tömböt tartalmaz, mert az egyes entitások többször is megtekinthetők a teljes kiosztásban. 

### <a name="list-entity-prediction-changes"></a>Entitások előrejelzési változásainak listázása

A lista entitások előrejelzéséhez tartozó JSON a tömbök tömbje lett:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Az egyes belső tömbök a teljes szövegen belüli szövegnek felelnek meg. A belső objektum egy tömb, mert ugyanaz a szöveg a lista entitásának több allistájában is megjelenhet. 

Az objektum és az `entities` `$instance` objektum közötti leképezéskor a rendszer megőrzi az objektumok sorrendjét az entitások listázása során.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>Entitás-szerepkör neve az entitás neve helyett 

A v2-ben `entities` a tömb az összes előre jelzett entitást visszaadotta, az entitás neve pedig az egyedi azonosító. Ha a v3-as verzióban az entitás szerepköröket használ, és az előrejelzés egy entitási szerepkörhöz tartozik, az elsődleges azonosító a szerepkör neve. Ez azért lehetséges, mert az entitás-szerepkörök nevének egyedinek kell lennie a teljes alkalmazásban, beleértve a más modelleket (szándékok, entitások) nevét.

A következő példában a szöveget `Yellow Bird Lane`tartalmazó kifejezésnek kell megfontolnia:. Ezt a szöveget a rendszer az egyéni `Location` entitás szerepköre alapján `Destination`Jósolja meg.

|Teljes szöveg|Entitás neve|Szerepkörnév|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

A v2-ben az entitás neve azonosítja az entitás _nevét_ az objektum tulajdonságának megfelelően:

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

A v3-as verzióban az entitásra az _entitás szerepkör_hivatkozik, ha az előrejelzés a következő szerepkörre vonatkozik:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

A v3-as verzióban Ugyanez az `verbose` eredmény szerepel az entitás metaadatainak visszaadására szolgáló jelzővel:

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

## <a name="external-entities-passed-in-at-prediction-time"></a>Az előrejelzési időpontban átadott külső entitások

A külső entitások lehetővé teszi, hogy a LUIS-alkalmazás képes legyen az entitások azonosítására és címkézésére a futtatókörnyezet során, amely funkciókként használható a meglévő entitásokhoz. Ez lehetővé teszi a saját különálló és egyéni entitás-kinyerő használatát, mielőtt lekérdezést küld az előrejelzési végpontnak. Mivel ez a lekérdezés-előrejelzési végponton történik, nincs szükség a modell újratanítására és közzétételére.

Az ügyfélalkalmazás saját entitást biztosít az entitások megfeleltetésével, és meghatározza az egyező entitások kiválasztásának helyét, majd elküldi ezeket az adatokat a kérelemmel. 

A külső entitások az entitások típusának kibővítésének mechanizmusa, miközben más modellek, például a szerepkörök, az összetett és mások számára is jelekként használatosak.

Ez olyan entitások esetében hasznos, amelyek csak a lekérdezés-előrejelzési futtatókörnyezetben érhetők el. Ilyen típusú adattípusok például folyamatosan változnak az adathalmazok vagy felhasználónként. A LUIS Contact entitást külső információkkal bővítheti egy felhasználó névjegyalbuma alapján. 

### <a name="entity-already-exists-in-app"></a>Az entitás már létezik az alkalmazásban

A kérelem elküldésekor a betanított és a közzétett alkalmazásban már léteznie kell a külső entitás értéke `entityName` , amelyet a rendszer a végponti kérelem post törzsében átadott. Az entitás típusa nem számít, az összes típus támogatott.

### <a name="first-turn-in-conversation"></a>A beszélgetés első bekapcsolása

Vegye fontolóra egy csevegési bot-beszélgetés első kitöltését, ahol a felhasználó a következő hiányos adatokat írja be:

`Send Hazem a new message`

A csevegési robottól a Luis-re irányuló kérés a post törzsben `Hazem` található információkkal is átadható, így közvetlenül a felhasználó névjegyeinek felel meg.

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

Az előrejelzési válasz tartalmazza azt a külső entitást, amely az összes többi előre jelzett entitáshoz tartozik, mert a kérelemben van definiálva.  

### <a name="second-turn-in-conversation"></a>Második bekapcsolási beszélgetés

A csevegési robot következő felhasználójának kimondása homályosan működik:

`Send him a calendar reminder for the party.`

Az előző Kimondás során a Kimondás `him` a következőre `Hazem`hivatkozik:. A post szövegtörzsben lévő beszélgetési csevegési robot a kinyert `him` `Hazem`entitás értékére képezhető le.

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

Az előrejelzési válasz tartalmazza azt a külső entitást, amely az összes többi előre jelzett entitáshoz tartozik, mert a kérelemben van definiálva.  

### <a name="override-existing-model-predictions"></a>Meglévő modell-előrejelzések felülbírálása

A `overridePredictions` beállítások tulajdonság azt adja meg, hogy ha a felhasználó olyan külső entitást küld, amely átfedésben van egy azonos nevű előre jelzett entitással, a Luis kiválasztja az átadott entitást vagy a modellben meglévő entitást. 

Vegyük például a lekérdezést `today I'm free`. A Luis `today` a következő választ datetimeV2 észleli:

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

Ha a felhasználó a külső entitást küldi el:

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

Ha a `overridePredictions` értéke `false`, a Luis egy választ ad vissza, mintha a külső entitás nem lett elküldve. 

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

Ha a `overridePredictions` értéke `true`, a Luis egy választ ad vissza, amely a következőket tartalmazza:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Megoldás:

A _választható_ `resolution` tulajdonság visszaadja az előrejelzési választ, amely lehetővé teszi a külső entitáshoz társított metaadatok átadását, majd visszaküldi a válaszban. 

Az elsődleges cél az előre elkészített entitások kiterjesztése, de nem korlátozódik az adott entitás típusára. 

A `resolution` tulajdonság lehet szám, karakterlánc, objektum vagy tömb:

* Dallas
* {"text": "value"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Az előrejelzési időpontban átadott dinamikus felsorolások

A dinamikus listák segítségével kiterjesztheti a már meglévő betanított és közzétett lista entitást, amely már a LUIS-alkalmazásban van. 

Akkor használja ezt a funkciót, ha a lista entitás értékeit időnként módosítani kell. Ez a funkció lehetővé teszi egy már betanított és közzétett lista entitás kiterjesztését:

* A lekérdezés-előrejelzési végpont kérelmének időpontjában.
* Egyetlen kérelem esetén.

A List entitás lehet üres a LUIS-alkalmazásban, de léteznie kell. A LUIS-alkalmazás listázási entitása nem változik, de a végponton megjelenő előrejelzési képesség kiterjeszthető úgy, hogy legfeljebb 2 listát tartalmazzon a 1 000 elemekkel.

### <a name="dynamic-list-json-request-body"></a>Dinamikus lista JSON-kérelmének törzse

A következő JSON-törzsbe való küldéssel adjon hozzá egy új, szinonimákkal ellátott allistát a listához, és Tippelje meg a `LUIS`szöveg lista entitását, a `POST` lekérdezés-előrejelzési kérelemmel együtt:

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

Az előrejelzési válasz tartalmazza a List entitást az összes többi előre jelzett entitással együtt, mert a kérelemben van definiálva. 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>TimezoneOffset átnevezve datetimeReference

A **v2**-ben `timezoneOffset` a [paramétert](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) lekérdezési karakterlánc paraméterként küldi el a rendszer az előrejelzési kérelemben, függetlenül attól, hogy a kérést Get vagy post kérelemként küldi el a rendszer. 

A **v3**-as verzióban ugyanaz a funkció szerepel a post Body paraméterben `datetimeReference`. 

## <a name="marking-placement-of-entities-in-utterances"></a>Entitások elhelyezésének megjelölése a hosszúságú kimondott szöveg-ben

**A v2-ben**egy entitást jelölt ki a és `startIndex` `endIndex`a kifejezéssel. 

A **v3**-as verzióban az entitás `startIndex` a `entityLength`és a jelöléssel van megjelölve.

## <a name="deprecation"></a>Elavulás 

A v2 API-t a v3 előzetes verzió után legalább 9 hónapig nem lehet érvényteleníteni. 

## <a name="next-steps"></a>További lépések

A V3 API dokumentációjának használatával frissítheti a meglévő REST-hívásokat a LUIS [Endpoint](https://aka.ms/luis-api-v3) API-kra. 
