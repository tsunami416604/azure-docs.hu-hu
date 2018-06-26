---
title: Hamarosan nyelvi ismertetése (LUIS) az Azure-ban |} Microsoft Docs
description: Megtudhatja, hogyan nyelvi ismertetése (LUIS) segítségével állítsa a gépi tanulási az alkalmazások teljesítménye.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/22/2017
ms.author: v-geberr
ms.openlocfilehash: bbd0a532e54f9b221739c8ae9ff097fe44fdc4df
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751595"
---
# <a name="what-is-language-understanding-luis"></a>Mi az a nyelvi ismertetése (LUIS)?
Nyelvi ismertetése (LUIS) egy felhőalapú szolgáltatás, amely egyéni gépi tanulásra vonatkozik egy felhasználói conversational, természetes nyelvű szövege előrejelzése általános jelentését, és ki a megfelelő, részletes információkat. 

Egy ügyfél-alkalmazást LUIS lehet bármely conversational alkalmazás, amely kommunikál a felhasználók természetes nyelven a tevékenységek végrehajtásához. Ügyfélalkalmazások például a közösségi média alkalmazások chatbots és asztali alkalmazások engedélyezését.  

![Információ LUIS etetési 3 alkalmazások fogalmi képe](./media/luis-overview/luis-entry-point.png)

## <a name="what-is-a-luis-app"></a>Mi az, hogy egy LUIS alkalmazást?
Egy LUIS alakítson ki az tartományspecifikus természetes nyelvű modell tartalmaz. Indítsa el a LUIS alkalmazást egy előre elkészített tartomány modellel, összeállítása a saját vagy egyesítés egy előre elkészített tartomány adatot a saját egyéni információ.

[Előre elkészített tartomány modellek](luis-how-to-use-prebuilt-domains.md) összes adatot tartalmaznak, és indíthatja gyorsan LUIS nagyszerű módját.

A LUIS is tartalmaz integrációs beállítások [közreműködők](luis-concept-collaborator.md), és [verziók](luis-concept-version.md).

## <a name="using-a-luis-app"></a>LUIS alkalmazással
<a name="Accessing-LUIS"></a> Miután közzétette az LUIS alkalmazást, az ügyfélalkalmazás utterances elküldi a LUIS [végpont API] [ endpoint-apis] és az előrejelzés eredmények JSON-választ kap.

Az alábbi diagram szemlélteti, először az ügyfél chatbot felhasználó szöveget küld a mi személy szeretne rendelni a HTTP-kérelem a LUIS saját szavakat. LUIS második, a megjegyzett modell vonatkozik a felhasználói bevitel értelmezhető természetes nyelv, és a JavaScript Object Notation (JSON) formátumú választ ad vissza. Az ügyfél chatbot harmadik, a JSON-választ használ a felhasználói kérelmek teljesítése érdekében kapcsolódott. 

![Fogalmi képeket a LUIS Chatbot használata](./media/luis-overview/luis-overview-process-2.png)

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
<a name="what-is-a-luis-model"></a>
## <a name="what-is-a-natural-language-model"></a>Mi az a természetes nyelvű modell?
A modellek listáját általános felhasználói céljaira nevű kezdődik _leképezések_, például "Könyv repülési" vagy "Forduljon támogatási szolgálat." Megadja a felhasználó Példaszöveg nevű _példa utterances_ a leképezések az. Ezután jelölje meg jelentős szavakat vagy kifejezéseket a utterance hívása a _entitások_.


A modell az alábbiak tartalmazza:

* **[leképezések](#intents)**: felhasználói céljaira (tervezett művelet vagy eredmény) kategóriái
* **[entitások](#entities)**: adatok utterances száma, e-mailek vagy neve például adott típusú
* **[Példa utterances](#example-utterances)**: a felhasználó adja meg az ügyfélalkalmazásban mintaszöveggel

### <a name="intents"></a>Leképezések 
Egy [leképezés](luis-how-to-add-intents.md), rövid a _szándékát_célú vagy a cél a kifejezett egy felhasználó utterance, például a felhőszolgáltató közötti átviteléhez foglalás, egy számlázási fizető vagy a hírek cikkében találja. Minden egyes művelethez megjelölésű hoz létre. Egy LUIS utazás app határozhatnak meg megjelölésű nevű "BookFlight." Az ügyfélalkalmazás használható leképezés pontozási felső elindítani egy műveletet. Például "BookFlight" leképezés LUIS küld vissza, ha az ügyfélalkalmazást elindíthatja az API-hívás a Foglalás vezérlősík jegy egy külső szolgáltatáshoz.

### <a name="entities"></a>Entitások
Egy [entitás](luis-how-to-add-entities.md) fontos a felhasználó kérelmére a utterance belül található részletes adatait jelöli. Például a "Könyv Párizsi jegy" utterance egyetlen jegy van szükség, és "Párizsi" egy helyre. Két olyan entitásra "jegyet" egy "Párizsi", amely jelzi, a cél és egyetlen jegyet találhatók. 

Miután LUIS a található a felhasználó utterance az entitásokat ad vissza, az ügyfélalkalmazás használható entitások listájának paraméterek elindítani egy műveletet. Például entitások, például a utazás cél, dátum és légitársaság foglalási a felhőszolgáltató közötti átviteléhez szükséges.

LUIS többféleképpen is azonosíthatja és entitások kategorizálását.

* **Előre elkészített entitások** LUIS rendelkezik leképezések, utterances, többek között számos előre elkészített tartomány modellek és [előre elkészített entitások](pre-builtentities.md). Az előre elkészített entitásokat is használhatja a leképezések és az előre elkészített modell utterances használata nélkül. Az előre elkészített entitások idő takarítható meg.

* **Egyéni entitások** LUIS számos lehetőséget biztosít a saját egyéni azonosításához [entitások](luis-concept-entity-types.md) entitások gép megtanulta, adott vagy -konstans entitásokat és számítógép-megtanulta, és a szöveges.

### <a name="example-utterances"></a>Példa utterances
Példa [utterance](luis-how-to-add-example-utterances.md) szöveges bevitel a felhasználótól, amelyet az ügyfélalkalmazás ismertetése. Lehet, hogy néhány mondatot, például az "A jegy könyv Párizsba", illetve egy kódrészletet néhány mondatot, például a "Foglalási" vagy "Párizsi repülési." Utterances mindig nem megfelelően formázott, és az egy adott célt számos utterance változata is lehet. 10-20 példa utterances Helyezzen minden leképezés, és jelölje minden utterance szerepelnek.

|Példa felhasználói utterance|Kísérlet történt|Entitások|
|-----------|-----------|-----------|
|"És a felhőszolgáltató közötti átviteléhez foglalható le __Budapest__?"|BookFlight|Seattle|
|"Ha does tárolt __nyissa meg a__?"|StoreHoursAndLocation|megnyitás|
|"A értekezletet __1 pm__ rendelkező __Bob__ a terjesztési"|ScheduleMeeting|Bob du. 1|

## <a name="improve-prediction-accuracy"></a>Előrejelzési pontosság növeléséhez
A LUIS alkalmazás közzé van téve, és megkapja a tényleges felhasználói utterances, után LUIS az előrejelzési pontosság növeléséhez számos módszert kínál a: [aktív tanulási](#active-learning) a végpont utterances [listák kifejezés](#phrase-lists) tartomány Word-felvételt és [minták](#patterns) szükséges utterances számának csökkentése érdekében.

### <a name="active-learning"></a>Aktív tanulás
Az a [aktív tanulási](label-suggested-utterances.md) folyamat LUIS lehetővé teszi a valós utterances LUIS alkalmazást támogató kapta meg a végpont felülvizsgálandó utterances kiválasztásával. Fogadja el, vagy javítsa ki a végpont előrejelzés teljesített kapcsolat-újraépítési és ismételt. LUIS Tanulja meg gyorsan iteratív ezt a folyamatot, a minimális mérete az idő és erőfeszítés véve. 

### <a name="phrase-lists"></a>Kifejezés listája 
LUIS biztosít [listák kifejezések](luis-concept-feature.md) úgy adhatja meg, fontos szavakat vagy kifejezéseket a modell-tartományban. LUIS a listák segítségével adja hozzá a további többszörösére szavak és kifejezések, amelyek akkor ellenkező esetben nem található a modellben.

### <a name="patterns"></a>Minták 
Minták lehetővé teszik egy leképezés utterance gyűjteményét közös egyszerűsítése [sablonok](luis-concept-patterns.md) word választott és word sorrendje. Ez lehetővé teszi további gyorsabb által kevesebb példa utterances kellene a leképezések az LUIS. Minták egy hibrid rendszer reguláris kifejezések és a gép megtanulta kifejezések. 

<a name="using-luis"></a>

## <a name="authoring-and-accessing-luis"></a>Jelentéskészítő és -LUIS elérése
Hozza létre a LUIS alkalmazását a LUIS webhelyről vagy a programozott módon a [szerzői](https://aka.ms/luis-authoring-apis) API-k, vagy használja mindkét attól függően, hogy a szerzői műveletekhez szükséges. A közzétett LUIS alkalmazás eléréséhez a lekérdezés [végpont](https://aka.ms/luis-endpoint-apis). 

LUIS világszerte, attól függően, hogy a szerzői műveletek terület három webhelyek biztosít. A szerzői műveletek terület meghatározza, hogy az Azure-régió, ahol az LUIS alkalmazás közzététele.
<!--
|Authoring region|Publishing region(s)|
|--|--|
|[www.luis.ai](https://www.luis.ai)|**U.S.**<br>West US<br>West US 2<br>East US<br>East US 2<br>South Central US<br>West Central US<br><br>**Asia**<br>Southeast Asia<br>East Asia<br><br>**South America**<br>Brazil South |
|[au.luis.ai](https://au.luis.ai)|Australia East|
|[eu.luis.ai](https://eu.luis.ai)|West Europe<br>North Europe|
-->

Ismerje meg, [további](luis-reference-regions.md) szerzői, és régiók közzététele.

## <a name="what-technologies-work-with-luis"></a>Milyen technikai megoldásokat LUIS használható?
Több Microsoft-technológiák LUIS használata:

* [Bing helyesírás ellenőrizze API](../bing-spell-check/proof-text.md) szöveg javítása előtt előrejelzés biztosít. 
* [Botot keretrendszer] [ bot-framework] lehetővé teszi, hogy egy chatbot, hogy egy felhasználó a szöveges bevitel forduljon. Válassza ki [3.x](https://github.com/Microsoft/BotBuilder) vagy [4.x](https://github.com/Microsoft/botbuilder-dotnet) SDK teljes botot élmény.
* [Kérdések és válaszok készítő] [ qnamaker] lehetővé teszi, hogy számos különböző típusú szöveg egy kérdés és válasz Tudásbázis egységgé kombinálják.
* [Beszéd](../Speech/home.md) szóbeli nyelvi kérelmek szöveggé alakítja. Ha szöveg konvertálva, LUIS kérelmeket dolgozza fel. Lásd: [beszéd SDK](https://aka.ms/csspeech) további információt.
* [Szövegelemzések](../text-analytics/overview.md) véleményeket elemzést, a kulcs kifejezés adatok kinyerése biztosít.

## <a name="next-steps"></a>További lépések
Hozzon létre egy új LUIS alkalmazást egy [előre elkészített](luis-get-started-create-app.md) vagy [egyéni](luis-quickstart-intents-only.md) tartomány.

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/