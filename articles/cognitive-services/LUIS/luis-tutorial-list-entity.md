---
title: Címke automatikusan egy lista entitást Nodejs rendelkező entitások |} Microsoft Docs
description: Útmutató a lista entitás érdekében LUIS címke változatait egy szót vagy kifejezést.
services: cognitive-services
author: v-geberr
titleSuffix: Azure
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/21/2018
ms.author: v-geberr
ms.openlocfilehash: e8558ecf4a64dbccef6e6367c1447bdcdb005126
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347135"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>A lista entitás segítségével entitás észlelési növelése 
Ez az oktatóanyag bemutatja, hogy egy [entitás listában](luis-concept-entity-types.md) entitás észlelési növelése érdekében. Lista entitások nem kell kell címkével, mert azok a feltételek pontos egyezést.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
* A lista entitás létrehozása 
* Normalizált értékek és a szinonimák hozzáadása
* Továbbfejlesztett entitás azonosítója ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Legújabb [Node.js](https://nodejs.org)
> * [HomeAutomation LUIS app](luis-get-started-create-app.md). Ha nem rendelkezik a kezdőlap Automation alkalmazást létrehozni, hozzon létre egy új alkalmazást, és az előre elkészített tartomány hozzáadása **HomeAutomation**. Betanítása, és tegye közzé az alkalmazást. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (ha sokszor lekérdezése), Alkalmazásazonosító, Verzióazonosító, és [régió](luis-reference-regions.md) LUIS alkalmazás.

> [!Tip]
> Ha még nem rendelkezik előfizetéssel, akkor a regisztrálhatja a [ingyenes fiókot](https://azure.microsoft.com/free/).

A jelen oktatóanyagban található kód mindegyikét érhető el a [LUIS-minták github-tárházban](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-list-entity). 

## <a name="use-homeautomation-app"></a>HomeAutomation alkalmazás használata
A HomeAutomation alkalmazás által biztosított vezérli az eszközöket, például fény, a Szórakozás rendszerek és a környezet például melegítés- és hűtési szabályozza. Ezek a rendszerek gyártó nevét, becenevet, Betűszavak és zsargon is tartalmazó számos különböző névvel rendelkeznek. 

Egy olyan rendszert, amely sok nevekkel rendelkezzen a másik kulturális környezetek és a demográfiai adatoknak megfelelő elérési a termosztát. Egy termosztát hűtési és fűtésrendszerek house vagy épület is szabályozhatja.

Ideális esetben oldja fel az a következő utterances az előre elkészített entitás **HomeAutomation.Device**:

|#|utterance|entitás azonosított|pontszám|
|--|--|--|--|
|1|a váltakozó bekapcsolása|HomeAutomation.Device - "ac"|0.8748562|
|2|Kapcsolja be a tűz|HomeAutomation.Device - "tűz"|0.784990132|
|3|Ellenőrizze, hogy annál alacsonyabb|||

Az első két utterances képezi le különböző eszközökre. A harmadik utterance "teszik annál alacsonyabb", nem képezi le egy eszközre, de helyette kéri az eredményt. LUIS nem ismert, hogy a "annál, alacsonyabb" kifejezés azt jelenti, hogy a termosztát a kért eszköz. Ideális esetben LUIS oldja fel az összes ezek utterances ugyanarra az eszközre. 

## <a name="use-a-list-entity"></a>A lista entitás
A HomeAutomation.Device eszközök vagy a nevek néhány változatait néhány nagy. Egy irodaépületet jelképez vagy egyetemi az eszköz nevét a HomeAutomation.Device entitás hasznosságát nagyobb legyen. 

A **entitás listában** jó választás az ebben a forgatókönyvben épület vagy egyetemi eszköz feltételei lesz egy ismert gyűjteményével, mert akkor is, ha azt egy olyan túl nagy. Egy lista entitás használatával LUIS is lehetséges értéket kap a csoport a termosztát, és csak az egyetlen eszköz "termosztát" le oldható meg. 

Ez az oktatóanyag egy entitás lista létrehozása a c-ra lesz. Ebben az oktatóanyagban egy termosztát alternatív nevek a következők: 

|alternatív neveket a termosztát|
|--|
| AC |
| / c|
| a-c|
|a melegítő|
|gyakran használt adatok|
|hotter|
|Cold|
|annál alacsonyabb|

Ha LUIS határozza meg egy új alternatív gyakran, majd egy [kifejezéslista](luis-concept-feature.md#how-to-use-phrase-lists) jobb válasz.

## <a name="create-a-list-entity"></a>A lista entitás létrehozása
Hozzon létre egy Node.js-fájlt, és a következő kódot bemásolhatja. Módosítsa a authoringKey, appId, versionId és régióban.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

A következő paranccsal NPM függőségek telepítése és futtatása a kódot a lista entitás létrehozásához:

```Javascript
npm install && node add-entity-list.js
```

A Futtatás a lista entitás azonosítója kimenete:

```Javascript
026e92b3-4834-484f-8608-6114a83b03a6
```
## <a name="train-the-model"></a>A modell betanítása
A vonat LUIS ahhoz, hogy az új lista befolyásolják a lekérdezés eredményeit. Képzési egy kétlépéses folyamat, a képzési, majd állapotának ellenőrzése, ha a képzés történik. Egy alkalmazást a sok modellek betanítása néhány percet is igénybe vehet. A következő kódot az alkalmazás betanítja, majd vár, amíg a képzés nem sikeres. A kód a 429 elkerülése érdekében használja a várakozási és újrapróbálkozási stratégiát "túl sok kérelem" hiba. 

Hozzon létre egy Node.js-fájlt, és a következő kódot bemásolhatja. Módosítsa a authoringKey, appId, versionId és régióban.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

A következő paranccsal futtassa a kódot az alkalmazás betanítása:

```Javascript
node train.js
```

A Futtatás minden egyes ismétlés a képzés LUIS modell állapotának eredménye. A következő végrehajtási képzési csak egy ellenőrzés szükséges:

```Javascript
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
Így a lista entitás érhető el a végpontról közzététele.

Hozzon létre egy Node.js-fájlt, és a következő kódot bemásolhatja. Módosítsa a endpointKey, az appId és a terület. A authoringKey is használhatja, ha nem tervezi, hogy hívható meg a fájl meghaladja a kvótát.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Az alábbi parancs segítségével az alkalmazás lekérdezése a kódra:

```Javascript
node publish.js
```

A következő kimeneti lekérdezéseket végpont URL-címét tartalmazza. A valódi appID valós JSON-eredményeket foglalja magában. 

```JSON
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
A lekérdezés az alkalmazáshoz, hogy a lista entitás segítségével határozza meg az eszköztípus LUIS igazolnia a végpont.

Hozzon létre egy Node.js-fájlt, és a következő kódot bemásolhatja. Módosítsa a endpointKey, az appId és a terület. A authoringKey is használhatja, ha nem tervezi, hogy hívható meg a fájl meghaladja a kvótát.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

A következő paranccsal lekérdezheti az alkalmazást, és a kódra:

```Javascript
node train.js
```

A lekérdezés eredményeinek eredménye. Mivel a kódot adja meg a **részletes** a lekérdezési karakterláncra, a kimeneti név-érték pár összes leképezések és eredményeiket tartalmazza:

```JSON
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

Az adott eszközön **termosztát** , amelynél az "kapcsolja be a tűz" eredményt irányuló lekérdezés. Mivel az eredeti HomeAutomation.Device entitás továbbra is az alkalmazásban, láthatja, valamint az eredményeket. 

Próbálja meg, hogy azok is vissza egy termosztát, hogy a többi két utterances. 

|#|utterance|entitás|type|érték|
|--|--|--|--|--|
|1|a váltakozó bekapcsolása| AC | DevicesList | Termosztát|
|2|Kapcsolja be a tűz|tűz| DevicesList |Termosztát|
|3|Ellenőrizze, hogy annál alacsonyabb|annál alacsonyabb|DevicesList|Termosztát|

## <a name="next-steps"></a>További lépések

Egy másik lista entitás eszköz helyek kibővítéséhez helyiségekben, padlók vagy épületek hozhat létre. 
