---
title: Előrejelzési végpont változások a V3 API-ban
description: A lekérdezés előrejelzési végpont V3 API-k megváltoztak. Ez az útmutató a 3-as verziójú végpontAPI-kra való áttelepítése.
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 9a8e8cb331dd11eebaddbcbf8f603c1148415aef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117382"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Előrejelzési végpont változások a V3

A lekérdezés előrejelzési végpont V3 API-k megváltoztak. Ez az útmutató a 3-as verziójú végpontAPI-kra való áttelepítése.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Általánosan elérhető állapot** – ez a V3 API jelentős JSON-kérelem- és válaszmódosításokat tartalmaz a V2 API-ból.

A V3 API a következő új funkciókat biztosítja:

* [Külső entitások](#external-entities-passed-in-at-prediction-time)
* [Dinamikus listák](#dynamic-lists-passed-in-at-prediction-time)
* [Előre összeállított entitás JSON-módosítások](#prebuilt-entity-changes)

Az előrejelzési [végpont-kérelem](#request-changes) és [-válasz](#response-changes) jelentős változtatásokat tartalmaz a fent felsorolt új funkciók támogatásához, beleértve a következőket:

* [Válaszobjektum-módosítások](#top-level-json-changes)
* [Entitásszerepkör-névhivatkozás az entitásnév helyett](#entity-role-name-instead-of-entity-name)
* [Entitások megjelölésére irányuló tulajdonságok a kimondott szövegekben](#marking-placement-of-entities-in-utterances)

A V3-hoz [referenciadokumentáció](https://aka.ms/luis-api-v3) áll rendelkezésre.

## <a name="v3-changes-from-preview-to-ga"></a>A V3 előnézetről GA-ra változik

V3 a következő módosításokat hajtotta végre a GA-ra való áttérés részeként:

* A következő előre összeállított entitások különböző JSON-válaszokkal rendelkeznek:
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [FöldrajzV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2 között](luis-reference-prebuilt-datetimev2.md)
    * Mérhető egységkulcs neve `units``unit`

* Kérelem törzs JSON változás:
    * innen `preferExternalEntities``preferExternalEntities`
    * választható `score` paraméter külső entitásokhoz

* Válasz szervezet JSON változások:
    * `normalizedQuery`Eltávolított

## <a name="suggested-adoption-strategy"></a>Javasolt elfogadási stratégia

Ha a Bot Framework, Bing Helyesírás-ellenőrzés V7, vagy szeretné áttelepíteni a LUIS-alkalmazás szerzői csak, továbbra is használja a V2-végpont.

Ha tudja, hogy az ügyfélalkalmazás vagy integrációk (Bot Framework, és a Bing Helyesírás-ellenőrzés V7) nem érinti, és kényelmesen migrálja a LUIS-alkalmazás szerzői és az előrejelzési végpont egy időben, kezdje meg a V3 előrejelzési végpont. A V2 előrejelzési végpont továbbra is elérhető lesz, és egy jó tartalék stratégia.


## <a name="not-supported"></a>Nem támogatott

### <a name="bing-spell-check"></a>Bing – Helyesírás-ellenőrzés

Ez az API nem támogatott a V3 előrejelzési végpont – továbbra is használja a V2 API előrejelzési végpont helyesírási javítások. Ha a V3 API használata közben helyesírás-javításra van szüksége, az ügyfélalkalmazás hívja meg a [Bing helyesírás-ellenőrző](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview) API-t, és módosítsa a szöveget a megfelelő helyesírásra, mielőtt elküldi a szöveget a LUIS API-nak.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Bot Framework és Az Azure Bot Service ügyfélalkalmazások

Továbbra is használja a V2 API előrejelzési végpont, amíg a Robot keretrendszer V4.7-es kiadása.

## <a name="v2-api-deprecation"></a>V2 API-esekessedés

A V2 előrejelzési API nem lesz elavult legalább 9 hónappal a V3 előzetes verzió után, június 8, 2020.

## <a name="endpoint-url-changes"></a>Végpont URL-jének módosítása

### <a name="changes-by-slot-name-and-version-name"></a>Változások a tárolóhely neve és a verziónév szerint

A V3-végpont HTTP-hívásának formátuma megváltozott.

Ha verzió szerint szeretne lekérdezni, először közzé kell [tennie az API-n keresztül](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) a segítségével. `"directVersionPublish":true` A tárolóhely neve helyett a verzióazonosítóra hivatkozó végpont lekérdezése.

|ELŐREJELZÉSI API-VERZIÓ|Módszer|URL-cím|
|--|--|--|
|V3|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>előrejelzés</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict?query=<b>{QUERY}</b>|
|V3|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>előrejelzés</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|2. verzió|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>előrejelzés</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict?query=<b>{QUERY}</b>|
|2. verzió|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>előrejelzés</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict|

|Érvényes értékek`SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Módosítási kérelmek

### <a name="query-string-changes"></a>Lekérdezési karakterlánc módosításai

A V3 API különböző lekérdezési karakterlánc-paramétereket rendelkezik.

|Param név|Típus|Verzió|Alapértelmezett|Cél|
|--|--|--|--|--|
|`log`|logikai|V2 & V3|hamis|A lekérdezést naplófájlban tárolja. Az alapértelmezett érték hamis.|
|`query`|sztring|Csak V3|Nincs alapértelmezett - ez szükséges a GET kérelemben|**A V2-ben**az előre jelezendő `q` utterance (kifejezés) a paraméterben van. <br><br>**A V3-ban**a funkció `query` átkerül a paraméterbe.|
|`show-all-intents`|logikai|Csak V3|hamis|Adja vissza az összes leképezést a megfelelő pontszámmal az **prediction.intents** objektumban. A leképezések egy `intents` szülőobjektum objektumaként kerülnek visszaadásra. Ez lehetővé teszi a programozott hozzáférést anélkül, `prediction.intents.give`hogy meg kellene találnia a szándékot egy tömbben: . A V2-ben ezeket egy tömbben adták vissza. |
|`verbose`|logikai|V2 & V3|hamis|**A V2**-ben, ha értéke igaz, az összes előre jelzett szándékok visszaadott. Ha az összes előre jelzett leképezésre van `show-all-intents`szüksége, használja a V3 param-ot.<br><br>**A V3-ban**ez a paraméter csak entitás-metaadatokat ad meg az entitás-előrejelzésről.  |
|`timezoneOffset`|sztring|2. verzió|-|A datetimeV2 entitásokra alkalmazott időzóna.|
|`datetimeReference`|sztring|V3|-|A datetimeV2 entitásokra alkalmazott [időzóna.](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) Helyettesíti `timezoneOffset` a V2.|


### <a name="v3-post-body"></a>V3 POST szerv

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
|`dynamicLists`|tömb|Csak V3|Nem szükséges.|[A dinamikus listák](#dynamic-lists-passed-in-at-prediction-time) lehetővé teszik egy meglévő betanított és közzétett listaentitás kiterjesztését, már a LUIS alkalmazásban.|
|`externalEntities`|tömb|Csak V3|Nem szükséges.|[A külső entitások](#external-entities-passed-in-at-prediction-time) lehetővé teszik a LUIS-alkalmazás számára az entitások azonosítását és címkézését futásidőben, amelyek meglévő entitások szolgáltatásaként használhatók. |
|`options.datetimeReference`|sztring|Csak V3|Nincs alapértelmezett|A [datetimeV2 eltolásának meghatározására](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)szolgál. A datetimeReference formátuma [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|logikai|Csak V3|hamis|Itt adható meg, hogy a felhasználó [külső entitását (a meglévő entitás nevével megegyező névvel)](#override-existing-model-predictions) használja-e a rendszer, vagy a modellben lévő meglévő entitást használja az előrejelzéshez. |
|`query`|sztring|Csak V3|Kötelező.|**A V2-ben**az előre jelezendő `q` utterance (kifejezés) a paraméterben van. <br><br>**A V3-ban**a funkció `query` átkerül a paraméterbe.|



## <a name="response-changes"></a>Válaszváltozások

A json lekérdezési válasz úgy módosult, hogy a leggyakrabban használt adatokhoz nagyobb programozott hozzáférést biztosítson.

### <a name="top-level-json-changes"></a>Felső szintű JSON változások



A V2 legnépszerűbb JSON-tulajdonsága igaz értékre `verbose` van állítva, amely `intents` az összes szándékot és a tulajdonságban lévő pontszámokat adja vissza:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

A V3 legnépszerűbb JSON-tulajdonságai a következők:

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

Az `intents` objektum rendezetlen lista. Ne feltételezze, hogy `intents` az első `topIntent`gyermek megfelel a . Ehelyett használja `topIntent` az értéket a pontszám megkereséséhez:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

A json-séma változásai a következőket teszik lehetővé:

* Egyértelmű különbség az eredeti `query`utterance (kifejezés) és a visszaadott előrejelzés között. `prediction`
* Könnyebb programozott hozzáférés az előre jelzett adatokhoz. A V2-ben egy tömbön keresztül történő enumerálás helyett **név** szerint érheti el az értékeket mind a szándékok, mind az entitások számára. Az előre jelzett entitásszerepkörök esetében a szerepkör nevét adja vissza, mert az a teljes alkalmazásban egyedi.
* Az adattípusok, ha meghatározva vannak, betartják őket. A numerikus szövegeket már nem adja vissza karakterláncként.
* Az elsődleges prioritás-előrejelzési információk és az `$instance` objektumban visszaadott további metaadatok közötti különbségtétel.

### <a name="entity-response-changes"></a>Entitásválasz változásai

#### <a name="marking-placement-of-entities-in-utterances"></a>Entitások elhelyezésének megjelölése a kimondott szövegekben

**A V2-ben**egy entitás t a `startIndex` `endIndex`és a kimondásban jelölt meg egy.

**A V3-ban**az `startIndex` entitás `entityLength`a és a.

#### <a name="access-instance-for-entity-metadata"></a>Entitásmetaadatok hoz való hozzáférés `$instance`

Ha entitás metaadatokra van szüksége, a `verbose=true` lekérdezési karakterláncnak a jelzőt `$instance` kell használnia, és a válasz tartalmazza az objektum metaadatait. Példák jelennek meg a JSON válaszok a következő szakaszokban.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Minden előre jelzett entitás tömbként jelenik meg

Az `prediction.entities.<entity-name>` objektum tartalmaz egy tömböt, mert minden entitás lehet előre jelezni egynél többször az utterance (kifejezés).

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Előre összeállított entitásmódosítások

A V3 válaszobjektum előre összeállított entitások módosításait tartalmazza. További információért tekintse át [az előre összeállított entitásokat.](luis-reference-prebuilt-entities.md)

#### <a name="list-entity-prediction-changes"></a>Entitás előrejelzési változásainak listázása

A listaentitás előrejelzésének JSON-ja tömbtömbök tömbjeként változott:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Minden belső tömb az utterance (kifejezés) szövegnek felel meg. A belső objektum tömb, mivel ugyanaz a szöveg egy listaentitás több allistájában is megjelenhet.

Amikor az `entities` objektum az `$instance` objektum között leképezést rendel az objektumhoz, az objektumok sorrendje megmarad a listaentitás-előrejelzésekhez.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Entitásszerepkör neve az entitásnév helyett

V2-ben `entities` a tömb visszaadta az összes előre jelzett entitást, amelynek az entitásneve az egyedi azonosító. A V3-ban, ha az entitás szerepköröket használ, és az előrejelzés egy entitásszerepkörhöz, az elsődleges azonosító a szerepkör neve. Ez azért lehetséges, mert az entitásszerepkör-neveknek egyedinek kell lenniük a teljes alkalmazásban, beleértve más modell (szándék, entitás) neveket is.

A következő példában: fontolja meg egy `Yellow Bird Lane`utterance (kifejezés), amely tartalmazza a szöveget, . Ezt a szöveget egyéni `Location` entitás szerepköreként jósolják `Destination`meg.

|Kimondott szöveg|Entitás neve|Szerepkörnév|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

A V2-ben az entitást az _entitás neve_ azonosítja, és az objektum tulajdonságaként a következő szerepkört kell játszania:

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

A V3-ban az entitásra az _entitás szerepkör_hivatkozik, ha az előrejelzés a szerepkörre vonatkozik:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

A V3-ban ugyanaz `verbose` az eredmény, ha a jelző az entitás metaadatait adja vissza:

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

A külső entitások lehetővé teszik a LUIS-alkalmazás számára az entitások azonosítását és címkézését futásidőben, amelyek meglévő entitások szolgáltatásaként használhatók. Ez lehetővé teszi, hogy saját különálló és egyéni entitáskibontók használata előtt lekérdezések az előrejelzési végpontra. Mivel ez történik a lekérdezés előrejelzési végpont, nem kell újrabetanítása és közzététele a modell.

Az ügyfél-alkalmazás biztosítja a saját entitás kinyerő kezelő kezelő entitás egyeztető és meghatározza a helyét az adott egyező entitás utterance (kifejezés) az adott entitás, majd elküldi az információt a kéréssel.

A külső entitások bármely entitástípus kiterjesztésének mechanizmusai, miközben továbbra is jelekként használják más modelleknek, például szerepköröknek, összetetteknek és másoknak.

Ez olyan entitás esetében hasznos, amely csak a lekérdezés-előrejelzés imént rendelkezik adatokkal. Ilyen típusú adatok például a felhasználónkénti adatok vagy meghatározott adatok folyamatosan változnak. A LUIS kapcsolattartó entitást kiterjesztheti a felhasználó partnerlistájából származó külső adatokkal.

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

Az előző utterance (kifejezés) `him` kifejezési `Hazem`a. A társalgási csevegési robot `him` a POST törzsében leképezheti az `Hazem`első utterance (kifejezés) kinyert entitásértéket.

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



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Az előrejelzés időpontjában átadott dinamikus listák

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

## <a name="deprecation"></a>Elavulás

A V2 API nem lesz elavult legalább 9 hónappal a V3 előzetes verzió után.

## <a name="next-steps"></a>További lépések

A V3 API dokumentációjával frissítse a [LUIS-végpont](https://aka.ms/luis-api-v3) API-k meglévő REST-hívásait.
