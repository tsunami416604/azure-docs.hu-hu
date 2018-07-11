---
title: Hamarosan Language Understanding (LUIS) az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan ruházhatja fel alkalmazásait gépi a Language Understanding (LUIS) használatával.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/22/2017
ms.author: v-geberr
ms.openlocfilehash: 1c68c586fb799a540f70804d181aa66b2bda9e97
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952538"
---
# <a name="what-is-language-understanding-luis"></a>Mit jelent a Language Understanding (LUIS)?
Language Understanding (LUIS) egy felhőalapú szolgáltatás, amely egyéni gépi tanulási vonatkozik a felhasználó természetes nyelvi, természetes nyelvi szöveg teljes jelentése találnia, és ki a megfelelő, részletes információkat. 

A LUIS ügyféloldali alkalmazás bármilyen természetes nyelvi alkalmazások lehetnek, amely kommunikál egy felhasználóval természetes nyelven egy adott feladat végrehajtásához. Ügyfélalkalmazások közé közösségimédia-alkalmazások, látás és asztali alkalmazások engedélyezését.  

![Információ a LUIS számból 3 alkalmazások fogalmi képe](./media/luis-overview/luis-entry-point.png)

## <a name="what-is-a-luis-app"></a>Mit jelent a LUIS-alkalmazások?
LUIS-alkalmazások tervezésekor a tartomány-specifikus természetes nyelvi modell tartalmazza. A LUIS-alkalmazás elindításához az előre összeállított szakterületi modell, létrehozhatja saját vagy egy előre elkészített tartomány darab kombinálhatja a saját egyéni.

[Előre összeállított tartománymodellek](luis-how-to-use-prebuilt-domains.md) közé tartozik a összes megtalálhatja az Ön számára és a LUIS használatának gyors megkezdéséhez nagyszerű lehetőséget.

A LUIS-alkalmazás is tartalmaz az integrációs beállításokat, [közreműködők](luis-concept-collaborator.md), és [verziók](luis-concept-version.md).

## <a name="using-a-luis-app"></a>A LUIS-alkalmazás használatával
<a name="Accessing-LUIS"></a> A LUIS-alkalmazás közzététele után az ügyfélalkalmazás utterances küld a LUIS [végponti API] [ endpoint-apis] és fogadja az előrejelzés eredményét, JSON-válaszok.

Az alábbi ábrán az ügyfél csevegőrobot elküldi az ember szándékának megértésére a saját szavaival, LUIS HTTP-kérést a felhasználó szöveget. Második LUIS, a felhasználói bevitel érthető legyen természetes nyelv a megismert modell vonatkozik, és egy JavaScript Object Notation (JSON) formátumú választ ad vissza. Az ügyfél csevegőrobot harmadik, a JSON-válasz használ a felhasználói kérések teljesítését. 

![A LUIS-Csevegőrobot használata fogalmi képeken](./media/luis-overview/luis-overview-process-2.png)

### <a name="example-of-json-endpoint-response"></a>JSON-végpont válasz – példa

A JSON végpont választ, legalább a lekérdezés utterance (kifejezés), valamint a leképezés pontozási felső tartalmazza. 

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
## <a name="what-is-a-natural-language-model"></a>Mi az természetes nyelvi modell?
A modell egy általános felhasználói céljaira, úgynevezett listája kezdődik _leképezések_, például "Címjegyzék repülési" vagy "Forduljon támogatási szolgálat." Azt adja meg a felhasználó látható példaszöveget, nevű _példa utterances_ a szándék fog vonatkozni a. Megjelölheti a jelentős szavak vagy kifejezések az utterance (kifejezés), nevű _entitások_.


A modell tartalmazza:

* **[leképezések](#intents)**: felhasználói céljaira (tervezett művelet vagy eredmény) kategóriái
* **[entitások](#entities)**: adott típusú adatok például a számot, e-mail vagy név kimondott szöveg
* **[Példa utterances](#example-utterances)**: a felhasználó beírja az ügyfélalkalmazásban mintaszöveggel

### <a name="intents"></a>Leképezések 
Egy [szándékot](luis-how-to-add-intents.md), rövid a _blokkolni_, a cél, vagy ki, a cél a felhasználó utterance (kifejezés), például a Foglalás repülőjegyet, és a egy számlázási vagy hír keresése. Minden egyes művelethez megjelölésű hoz létre. A LUIS utazási alkalmazás adhat meg megjelölésű nevű, "BookFlight." Az ügyfélalkalmazás szándékot pontozási felső használhatja egy műveletet aktiválásához. Például "BookFlight" szándékot küld vissza, LUIS, ha az ügyfélalkalmazás válthatja ki egy API-hívások egy külső szolgáltatás lefoglalási adatsík jegyet.

### <a name="entities"></a>Entitások
Egy [entitás](luis-how-to-add-entities.md) az utterance (kifejezés), amely a felhasználó kérésére fontos található részletes adatait jelöli. Például az "Egy a jegy Párizsba könyv" utterance egy hibajegy van szükség, és a "Párizs" az a hely. A két entitás "egy a jegy" jelző egy hibajegy és a cél jelző "Párizs" találhatók. 

A LUIS a található az a felhasználó utterance (kifejezés) entitásokat ad vissza, miután az ügyfélalkalmazás segítségével az entitások listájában paraméterekként műveletek aktiválására. Például entitások, például az úti cél, dátum és légitársaság foglalási repülőjegyet igényel.

A LUIS azonosíthatja és entitások kategorizálása több módszert is biztosít.

* **Előre összeállított entitások** LUIS rendelkezik szándék fog vonatkozni, utterances, beleértve számos előre elkészített tartománymodellek és [előre összeállított entitások](luis-prebuilt-entities.md). A leképezések és a kimondott szöveg az előre létrehozott modell használata nélkül is használhatja az előre összeállított entitások. Az előre összeállított entitások időt takarít meg.

* **Az egyéni entitások** LUIS számos lehetőséget biztosít a saját egyéni azonosításához [entitások](luis-concept-entity-types.md) többek között a gép megismert entitások, az adott vagy -konstans entitások és a gép megtanult és szövegkonstans.

### <a name="example-utterances"></a>Példák kimondott szövegekre
Példa [utterance (kifejezés)](luis-how-to-add-example-utterances.md) van a felhasználót, hogy az ügyfélalkalmazás meg kell ismernie a szövegbevitel. Elképzelhető, hogy egy mondatot, például "Book egy jegyet, Párizs", vagy egy kódrészletet egy mondatot, például a "Foglalás" vagy "Párizs repülési." Beszédmódok nem mindig helytelen formátumú, és számos utterance (kifejezés) változata létezik egy adott szándékot is lehet. Minden egyes beszédszándék 10 és 20 példa beszédmódok hozzáadása, és jelölje meg minden utterance (kifejezés) entitásokat.

|Példa felhasználói utterance (kifejezés)|Leképezés|Entitások|
|-----------|-----------|-----------|
|"A repülőjegyet repülőjáratra __Seattle__?"|BookFlight|Seattle|
|"Ha does a store __nyissa meg a__?"|StoreHoursAndLocation|megnyitás|
|"Értekezletet, __1 du__ a __Bob__ terjesztés"|ScheduleMeeting|1 du, Bob|

## <a name="improve-prediction-accuracy"></a>Előrejelzés pontosságának javítása
A LUIS-alkalmazás közzé van téve, és a valós felhasználói utterances kap, miután a LUIS növelve előrejelzés pontosságát több módszert is biztosít: [aktív tanulás](#active-learning) a végpont utterances, [listák kifejezés](#phrase-lists) tartomány a felvételt a Word és [minták](#patterns) szükséges utterances számának csökkentése érdekében.

### <a name="active-learning"></a>Aktív tanulás
Az a [aktív tanulás](luis-how-to-review-endoint-utt.md) folyamat, LUIS lehetővé teszi, hogy a LUIS-alkalmazás a való életből vett utterances alkalmazkodnak a végponton felülvizsgálandó kapott utterances kiválasztásával. Fogadja el, vagy javítsa ki a végpont-előrejelzés, teljesített kapcsolat-újraépítési és újra közzétettük. A LUIS megtanulja gyorsan a iteratív folyamattal minimális mennyisége az idő és erőfeszítés véve. 

### <a name="phrase-lists"></a>Kifejezéslisták 
A LUIS biztosít [listák kifejezések](luis-concept-feature.md) úgy adhatja meg, fontos szavakat vagy kifejezéseket modell tartományához. A LUIS ezek a listák segítségével további többszörösére hozzá ezekhez a szavak és kifejezések, amelyek lenne egyébként nem található a modellben.

### <a name="patterns"></a>Minták 
Minták lehetővé teszik egy leképezés utterance (kifejezés) gyűjteményét közös egyszerűsítése [sablonok](luis-concept-patterns.md) word választási lehetőség és szórendjét. Ez lehetővé teszi a LUIS további gyorsabb kevesebb példa utterances kellene a a szándék szerint. Minták reguláris kifejezések és a gép megtanult kifejezések hibrid rendszerek. 

<a name="using-luis"></a>

## <a name="authoring-and-accessing-luis"></a>Szerzői műveletek, és a LUIS elérése
A LUIS webhelyről vagy programozott módon a LUIS alkalmazás létrehozása a [szerzői](https://aka.ms/luis-authoring-apis) API-k, vagy használja mindkét attól függően, a szerzői műveletekhez részben szükség. A lekérdezés által a közzétett LUIS-alkalmazás elérésére [végpont](https://aka.ms/luis-endpoint-apis). 

A LUIS három websites, a szerzői műveletekhez részben régiójától függően a világ különböző pontjain biztosít. Az Authoring Tool régió határozza meg, ahol közzéteheti a LUIS-alkalmazás Azure-régióban.
<!--
|Authoring region|Publishing region(s)|
|--|--|
|[www.luis.ai](https://www.luis.ai)|**U.S.**<br>West US<br>West US 2<br>East US<br>East US 2<br>South Central US<br>West Central US<br><br>**Asia**<br>Southeast Asia<br>East Asia<br><br>**South America**<br>Brazil South |
|[au.luis.ai](https://au.luis.ai)|Australia East|
|[eu.luis.ai](https://eu.luis.ai)|West Europe<br>North Europe|
-->

Ismerje meg, [további](luis-reference-regions.md) szerzői, és régiók közzététele.

## <a name="what-technologies-work-with-luis"></a>Mi a technológiák együttműködnek az intelligens hangfelismerési szolgáltatással?
Számos Microsoft-technológiák használata, az intelligens hangfelismerési szolgáltatással:

* [A Bing Spell Check API](../bing-spell-check/proof-text.md) szöveg javítása előtt előrejelzési biztosít. 
* [Bot Framework] [ bot-framework] lehetővé teszi, hogy egy csevegőrobotot szeretne beszélni a szövegbevitel felhasználók. Válassza ki [3.x](https://github.com/Microsoft/BotBuilder) vagy [4.x](https://github.com/Microsoft/botbuilder-dotnet) SDK teljes bot biztosítása érdekében.
* [A QnA Maker] [ qnamaker] lehetővé teszi, hogy a szöveg egy kérdést és választ Tudásbázis egyesítendő számos különböző típusú.
* [Beszéd](../Speech/home.md) kérelmek használja a beszélt nyelv szöveggé. Ha szöveges konvertálva, LUIS dolgozza fel a kérelmeket. Lásd: [beszéd SDK](https://aka.ms/csspeech) további információt.
* [Szövegelemzés](../text-analytics/overview.md) vélemények elemzése és adatok kulcskifejezések biztosít.

## <a name="next-steps"></a>További lépések
Az új LUIS-alkalmazás létrehozása egy [előre összeállított](luis-get-started-create-app.md) vagy [egyéni](luis-quickstart-intents-only.md) tartományhoz.

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/