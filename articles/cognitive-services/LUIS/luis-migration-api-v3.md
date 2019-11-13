---
title: Az előrejelzési végpont módosításai a V3 API-ban
titleSuffix: Azure Cognitive Services
description: Megváltoztak a lekérdezés-előrejelzési végpont V3 API-jai. Ebből az útmutatóból megtudhatja, hogyan telepítheti át a 3. verziójú Endpoint API-kat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: bb2255a9a68a499ff3e77c1fbd35081a2474cf1d
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961935"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Előrejelzési végpont módosításai v3 esetén

Megváltoztak a lekérdezés-előrejelzési végpont V3 API-jai. Ebből az útmutatóból megtudhatja, hogyan telepítheti át a 3. verziójú Endpoint API-kat. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Általánosan elérhető állapot** – ez a V3 API jelentős JSON-kérést és válasz-változásokat tartalmaz a v2 API-ból.

A V3 API a következő új funkciókat biztosítja:

* [Külső entitások](#external-entities-passed-in-at-prediction-time)
* [Dinamikus felsorolások](#dynamic-lists-passed-in-at-prediction-time)
* [Előre elkészített entitás JSON-módosításai](#prebuilt-entity-changes)

Az előrejelzési végpontra vonatkozó [kérelem](#request-changes) és [Válasz](#response-changes) jelentős módosításokat tartalmaz a fent felsorolt új funkciók támogatásához, beleértve a következőket:

* [Válasz objektum változásai](#top-level-json-changes)
* [Entitás-szerepkör neve hivatkozás az entitás neve helyett](#entity-role-name-instead-of-entity-name)
* [Entitások megjelölésére szolgáló tulajdonságok a hosszúságú kimondott szöveg](#marking-placement-of-entities-in-utterances)

A [hivatkozás dokumentációja](https://aka.ms/luis-api-v3) a v3 verzióhoz érhető el.

## <a name="v3-changes-from-preview-to-ga"></a>V3 változások az előzetes verzióról a GA-ra

V3 a következő módosításokat hajtotta végre a GA-ra való áttérés részeként: 

* A következő előre összeállított entitások különböző JSON-válaszokat tartalmaznak: 
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * Mérhető egység kulcsának neve `units`ról `unit`

* Kérelem törzse JSON-változás:
    * `preferExternalEntities`ról `preferExternalEntities`re
    * opcionális `score` paraméter külső entitásokhoz

* A válasz törzsének JSON-módosításai:
    * `normalizedQuery` eltávolítva

## <a name="suggested-adoption-strategy"></a>Javasolt bevezetési stratégia

Ha bot Framework-t, Bing Spell Check v7-t vagy csak a LUIS-alkalmazás készítését szeretné áttelepíteni, folytassa a v2-végpont használatát. 

Ha tudja, hogy egyik ügyfélalkalmazás vagy integráció (bot Framework és Bing Spell Check v7) sem érintett, és a LUIS-alkalmazás létrehozása és az előrejelzési végpontja is kényelmesen áttelepíthető a v3 előrejelzési végpont használatával. A v2 előrejelzési végpont továbbra is elérhető lesz, és jó visszaesési stratégia. 

## <a name="not-supported"></a>Nem támogatott

* A Bing Spell Check API nem támogatott a v3 előrejelzési végpontban – továbbra is használhatja a v2 API-előrejelzési végpontot a helyesírási helyesbítésekhez.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>A bot Framework és Azure Bot Service ügyfélalkalmazások

Továbbra is használja a v2 API-előrejelzési végpontot, amíg megjelent a bot Framework V 4.7-es verziója. 

## <a name="v2-api-deprecation"></a>V2 API-elavult 

A v2 előrejelzési API-t a v3-es előzetes verziótól számítva legalább 9 hónapig nem lehet érvényteleníteni, 2020. június 8-án. 

## <a name="endpoint-url-changes"></a>Végpont URL-címének módosítása 

### <a name="changes-by-slot-name-and-version-name"></a>Változások a tárolóhely neve és a verzió neve alapján

A v3-végpont HTTP-hívásának formátuma megváltozott.

Ha verzió alapján szeretne lekérdezni, először közzé kell tennie az [API-n keresztül](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) a `"directVersionPublish":true`használatával. A tárolóhely neve helyett a verziószámra hivatkozó végpont lekérdezése.

|ELŐREJELZÉSI API VERZIÓJA|METÓDUS|URL-cím|
|--|--|--|
|V3|GET|https://<b>{region}</b>. API.Cognitive.microsoft.com/Luis/<b>előrejelzése</b>/<b>v 3.0</b>/apps/<b>{app-ID}</b>/Slots/<b>{slot-Name}</b>/Predict? lekérdezés =<b>{query}</b>|
|V3|POST|https://<b>{region}</b>. API.Cognitive.microsoft.com/Luis/<b>előrejelzése</b>/<b>v 3.0</b>/apps/<b>{app-ID}</b>/Slots/<b>{slot-Name}</b>/Predict|
|2\. verzió|GET|https://<b>{region}</b>. API.Cognitive.microsoft.com/Luis/<b>előrejelzése</b>/<b>v 3.0</b>/apps/<b>{app-ID}</b>/Versions/<b>{Version-ID}</b>/Predict? Query =<b>{query}</b>|
|2\. verzió|POST|https://<b>{region}</b>. API.Cognitive.microsoft.com/Luis/<b>Előrejelzés</b><b>v 3.0</b>/apps/<b>{app-ID}</b>/Versions/<b>{Version-ID}</b>/Predict|

|`SLOT-NAME` érvényes értékei|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Módosítási kérelmek 

### <a name="query-string-changes"></a>Lekérdezési karakterlánc módosításai

A V3 API különböző lekérdezési karakterlánc-paraméterekkel rendelkezik.

|Params neve|Típus|Verzió|Alapértelmezett|Cél|
|--|--|--|--|--|
|`log`|logikai|V2 & v3|false|A lekérdezés tárolása a naplófájlban. Az alapértelmezett érték false (hamis).| 
|`query`|sztring|Csak v3|Nincs alapértelmezett – a GET kérelemben szükséges|A **v2-ben**a megjósolható teljes érték a `q` paraméterben található. <br><br>A **v3**-as verzióban a funkció a `query` paraméterben lesz átadva.|
|`show-all-intents`|logikai|Csak v3|false|Az összes leképezés visszaküldése a megfelelő pontszámmal a **jóslat. szándékok** objektumban. A rendszer egy szülő `intents` objektumban lévő objektumokként adja vissza a leképezéseket. Ez lehetővé teszi a programozott hozzáférést anélkül, hogy meg kellene találni a szándékot egy tömbben: `prediction.intents.give`. A v2-ben ezek egy tömbben voltak visszaadva. |
|`verbose`|logikai|V2 & v3|false|Ha **a v2**értéke TRUE (igaz) értékre van állítva, az összes előre jelzett leképezést visszaadja. Ha minden előre jelzett leképezésre van szüksége, használja a `show-all-intents`v3 paraméterét.<br><br>**A v3-** as verzióban ez a paraméter csak az entitások előrejelzését biztosító entitás-metaadatokat tartalmazza.  |
|`timezoneOffset`|sztring|2\. verzió|-|A datetimeV2 entitásokra alkalmazott időzóna.|
|`datetimeReference`|sztring|V3|-|A datetimeV2 entitásokra alkalmazott [időzóna](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) . Lecseréli a `timezoneOffset`t a v2-ből.|


### <a name="v3-post-body"></a>V3 POST törzs

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Tulajdonság|Típus|Verzió|Alapértelmezett|Cél|
|--|--|--|--|--|
|`dynamicLists`|tömb|Csak v3|Nem kötelező.|A [dinamikus listák](#dynamic-lists-passed-in-at-prediction-time) segítségével kiterjesztheti a már meglévő betanított és közzétett lista entitást, amely már a Luis-alkalmazásban van.|
|`externalEntities`|tömb|Csak v3|Nem kötelező.|A [külső entitások](#external-entities-passed-in-at-prediction-time) lehetővé teszi, hogy a Luis-alkalmazás képes legyen az entitások azonosítására és címkézésére a futtatókörnyezet során, amely funkciókként használható a meglévő entitásokhoz. |
|`options.datetimeReference`|sztring|Csak v3|Nincs alapértelmezett érték|A [datetimeV2 eltolásának](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)meghatározására szolgál. A datetimeReference formátuma [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|logikai|Csak v3|false|Meghatározza, hogy a felhasználó [külső entitása (a meglévő entitás nevével megegyező névvel)](#override-existing-model-predictions) van-e használva, vagy a modellben lévő meglévő entitást használja-e a rendszer az előrejelzéshez. |
|`query`|sztring|Csak v3|Kötelező.|A **v2-ben**a megjósolható teljes érték a `q` paraméterben található. <br><br>A **v3**-as verzióban a funkció a `query` paraméterben lesz átadva.|



## <a name="response-changes"></a>Válasz módosításai

A lekérdezési válasz JSON-je úgy módosult, hogy a leggyakrabban használt adattartalomhoz való nagyobb programozási hozzáférést engedélyezzen. 

### <a name="top-level-json-changes"></a>Legfelső szintű JSON-változások



A v2 leggyakoribb JSON-tulajdonságai, ha a `verbose` True értékre van állítva, amely visszaadja az összes leképezést és azok pontszámait a `intents` tulajdonságban:

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
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

A `intents` objektum egy Rendezetlen lista. Ne tegyük fel, hogy a `intents` első gyermeke megfelel a `topIntent`nak. Ehelyett használja a `topIntent` értéket a pontszám megkereséséhez:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

A válasz JSON-sémájának módosítása az alábbiakat teszi lehetővé:

* Törölje az eredeti Kimondás, a `query`és a visszaadott előrejelzés (`prediction`) közötti különbséget.
* Egyszerűbb programozott hozzáférés az előre jelzett adatértékekhez. A v2-ben a tömbön keresztüli számbavétel helyett a szándékok és az entitások **nevével** is elérheti az értékeket. Az előre jelzett entitási szerepkörök esetében a rendszer a szerepkör nevét adja vissza, mert az a teljes alkalmazásban egyedi.
* Az adattípusok, ha meg vannak határozva, figyelembe veszik azokat. A numerikus értékeket a rendszer már nem adja vissza karakterláncként.
* Az első prioritású előrejelzési információk és a további metaadatok közötti különbségtétel a `$instance` objektumban visszaadott érték. 

### <a name="entity-response-changes"></a>Entitások válaszának módosításai

#### <a name="marking-placement-of-entities-in-utterances"></a>Entitások elhelyezésének megjelölése a hosszúságú kimondott szöveg-ben

**A v2-ben**egy entitást a `startIndex` és `endIndex`megjelöléssel jelölt ki. 

A **v3-** as verzióban az entitás `startIndex` és `entityLength`jelöléssel van megjelölve.

#### <a name="access-instance-for-entity-metadata"></a>Az entitás metaadatainak hozzáférési `$instance`

Ha entitás-metaadatokra van szüksége, a lekérdezési karakterláncnak a `verbose=true` jelzőt kell használnia, és a válasz tartalmazza a metaadatokat a `$instance` objektumban. A következő részben a JSON-válaszokban látható példák.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Minden előre jelzett entitás tömbként jelenik meg

A `prediction.entities.<entity-name>` objektum egy tömböt tartalmaz, mert az egyes entitások többször is megtekinthetők a teljes kiosztásban. 

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Előre összeépített entitások módosításai

A v3 válasz objektum az előre elkészített entitások módosításait tartalmazza. Tekintse át az [adott előre összeépített entitásokat](luis-reference-prebuilt-entities.md) további információért. 

#### <a name="list-entity-prediction-changes"></a>Entitások előrejelzési változásainak listázása

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

A `entities` objektum és a `$instance` objektum közötti leképezéskor a rendszer megőrzi az objektumok sorrendjét az entitások listázása során.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Entitás-szerepkör neve az entitás neve helyett 

A v2-ben a `entities` tömb az összes előre jelzett entitást visszaadotta, az entitás neve pedig az egyedi azonosító. Ha a v3-as verzióban az entitás szerepköröket használ, és az előrejelzés egy entitási szerepkörhöz tartozik, az elsődleges azonosító a szerepkör neve. Ez azért lehetséges, mert az entitás-szerepkörök nevének egyedinek kell lennie a teljes alkalmazásban, beleértve a más modelleket (szándékok, entitások) nevét.

A következő példában érdemes megfontolni, hogy a szöveget tartalmazó szöveg, `Yellow Bird Lane`. Ezt a szöveget a rendszer az egyéni `Location` entitás `Destination`-szerepkörének megfelelően Jósolja meg.

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

A v3-as verzióban Ugyanez az eredmény szerepel a `verbose` jelzővel az entitás metaadatainak visszaadásához:

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

A külső entitások az entitások típusának kibővítésének mechanizmusa, miközben más modellekhez, például szerepkörökhöz, kompozithoz és másokhoz is jeleket használnak.

Ez olyan entitások esetében hasznos, amelyek csak a lekérdezés-előrejelzési futtatókörnyezetben érhetők el. Ilyen típusú adattípusok például folyamatosan változnak az adathalmazok vagy felhasználónként. A LUIS Contact entitást külső információkkal bővítheti egy felhasználó névjegyalbuma alapján. 

### <a name="entity-already-exists-in-app"></a>Az entitás már létezik az alkalmazásban

A külső entitás `entityName` értéke, amelyet a rendszer a végponti kérelem POST törzsében továbbított, már léteznie kell a betanított és közzétett alkalmazásban a kérelem elküldésekor. Az entitás típusa nem számít, az összes típus támogatott.

### <a name="first-turn-in-conversation"></a>A beszélgetés első bekapcsolása

Vegye fontolóra egy csevegési bot-beszélgetés első kitöltését, ahol a felhasználó a következő hiányos adatokat írja be:

`Send Hazem a new message`

A csevegési robottól a LUIS-re irányuló kérés a POST törzsében a `Hazem`, így közvetlenül az egyik felhasználó kapcsolattartójának felel meg.

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

Az előző Kimondás során a teljes `him` a `Hazem`ra mutató hivatkozásként használja. A POST szövegtörzsben a társalgási csevegési robot leképezheti az `him` az első lemondás után kinyert entitás értékére `Hazem`.

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

A `preferExternalEntities` beállítások tulajdonság azt adja meg, hogy ha a felhasználó olyan külső entitást küld, amely átfedésben van ugyanazzal a névvel, akkor LUIS kiválasztja az átadott entitást vagy a modellben meglévő entitást. 

Vegyük például a `today I'm free`lekérdezést. A LUIS észleli `today` datetimeV2 a következő választal:

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

Ha a `preferExternalEntities` `false`re van beállítva, a LUIS egy választ ad vissza, mintha a külső entitás nem lett elküldve. 

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

Ha a `preferExternalEntities` `true`re van beállítva, a LUIS egy választ ad vissza, amely a következőket tartalmazza:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Megoldás:

A nem _kötelező_ `resolution` tulajdonság visszaadja az előrejelzési választ, így átadhatja a külső entitáshoz társított metaadatokat, majd visszaküldheti azt a válaszban. 

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

A következő JSON-törzsbe való küldéssel adjon hozzá egy új, szinonimákkal ellátott allistát a listához, és Tippelje meg, hogy a lista entitása szerepel-e a szövegben, `LUIS``POST` lekérdezés-előrejelzési kérelemmel:

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

## <a name="deprecation"></a>Elavulás 

A v2 API-t a v3 előzetes verzió után legalább 9 hónapig nem lehet érvényteleníteni. 

## <a name="next-steps"></a>Következő lépések

A V3 API dokumentációjának használatával frissítheti a meglévő REST-hívásokat a LUIS [Endpoint](https://aka.ms/luis-api-v3) API-kra. 
