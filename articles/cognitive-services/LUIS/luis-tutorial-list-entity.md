---
title: Szövegegyezési entitások kibűlése – LUIS
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan adhat hozzá egy listaentitást, amely segít a LUIS-nak egy szó vagy kifejezés változatainak címkézésében.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: f3c99856eaffc454754618a1eac34630b985a77e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499479"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Listaentitás használata az entitásészlelés növeléséhez 
Ez a cikk bemutatja a [listaentitás](luis-concept-entity-types.md) használatát az entitásészlelés növeléséhez. A listaentitásokat nem kell címkézni, mivel pontosan egyeznek a kifejezésekkel.  

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Listaentitás létrehozása 
> * Normalizált értékek és szinonimák hozzáadása
> * Továbbfejlesztett entitásazonosítás ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Legújabb [Node.js](https://nodejs.org)
> * [HomeAutomation LUIS alkalmazás](luis-get-started-create-app.md). Ha nem hozta létre a Home Automation alkalmazást, hozzon létre egy új alkalmazást, és adja hozzá az Előre összeállított tartomány **homeautomation**. Tanítsa be és tegye közzé az alkalmazást. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (ha többször is lekérdezi), az alkalmazásazonosító, verzióazonosító és a LUIS-alkalmazás [régiója.](luis-reference-regions.md)

> [!Tip]
> Ha még nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókra.](https://azure.microsoft.com/free/)

A cikkben szereplő összes kód elérhető az [Azure-Samples GitHub-tárházban.](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity) 

## <a name="use-homeautomation-app"></a>A HomeAutomation alkalmazás használata
A HomeAutomation alkalmazással vezérelheti az olyan eszközöket, mint a fények, a szórakoztató rendszerek és a környezetszabályozás, például a fűtés és a hűtés. Ezek a rendszerek több különböző nevet tartalmaznak, amelyek magukban foglalhatják a gyártó nevét, beceneveit, betűszavakat és szleng. 

Az egyik rendszer, amely sok nevet különböző kultúrák és demográfia a termosztát. A termosztát vezérelheti mind a hűtési és fűtési rendszerek egy ház vagy épület.

Ideális esetben a következő kimondott szövegeket kell oldani a prebuilt entitás **HomeAutomation.Device:**

|#|Megnyilatkozás|azonosított szervezet|pontszám|
|--|--|--|--|
|1|kapcsolja be az ac|HomeAutomation.Device - "ac"|0.8748562|
|2|kapcsolja fel a hőt|HomeAutomation.Device - "hő"|0.784990132|
|3|hogy hidegebb|||

Az első két utterances leképezése különböző eszközökre. A harmadik utterance (kifejezés), "legyen hidegebb", nem felel meg egy eszköz, hanem kéri az eredményt. A LUIS nem tudja, hogy a "hidegebb" kifejezés azt jelenti, hogy a termosztát a kért eszköz. Ideális esetben a LUIS-nak fel kell oldania az összes ilyen kimondott szöveget ugyanarra az eszközre. 

## <a name="use-a-list-entity"></a>Listaentitás használata
A HomeAutomation.Device entitás nagy egy kis számú eszköz, vagy néhány változata a nevek. Egy irodaház vagy campus esetében az eszköznevek a HomeAutomation.Device entitás hasznosságán túlnőnek. 

A **listaentitás** jó választás ebben a forgatókönyvben, mert egy épületben vagy campusban lévő eszköz kifejezéskészlete ismert készlet, még akkor is, ha hatalmas készlet. Egy lista entitás használatával a LUIS a termosztát készletében bármilyen lehetséges értéket kaphat, és csak az egyetlen eszköz "termosztát" lesz oldja fel. 

Ez a cikk létrehoz egy entitáslistát a termosztáttal. A cikkben a termosztát alternatív nevei a következők: 

|a termosztát alternatív nevei|
|--|
| Ac |
| a/c|
| a-c|
|Melegítő|
|Forró|
|Melegebb|
|Hideg|
|Hidegebb|

Ha a LUIS-nak gyakran kell meghatároznia egy új alternatívát, akkor egy [kifejezéslista](luis-concept-feature.md#how-to-use-phrase-lists) jobb válasz.

## <a name="create-a-list-entity"></a>Listaentitás létrehozása
Hozzon létre egy Node.js fájlt, és másolja a következő kódot. Módosítsa a authoringKey, appId, versionId és régió értékeket.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Az alábbi paranccsal telepítheti az NPM-függőségeket, és futtathatja a kódot a listaentitás létrehozásához:

```console
npm install && node add-entity-list.js
```

A futtatás kimenete a listaentitás azonosítója:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>A modell betanítása
Betanítsa a LUIS-t annak érdekében, hogy az új lista befolyásolja a lekérdezés eredményét. A képzés két részből álló képzési folyamat, majd az állapot ellenőrzése, ha a képzés történik. Egy alkalmazás sok modell eltarthat néhány pillanatig, hogy a vonat. A következő kód betanítása az alkalmazás, majd megvárja, amíg a képzés sikeres. A kód várakozási és újrapróbálkozási stratégiát használ a 429 -es "Túl sok kérelem" hiba elkerülésére. 

Hozzon létre egy Node.js fájlt, és másolja a következő kódot. Módosítsa a authoringKey, appId, versionId és régió értékeket.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Az alkalmazás betanításához használja a következő parancsot a kód futtatásához:

```console
node train.js
```

A futtatás kimenete a LUIS-modellek betanításának minden egyes iterációjának állapota. A következő végrehajtás csak egy edzésellenőrzést igényelt:

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
Közzététel, hogy a listaentitás elérhető vé válik a végpontról.

Hozzon létre egy Node.js fájlt, és másolja a következő kódot. Módosítsa a végpontkulcs, appAzonosító és régió értékeket. Használhatja a authoringKey, ha nem tervezi, hogy hívja ezt a fájlt túl a kvóta határt.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Az alkalmazás lekérdezéséhez használja a következő parancsot a kód futtatásához:

```console
node publish.js
```

A következő kimenet tartalmazza a végpont URL-címét a lekérdezésekhez. A valódi JSON-eredmények közé tartozna a valódi appID. 

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
Az alkalmazás lekérdezése a végpontról annak igazolására, hogy a listaentitás segít a LUIS-nak meghatározni az eszköz típusát.

Hozzon létre egy Node.js fájlt, és másolja a következő kódot. Módosítsa a végpontkulcs, appAzonosító és régió értékeket. Használhatja a authoringKey, ha nem tervezi, hogy hívja ezt a fájlt túl a kvóta határt.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

A kód futtatásához és az alkalmazás lekérdezéséhez használja a következő parancsot:

```console
node train.js
```

A kimenet a lekérdezés eredménye. Mivel a kód hozzáadta a **részletes** név/érték párt a lekérdezési karakterlánchoz, a kimenet tartalmazza az összes leképezést és azok pontszámait:

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

**A termosztát** konkrét eszközét a "melegítés" eredményorientált lekérdezésével azonosítják. Mivel az eredeti HomeAutomation.Device entitás még mindig az alkalmazásban van, az eredményeket is láthatja. 

Próbálja meg a másik két utterances látni, hogy ők is vissza, mint a termosztát. 

|#|Megnyilatkozás|Entitás|type|érték|
|--|--|--|--|--|
|1|kapcsolja be az ac| Ac | Eszközök listája | Termosztát|
|2|kapcsolja fel a hőt|Hő| Eszközök listája |Termosztát|
|3|hogy hidegebb|Hidegebb|Eszközök listája|Termosztát|

## <a name="next-steps"></a>További lépések

Létrehozhat egy másik Lista entitást, hogy az eszközhelyeket kibővítse a szobákra, emeletekre vagy épületekre. 
