---
title: A kivonatot szöveg egyezés entitások
description: Ismerje meg, hogy egy lista entitás segítségével egy szót vagy kifejezést a LUIS-címke változatok hozzáadása.
services: cognitive-services
author: diberry
titleSuffix: Azure
manager: cgronlun
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: fe9276cb0af96baa8d818b1f2e965c83cb6b26c2
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211491"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Egy lista entitás használatával növelheti az entitás észlelése 
Ez az oktatóanyag bemutatja, hogy egy [entitás listában](luis-concept-entity-types.md) entitás észlelési növelése érdekében. Lista entitások nem kell címkével ellátott, mivel ezek a feltételek pontosan egyeznie kell.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
* Lista entitás létrehozása 
* Normalizált értékeket és a szinonimák hozzáadása
* Továbbfejlesztett entitás azonosító ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Legújabb [Node.js](https://nodejs.org)
> * [A LUIS alkalmazás HomeAutomation](luis-get-started-create-app.md). Ha nem rendelkezik a kezdőlap Automation létrehozott alkalmazást, hozzon létre egy új alkalmazást, és adja hozzá az előre összeállított tartományt **HomeAutomation**. Tanítsa be és tegye közzé az alkalmazást. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (ha sokszor lekérdezése), alkalmazás-azonosító, verzió:, és [régió](luis-reference-regions.md) a LUIS alkalmazás.

> [!Tip]
> Ha Ön még nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).

Ebben az oktatóanyagban kódja megtalálható a [Azure-minták GitHub-adattár](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity). 

## <a name="use-homeautomation-app"></a>HomeAutomation alkalmazás használata
Meghatározhatja az eszközök, például lámpa, szórakoztató rendszerek és a környezet azt szabályozza, például fűtés és hűtéssel HomeAutomation alkalmazás lehetőséget biztosít. Ezek a rendszerek rendelkeznie több különböző gyártó neveket, beceneveinek, Betűszavak és szleng kifejezéseket is tartalmazhat. 

Egy másik kulturális környezetek és a demográfiai sok nevet tartalmaz, a rendszer az okosórák. Egy thermostat hűtéssel és a egy ház vagy épület fűtésrendszerek is szabályozhatja.

Ideális esetben kell feloldhatónak lennie a következő utterances az előre összeállított entitások **HomeAutomation.Device**:

|#|Utterance (kifejezés)|azonosított entitás|pontszám|
|--|--|--|--|
|1|Kapcsolja be az ac|HomeAutomation.Device – "ac"|0.8748562|
|2|Kapcsolja be a megadott hőtérképrészlet|HomeAutomation.Device – "heat"|0.784990132|
|3|Győződjön meg arról, hogy annál alacsonyabb|||

Az első két utterances képezze le a különböző eszközökhöz. A harmadik utterance (kifejezés), "Győződjön meg arról, hogy annál alacsonyabb", egy eszköz nem leképezése, de ehelyett kér az eredményt. A LUIS nem ismert, hogy "annál, alacsonyabb" kifejezés azt jelenti, hogy az okosórák a kért eszköz. Ideális esetben a LUIS kell feloldhatónak lennie az összes alábbi utterances ugyanarra az eszközre. 

## <a name="use-a-list-entity"></a>Egy lista entitás használja
A HomeAutomation.Device entitás kiválóan alkalmazható az eszközök vagy a nevek néhány változata kevés. Irodaépület konferenciatermére vagy campus az eszköz nevét a HomeAutomation.Device entitás hasznosságát túli növekszik. 

A **entitás listában** azért megfelelő választás az ebben a forgatókönyvben egy épületben vagy campus eszköz feltételkészlet olyan ismert készlete, akkor is, ha egy hatalmas beállítása. Egy lista entitás használatával LUIS is lehetséges értéket kapják meg a készletet az okosórák, és csak az egyetlen eszköz "thermostat" lefelé oldható meg. 

Ebben az oktatóanyagban a-ra entitás lista létrehozása történik. Ebben az oktatóanyagban egy thermostat tartozó alternatív nevek a következők: 

|alternatív neveket, az okosórák|
|--|
| AC |
| / c|
| a-c|
|melegítőkráter|
|gyakori elérésű|
|hotter|
|ritka elérésű|
|annál alacsonyabb|

Ha a LUIS határozza meg egy új lehetőség gyakran kell majd egy [kifejezéslista](luis-concept-feature.md#how-to-use-phrase-lists) van még jobb válasz.

## <a name="create-a-list-entity"></a>Lista entitás létrehozása
Hozzon létre egy Node.js-fájlt, és másolja az alábbi kódot a fájlba. Módosítsa a authoringKey, appId, versionId és régióját.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

A következő paranccsal telepítse az NPM függőségeit, és futtassa a kódot a lista entitás létrehozása:

```console
npm install && node add-entity-list.js
```

A Futtatás kimenete a lista entitás azonosítója:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>A modell betanítása
A LUIS betanításához ahhoz, hogy az új lista befolyásolhatja a lekérdezés eredményeit. Képzési az képzés, majd állapotának ellenőrzése, ha a betanítási történik egy kétlépéses folyamat. Számos modellt az alkalmazás taníthat be néhány percet is igénybe vehet. A következő kód betanítja az alkalmazást, majd megvárja, amíg a képzés létrejött. A kód várakozási és újrapróbálkozási stratégiát használ, a 429-es elkerülése érdekében "túl sok kérelem" hiba történt. 

Hozzon létre egy Node.js-fájlt, és másolja az alábbi kódot a fájlba. Módosítsa a authoringKey, appId, versionId és régióját.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Az alábbi parancs segítségével futtassa a kódot az alkalmazás betanítása:

```console
node train.js
```

A Futtatás kimenete a képzés a LUIS-modell minden egyes ismétléskor állapotát. A következő végrehajtási képzési csak egy ellenőrzés szükséges:

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
A lista entitás érhető el a végpontot, tegye közzé.

Hozzon létre egy Node.js-fájlt, és másolja az alábbi kódot a fájlba. Módosítsa a endpointKey, appId és régióját. A authoringKey is használhatja, ha nem tervezi, hogy ez a fájl túl a kvótahatár hívja.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Az alábbi parancs segítségével futtassa a kódot az alkalmazás lekérdezéséhez:

```console
node publish.js
```

A következő kimenet tartalmazza a lekérdezéseket a végpont URL-címe. Valódi JSON-eredményeket a valódi appID tartalmazhat. 

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
A lekérdezés a végpontot, hogy igazolja, hogy a lista entitás segít megállapítani az eszköz típusától, a LUIS az alkalmazásból.

Hozzon létre egy Node.js-fájlt, és másolja az alábbi kódot a fájlba. Módosítsa a endpointKey, appId és régióját. A authoringKey is használhatja, ha nem tervezi, hogy ez a fájl túl a kvótahatár hívja.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

A következő paranccsal futtassa a kódot, és az alkalmazás lekérdezést:

```console
node train.js
```

A lekérdezés eredményeinek kimenete. Mivel a kód hozzáadja a **részletes** a lekérdezési karakterlánchoz, a kimeneti név-érték pár tartalmazza az összes leképezések és azok pontszámok:

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

Az adott eszköz a **Thermostat** , amelynél az "kapcsolja be a megadott hőtérképrészlet" eredmény-orientált lekérdezéséhez. Mivel az eredeti HomeAutomation.Device entitás továbbra is az alkalmazásban, megtekintheti az eredményeket is. 

Próbálja ki, hogy azok is vissza egy okosórák, hogy más két megcímkézzen. 

|#|Utterance (kifejezés)|entitás|type|érték|
|--|--|--|--|--|
|1|Kapcsolja be az ac| AC | DevicesList | Thermostat|
|2|Kapcsolja be a megadott hőtérképrészlet|heat| DevicesList |Thermostat|
|3|Győződjön meg arról, hogy annál alacsonyabb|annál alacsonyabb|DevicesList|Thermostat|

## <a name="next-steps"></a>További lépések

Bontsa ki az eszköz helyek termek, emeleteken vagy épületek egy másik lista entitás hozhat létre. 
