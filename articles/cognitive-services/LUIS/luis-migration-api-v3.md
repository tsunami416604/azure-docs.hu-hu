---
title: Előrejelzési végpont változások a V3 API-ban
description: A lekérdezés előrejelzési végpont V3 API-k megváltoztak. Ez az útmutató a 3-as verziójú végpontAPI-kra való áttelepítése.
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: 4b6d28b24ffc6c0a848d1c7a34e863da0606d936
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530385"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Előrejelzési végpont változások a V3

A lekérdezés előrejelzési végpont V3 API-k megváltoztak. Ez az útmutató a 3-as verziójú végpontAPI-kra való áttelepítése.

**Általánosan elérhető állapot** – ez a V3 API jelentős JSON-kérelem- és válaszmódosításokat tartalmaz a V2 API-ból.

A V3 API a következő új funkciókat biztosítja:

* [Külső entitások](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [Dinamikus listák](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
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
|`dynamicLists`|tömb|Csak V3|Nem szükséges.|[A dinamikus listák](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time) lehetővé teszik egy meglévő betanított és közzétett listaentitás kiterjesztését, már a LUIS alkalmazásban.|
|`externalEntities`|tömb|Csak V3|Nem szükséges.|[A külső entitások](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time) lehetővé teszik a LUIS-alkalmazás számára az entitások azonosítását és címkézését futásidőben, amelyek meglévő entitások szolgáltatásaként használhatók. |
|`options.datetimeReference`|sztring|Csak V3|Nincs alapértelmezett|A [datetimeV2 eltolásának meghatározására](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)szolgál. A datetimeReference formátuma [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|logikai|Csak V3|hamis|Itt adható meg, hogy a felhasználó [külső entitását (a meglévő entitás nevével megegyező névvel)](schema-change-prediction-runtime.md#override-existing-model-predictions) használja-e a rendszer, vagy a modellben lévő meglévő entitást használja az előrejelzéshez. |
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

<a name="external-entities-passed-in-at-prediction-time"></a>
<a name="override-existing-model-predictions"></a>

## <a name="extend-the-app-at-prediction-time"></a>Az alkalmazás kiterjesztése előrejelzési időpontban

Ismerje [meg,](schema-change-prediction-runtime.md) hogyan bővítheti az alkalmazást előrejelzési futásidőben.

## <a name="deprecation"></a>Elavulás

A V2 API nem lesz elavult legalább 9 hónappal a V3 előzetes verzió után.

## <a name="next-steps"></a>További lépések

A V3 API dokumentációjával frissítse a [LUIS-végpont](https://aka.ms/luis-api-v3) API-k meglévő REST-hívásait.
