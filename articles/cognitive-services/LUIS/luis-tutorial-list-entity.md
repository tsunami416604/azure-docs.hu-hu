---
title: Extact-szöveg egyeztetése entitások – LUIS
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan adhat hozzá egy list entitást egy szó vagy kifejezés LUIS Label-változatának elősegítéséhez.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: f3c99856eaffc454754618a1eac34630b985a77e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499479"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Entitások észlelésének megnövelésére szolgáló lista entitás használata 
Ez a cikk a [lista entitások](luis-concept-entity-types.md) használatát mutatja be az entitások észlelésének növeléséhez. Az entitások listázása nem szükséges, mivel azok pontosan egyeznek a feltételekkel.  

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Lista entitás létrehozása 
> * Normalizált értékek és szinonimák hozzáadása
> * Továbbfejlesztett entitás-azonosító ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Legújabb [Node. js](https://nodejs.org)
> * [HOMEAUTOMATION Luis-alkalmazás](luis-get-started-create-app.md). Ha nincs létrehozva a Home Automation alkalmazás, hozzon létre egy új alkalmazást, és adja hozzá az előre elkészített tartományi **HomeAutomation**. Tanítsa be és tegye közzé az alkalmazást. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (ha többször kérdezi le), az alkalmazás azonosítóját, a Version ID-t és a [régiót](luis-reference-regions.md) a Luis-alkalmazáshoz.

> [!Tip]
> Ha még nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).

A cikkben szereplő összes kód az [Azure-Samples GitHub-tárházban](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity)érhető el. 

## <a name="use-homeautomation-app"></a>HomeAutomation-alkalmazás használata
A HomeAutomation-alkalmazás lehetővé teszi az eszközök, például a fények, a szórakoztató rendszerek és a környezeti vezérlők, például a fűtés és a hűtés szabályozását. Ezek a rendszerek több különböző névvel rendelkeznek, amelyek magukban foglalhatják a gyártó nevét, a beceneveket, a betűszókat és a szlenget. 

Egy olyan rendszer, amely számos névvel rendelkezik a különböző kultúrákban és a demográfiai kapcsolatban, a termosztát. A Termosztátok alkalmasak a hűtési és a fűtő rendszerek szabályozására egy adott házban vagy épületben.

Ideális esetben a következő hosszúságú kimondott szöveg kell feloldania az előre elkészített entitás **HomeAutomation. eszközén**:

|#|utterance|azonosított entitás|pontszám|
|--|--|--|--|
|1|az AC bekapcsolása|HomeAutomation. Device-"AC"|0,8748562|
|2|a hő kikapcsolása|HomeAutomation. Device – "Heat"|0,784990132|
|3|legyen hidegebb|||

Az első két hosszúságú kimondott szöveg a különböző eszközökre mutat. A harmadik Kimondás, a "hidegebb", nem egy eszközre van leképezve, hanem egy eredményt kér. LUIS nem tudja, hogy a "hidegebb" kifejezés azt jelenti, hogy a termosztát a kért eszköz. Ideális esetben a LUIS-nek fel kell oldania az összes ilyen hosszúságú kimondott szöveg ugyanarra az eszközre. 

## <a name="use-a-list-entity"></a>Lista entitás használata
A HomeAutomation. Device entitás ideális kis számú eszközhöz vagy a nevek néhány változatához. Az irodaház vagy az Egyetem esetében az eszközök nevei a HomeAutomation. Device entitás hasznosságán túl növekednek. 

A **List entitás** jó választás ehhez a forgatókönyvhöz, mert egy épületben vagy Egyetemen található eszköz használati feltételei egy ismert készlet, még akkor is, ha ez egy hatalmas készlet. A List entitás használatával a LUIS bármely lehetséges értéket megkaphat a készletben a termosztáthoz, és csak az egyetlen eszköz "termosztát"-ra oldható fel. 

Ez a cikk a termosztáttal rendelkező entitások listáját fogja létrehozni. Ebben a cikkben a termosztát alternatív nevei a következők: 

|alternatív nevek a termosztáthoz|
|--|
| AC |
| a/c|
| a – c|
|Bojler|
|gyakori|
|melegebb|
|Hideg|
|hidegebb|

Ha LUIS új alternatívát kell meghatároznia, akkor a [kifejezések listája](luis-concept-feature.md#how-to-use-phrase-lists) jobb válasz.

## <a name="create-a-list-entity"></a>Lista entitás létrehozása
Hozzon létre egy Node. js-fájlt, és másolja a következő kódot. Módosítsa a authoringKey, a appId, a versionId és a régió értékét.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

A következő parancs használatával telepítse a NPM-függőségeket, és futtassa a kódot a lista entitás létrehozásához:

```console
npm install && node add-entity-list.js
```

A Run kimenete a lista entitás azonosítója:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>A modell tanítása
A LUIS betanítása ahhoz, hogy az új lista hatással legyen a lekérdezés eredményeire. A képzés a betanítás kétrészes folyamata, majd az állapot ellenőrzése, ha a képzés elkészült. Számos modellel rendelkező alkalmazás eltarthat néhány percet a betanításhoz. A következő kód bevezeti az alkalmazást, majd megvárja a képzés sikerességét. A kód egy várakozási és újrapróbálkozási stratégiát használ, hogy elkerülje az 429 "túl sok kérés" hibát. 

Hozzon létre egy Node. js-fájlt, és másolja a következő kódot. Módosítsa a authoringKey, a appId, a versionId és a régió értékét.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

A következő paranccsal futtathatja a kódot az alkalmazás betanításához:

```console
node train.js
```

A Run kimenete a LUIS modellek betanításának minden egyes ismétlésének állapota. A következő végrehajtáshoz csak egy vizsgálat szükséges a képzéshez:

```console
1 trained = true
[ { modelId: '2c549f95-867a-4189-9c35-44b95c78b70f',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '5530e900-571d-40ec-9c78-63e66b50c7d4',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '519faa39-ae1a-4d98-965c-abff6f743fe6',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9671a485-36a9-46d5-aacd-b16d05115415',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9ef7d891-54ab-48bf-8112-c34dcd75d5e2',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '8e16a660-8781-4abf-bf3d-f296ebe1bf2d',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } } ]

```
## <a name="publish-the-model"></a>A modell közzététele
Tegye közzé, hogy a lista entitás elérhető legyen a végpontról.

Hozzon létre egy Node. js-fájlt, és másolja a következő kódot. Módosítsa a endpointKey, a appId és a régió értékét. A authoringKey akkor használhatja, ha nem tervezi a fájl meghívását a kvóta korlátján túl.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

A következő paranccsal futtathatja a kódot az alkalmazás lekérdezéséhez:

```console
node publish.js
```

A következő kimenet tartalmazza az összes lekérdezés végpontjának URL-címét. A valós JSON-eredmények közé tartozik a valódi appID. 

```json
{ 
  versionId: null,
  isStaging: false,
  endpointUrl: 'https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>',
  region: null,
  assignedEndpointKey: null,
  endpointRegion: 'westus',
  publishedDateTime: '2018-01-29T22:17:38Z' }
}
```

## <a name="query-the-app"></a>Az alkalmazás lekérdezése 
Az alkalmazás lekérdezése a végpontról annak bizonyítására, hogy a List entitás segíti a LUIS az eszköz típusának meghatározását.

Hozzon létre egy Node. js-fájlt, és másolja a következő kódot. Módosítsa a endpointKey, a appId és a régió értékét. A authoringKey akkor használhatja, ha nem tervezi a fájl meghívását a kvóta korlátján túl.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Futtassa a következő parancsot a kód futtatásához és az alkalmazás lekérdezéséhez:

```console
node train.js
```

A kimenet a lekérdezés eredményei. Mivel a kód a **részletes** név/érték párokat adta hozzá a lekérdezési karakterlánchoz, a kimenet tartalmazza az összes leképezést és azok pontszámait:

```json
{
  "query": "turn up the heat",
  "topScoringIntent": {
    "intent": "HomeAutomation.TurnOn",
    "score": 0.139018849
  },
  "intents": [
    {
      "intent": "HomeAutomation.TurnOn",
      "score": 0.139018849
    },
    {
      "intent": "None",
      "score": 0.120624863
    },
    {
      "intent": "HomeAutomation.TurnOff",
      "score": 0.06746891
    }
  ],
  "entities": [
    {
      "entity": "heat",
      "type": "HomeAutomation.Device",
      "startIndex": 12,
      "endIndex": 15,
      "score": 0.784990132
    },
    {
      "entity": "heat",
      "type": "DevicesList",
      "startIndex": 12,
      "endIndex": 15,
      "resolution": {
        "values": [
          "Thermostat"
        ]
      }
    }
  ]
}
```

A **termosztát** adott eszközét a "hő kikapcsolására" vonatkozó eredményorientált lekérdezéssel azonosítjuk. Mivel az eredeti HomeAutomation. Device entitás még mindig az alkalmazásban van, az eredményeit is megtekintheti. 

Próbálja ki a másik két hosszúságú kimondott szöveg, és ellenőrizze, hogy a rendszer termosztátként is visszaadja-e őket. 

|#|utterance|Entitás|type|érték|
|--|--|--|--|--|
|1|az AC bekapcsolása| AC | DevicesList | Okostelefonok|
|2|a hő kikapcsolása|intenzitástérkép| DevicesList |Okostelefonok|
|3|legyen hidegebb|hidegebb|DevicesList|Okostelefonok|

## <a name="next-steps"></a>További lépések

Létrehozhat egy másik lista entitást az eszközök helyeinek kibontásához a szobákhoz, a padlóhoz vagy az épületekhez. 
