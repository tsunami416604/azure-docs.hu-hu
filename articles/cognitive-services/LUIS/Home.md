---
title: Hamarosan nyelvi ismertetése (LUIS) az Azure-ban |} Microsoft Docs
description: Megtudhatja, hogyan nyelvi ismertetése (LUIS) segítségével állítsa a gépi tanulási az alkalmazások teljesítménye.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/22/2017
ms.author: v-geberr
ms.openlocfilehash: 5656ce051a171812e6d008d56b42bf16c0c2a2ca
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "35349953"
---
# <a name="what-is-language-understanding-luis"></a>Mi az a nyelvi ismertetése (LUIS)?
Nyelvi ismertetése (LUIS) egy felhőalapú szolgáltatás, amely egyéni gépi tanulásra vonatkozik egy felhasználói conversational, természetes nyelvű szövege előrejelzése általános jelentését, és ki a megfelelő, részletes információkat. 

Egy ügyfél-alkalmazást LUIS lehet bármely conversational alkalmazás, amely kommunikál a felhasználók természetes nyelven a tevékenységek végrehajtásához. Ügyfélalkalmazások például a közösségi média alkalmazások chatbots és asztali alkalmazások engedélyezését.  

![Információ LUIS etetési 3 alkalmazások fogalmi képe](./media/luis-overview/luis-entry-point.png)

Az ügyfélalkalmazást (például egy chatbot) a mi egy személy a HTTP-kérelem a LUIS saját szavakat szeretne a felhasználó szöveget küld. LUIS a megjegyzett modell vonatkozik a felhasználói bevitel értelmezhető természetes nyelv, és a JSON formátum választ ad vissza. Az ügyfélalkalmazás a JSON-választ a felhasználói kérelmek teljesítése érdekében kapcsolódott. 

![Fogalmi képeket a LUIS Chatbot használata](./media/luis-overview/luis-overview-process-2.png)

## <a name="what-is-a-luis-app"></a>Mi az, hogy egy LUIS alkalmazást?
Egy LUIS alkalmazást egy olyan tartományspecifikus nyelvi modell, hogy alakítson ki. Indítsa el az alkalmazást egy előre elkészített tartomány modellel, összeállítása a saját vagy egyesítés egy előre elkészített tartomány adatot a saját egyéni információ.

A modellek listáját általános felhasználói céljaira nevű kezdődik _leképezések_, például "Könyv repülési" vagy "Forduljon támogatási szolgálat." Megadja a felhasználó például kifejezések nevű _utterances_ a leképezések az. Ezután jelölje meg jelentős szavakat vagy kifejezéseket a utterance hívása a _entitások_.

[Előre elkészített tartomány modellek] [ prebuilt-domains] összes adatot tartalmaznak, és indíthatja gyorsan LUIS nagyszerű módját.

<a name="Accessing-LUIS"></a>

Amikor a modell létrehozott és közzétett, az ügyfélalkalmazás utterances elküldi a LUIS [végpont API] [ endpoint-apis] és az előrejelzés eredmények JSON-választ kap.

### <a name="example-of-json-endpoint-response"></a>JSON-végpont válasz – példa

A JSON végpont választ, legalább a lekérdezés utterance és szándéka pontozási felső tartalmaz. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```

<a name="Key-LUIS-concepts"></a>

## <a name="what-is-a-luis-model"></a>Mi az a LUIS modell?
Egy LUIS modell az alábbiak tartalmazza:

* **[leképezések](#intents)**: felhasználói céljaira (tervezett művelet vagy eredmény) kategóriái
* **[entitások](#entities)**: adatok utterances száma, e-mailek vagy neve például adott típusú
* **[Példa utterances](#example-utterances)**: a felhasználó adja meg az ügyfél alkalmazásban mintaszöveggel

### <a name="intents"></a>Leképezések 
Egy [leképezés][add-intents], rövid a _szándékát_célú vagy a cél a kifejezett egy felhasználó utterance, például a felhőszolgáltató közötti átviteléhez foglalás, egy számlázási fizető vagy a hírek cikkében találja. Minden egyes művelethez megjelölésű hoz létre. Egy utazás app határozhatnak meg megjelölésű nevű "BookFlight." Az ügyfélalkalmazás használható leképezés pontozási felső elindítani egy műveletet. Például "BookFlight" leképezés LUIS küld vissza, ha az ügyfélalkalmazást elindíthatja az API-hívás a Foglalás vezérlősík jegy egy külső szolgáltatáshoz.

### <a name="entities"></a>Entitások
Egy [entitás] [ add-entities] fontos a felhasználó kérelmére a utterance belül található részletes adatait jelöli. Például a "Könyv Párizsi jegy" utterance egyetlen jegy van szükség, és "Párizsi" egy helyre. Két olyan entitásra "jegyet" egy "Párizsi", amely jelzi, a cél és egyetlen jegyet találhatók. 

Miután LUIS a található a felhasználó utterance az entitásokat ad vissza, az ügyfélalkalmazást entitások listáján paraméterek kiváltott művelethez használható. Például entitások, például a utazás cél, dátum és légitársaság foglalási a felhőszolgáltató közötti átviteléhez szükséges.

LUIS többféleképpen is azonosíthatja és entitások kategorizálását.

* **Előre elkészített entitások** LUIS rendelkezik leképezések, utterances, többek között számos előre elkészített tartomány modellek és [előre elkészített entitások][prebuilt-entities]. Az előre elkészített entitásokat is használhatja a leképezések és az előre elkészített modell utterances használata nélkül. Az előre elkészített entitások idő takarítható meg.

* **Egyéni entitások** LUIS számos lehetőséget biztosít a saját egyéni azonosításához [entitások] [ entity-concept] entitások gép megtanulta, adott vagy -konstans entitásokat és kombinációja számítógép-megtanulta, és literal.

### <a name="example-utterances"></a>Példa utterances
Példa [utterance] [ add-example-utterances] szöveges bevitel a felhasználótól, amelyet az alkalmazás megismeréséhez. Lehet, hogy néhány mondatot, például az "A jegy könyv Párizsba", illetve egy kódrészletet néhány mondatot, például a "Foglalási" vagy "Párizsi repülési." Utterances mindig nem megfelelően formázott, és az egy adott célt számos utterance változata is lehet. 10-20 példa utterances Helyezzen minden leképezés, és jelölje minden utterance szerepelnek.

|Példa felhasználói utterance|Kísérlet történt|Entitások|
|-----------|-----------|-----------|
|"És a felhőszolgáltató közötti átviteléhez foglalható le __Budapest__?"|BookFlight|Seattle|
|"Ha does tárolt __nyissa meg a__?"|StoreHoursAndLocation|megnyitás|
|"A értekezletet __1 pm__ rendelkező __Bob__ a terjesztési"|ScheduleMeeting|Bob du. 1|

## <a name="improve-prediction-accuracy"></a>Előrejelzési pontosság növeléséhez
Után az alkalmazás közzé van téve, és a felhasználó utterances kap, LUIS biztosít több módszert is növelve az előrejelzés pontosságát: [aktív tanulási](#active-learning) a végpont utterances [listák kifejezés](#phrase-lists) a tartomány word befoglalási, és [minták](#patterns) szükséges utterances számának csökkentése érdekében.

### <a name="active-learning"></a>Aktív tanulás
Az a [aktív tanulási](label-suggested-utterances.md) folyamat LUIS lehetővé teszi az alkalmazásnak, hogy valós utterances igazítja a végpont felülvizsgálandó kapott utterances kiválasztásával. Fogadja el, vagy javítsa ki a végpont előrejelzés teljesített kapcsolat-újraépítési és ismételt. LUIS Tanulja meg gyorsan iteratív ezt a folyamatot, a minimális mérete az idő és erőfeszítés véve. 

### <a name="phrase-lists"></a>Kifejezés listája 
LUIS biztosít [listák kifejezések](luis-concept-feature.md) úgy adhatja meg, fontos szavakat vagy kifejezéseket a modell-tartományban. LUIS a listák segítségével adja hozzá a további többszörösére szavak és kifejezések, amelyek akkor ellenkező esetben nem található a modellben.

### <a name="patterns"></a>Minták 
Minták lehetővé teszik egy leképezés utterance gyűjteményét közös egyszerűsítése [sablonok] [ patterns] word választott és word sorrendje. Ez lehetővé teszi további gyorsabb által kevesebb példa utterances kellene a leképezések az LUIS. Minták egy hibrid rendszer reguláris kifejezések és a gép megtanulta kifejezések. 

## <a name="using-luis"></a>LUIS használatával
A LUIS alkalmazást hozhat létre a [www.luis.ai](http://www.luis.ai) webhelyére vagy a programozott módon, az alkalmazás elkészítésére is a [szerzői](https://aka.ms/luis-authoring-apis) API-k. A közzétett LUIS alkalmazás eléréséhez a lekérdezés [végpont](https://aka.ms/luis-endpoint-apis). 

## <a name="what-technologies-work-with-luis"></a>Milyen technikai megoldásokat LUIS használható?
Több Microsoft-technológiák LUIS használata:

* [Bing helyesírás ellenőrizze API] [ bing-spell-check-api] szöveg javítása előtt előrejelzés biztosít. 
* [Botot keretrendszer] [ bot-framework] lehetővé teszi, hogy egy chatbot, hogy egy felhasználó a szöveges bevitel forduljon. Válassza ki [3.x](https://github.com/Microsoft/BotBuilder) vagy [4.x](https://github.com/Microsoft/botbuilder-dotnet) SDK teljes botot élmény.
* [Kérdések és válaszok készítő] [ qnamaker] lehetővé teszi, hogy számos különböző típusú szöveg egy kérdés és válasz Tudásbázis egységgé kombinálják.
* [Beszéd] [ speech] szóbeli nyelvi kérelmek szöveggé alakítja. Ha szöveg konvertálva, LUIS kérelmeket dolgozza fel. Lásd: [beszéd SDK](https://aka.ms/csspeech) további információt.
* [Szövegelemzések] [ text-analytics] véleményeket elemzést, a kulcs kifejezés adatok kinyerése biztosít.

## <a name="next-steps"></a>További lépések
Hozzon létre egy [új LUIS app](LUIS-get-started-create-app.md).

<!-- Reference-style links -->
[create-app]:luis-get-started-create-app.md
[azure-portal]:https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account
[publish-app]:PublishApp.md#test-your-published-endpoint-in-a-browser
[luis-concept-entity-types]:luis-concept-entity-types.md
[add-example-utterances]: luis-how-to-add-example-utterances.md
[prebuilt-entities]: pre-builtentities.md
[prebuilt-domains]: luis-how-to-use-prebuilt-domains.md
[label-suggested-utterances]: label-suggested-utterances.md
[intro-video]:https://aka.ms/LUIS-Intro-Video
[bot-framework]:https://docs.microsoft.com/bot-framework/
[speech]:../Speech/index.md
[flow]:https://docs.microsoft.com/connectors/luis/
[entity-concept]:luis-concept-entity-types.md
[add-intents]:luis-how-to-add-intents.md
[add-entities]:luis-how-to-add-entities.md
[authoring-apis]:https://aka.ms/luis-authoring-api
[endpoint-apis]:https://aka.ms/luis-endpoint-apis
[LUIS]:luis-reference-regions.md
[text-analytics]:https://azure.microsoft.com/services/cognitive-services/text-analytics/
[patterns]:luis-concept-patterns.md
[bing-spell-check-api]:https://azure.microsoft.com/services/cognitive-services/spell-check/
[qnamaker]:https://qnamaker.ai/