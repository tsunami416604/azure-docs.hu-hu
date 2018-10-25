---
title: Mi az a Language Understanding (LUIS) – Azure Cognitive Services | Microsoft Docs
description: A Language Understanding (LUIS) egy olyan felhőalapú API-szolgáltatás, amely egyéni gépi tanulási intelligenciát használ a felhasználó párbeszédszerű, természetes nyelvi szövege általános jelentésének megállapítására, valamint a releváns, részletes információk kinyerésére. A LUIS ügyfélalkalmazása lehet bármely beszélgetési alkalmazás, amely a felhasználóval természetes nyelven kommunikál egy adott feladat végrehajtásához. Az ügyfélalkalmazások közé sorolhatók például a közösségimédia-alkalmazások, a csevegőrobotok és a beszédalapú asztali alkalmazások.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: overview
ms.date: 10/06/2018
ms.author: diberry
ms.openlocfilehash: 28580a29c2ffaadfa3b3ea26cb28f103d883d576
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2018
ms.locfileid: "49637272"
---
# <a name="what-is-language-understanding-luis"></a>Mi a Language Understanding (LUIS)?

A Language Understanding (LUIS) egy olyan felhőalapú API-szolgáltatás, amely egyéni gépi tanulási intelligenciát használ a felhasználó párbeszédszerű, természetes nyelvi szövege általános jelentésének megállapítására, valamint a releváns, részletes információk kinyerésére. 

A LUIS ügyfélalkalmazása lehet bármely beszélgetési alkalmazás, amely a felhasználóval természetes nyelven kommunikál egy adott feladat végrehajtásához. Az ügyfélalkalmazások közé sorolhatók például a közösségimédia-alkalmazások, a csevegőrobotok és a beszédalapú asztali alkalmazások.  

![A Cognitive Services Language Understanding (LUIS) szolgáltatással együttműködő 3 kliensalkalmazás koncepcióképe](./media/luis-overview/luis-entry-point.png "A Cognitive Services Language Understanding (LUIS) szolgáltatással együttműködő 3 kliensalkalmazás koncepcióképe")

## <a name="use-luis-in-a-chat-bot"></a>A LUIS használata csevegőrobotban

<a name="Accessing-LUIS"></a>

A LUIS alkalmazás közzététele után az ügyfélalkalmazás kimondott szöveget (szöveg) küld a LUIS természetes nyelvi feldolgozási végpont [API-jának][endpoint-apis], és az eredményeket JSON-válaszok formájában kapja meg. A csevegőrobotok a LUIS gyakori ügyfélalkalmazásai.


![Koncepciókép: csevegőrobottal együttműködő LUIS a felhasználói szöveg természetesnyelv-feldolgozással (NLP) történő megjósolásához](./media/luis-overview/luis-overview-process-2.png "Koncepciókép: csevegőrobottal együttműködő LUIS a felhasználói szöveg természetesnyelv-feldolgozással (NLP) történő megjósolásához")

|Lépés|Műveletek|
|:--|:--|
|1|Az ügyfélalkalmazás a következő felhasználói _kimondott szöveget_ (saját szavaikkal megfogalmazott szöveg) küldi el HTTP-kérésként a LUIS-végpontnak: „Fel szeretném hívni a HR képviselőmet.”|
|2|A LUIS alkalmazza a tanult modellt a természetes nyelvű szövegre a felhasználói bemenet intelligens feldolgozása végett. A LUIS JSON-formátumú választ ad vissza a „HRContact” legfelső szándékkal. A JSON-végpont minimális válasza tartalmazza a lekérdezési kimondott szöveget, valamint a legmagasabb pontszámú szándékot. Az olyan adatokat is képes kinyerni, mint a Kapcsolat típusa entitás.|
|3|Az ügyfélalkalmazás a JSON-választ használatával dönti el, hogy hogyan teljesítse a felhasználói kérést. Ezek a döntések a robot keretrendszerében lévő döntési fákat és más szolgáltatások felé irányuló hívásokat tartalmazhatnak. |

A LUIS alkalmazás biztosítja az intelligenciát, így az ügyfélalkalmazás is intelligens döntéseket hozhat. A döntéseket nem a LUIS biztosítja. 

<!--

### Example of JSON endpoint response

The minimum JSON endpoint response contains the query utterance, and the top scoring intent. It can also extract data such as the following **Contact Type** entity. 

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
-->

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Természetes nyelvek feldolgozása

A LUIS alkalmazás tartományspecifikus természetes nyelvi modellt tartalmaz. A LUIS alkalmazás elindításához használhat előre összeállított tartománymodellt, elkészítheti a saját modelljét vagy kombinálhatja egy előre elkészített tartomány elemeit saját egyéni adataival.

* **Előre összeállított modell** A LUIS számos előre összeállított, szándékokat, kimondott szövegeket és előre létrehozott entitásokat tartalmazó tartománymodellel rendelkezik. Az előre létrehozott entitásokat a szándékok és kimondott szövegek használata nélkül is alkalmazhatja. Az [Előre összeállított tartománymodellek](luis-how-to-use-prebuilt-domains.md) tartalmazzák a teljes dizájnt, így nagyszerű módot kínálnak arra, hogy a LUIS használatát gyorsan elkezdhesse.

* **Egyéni entitások** A LUIS több módot kínál Önnek arra, hogy azonosítsa saját egyéni szándékait és entitásait, beleértve a gép által tanult entitásokat, a konkrét vagy literálentitásokat valamint a gép által tanult és literálentitások kombinációit is.

## <a name="build-the-luis-model"></a>A LUIS-modell létrehozása
A modell létrehozása a [szerkesztési](https://aka.ms/luis-authoring-apis) API-kkal vagy a LUIS-portállal lehetséges.

A LUIS-modell a **[szándékok](luis-concept-intent.md)** elnevezésű felhasználói szándékok kategóriáival kezdődik. Minden egyes szándéknak szüksége van felhasználói **[kimondott szövegek](luis-concept-utterance.md)** példáira. Minden egyes kimondott szöveg tartalmazhat számos különféle adatot, amelyeket **[entitásokkal](luis-concept-entity-types.md)** kell kinyerni. 

|Példa felhasználói kimondott szövegre|Szándék|Entitások|
|-----------|-----------|-----------|
|„Foglalj repülőjegyet __Seattle-be__.”|RepülőjegyFoglalás|Seattle|
|„Mikor __nyit ki__ az üzlet?”|ÜzletNyitvatartásÉsHelyszín|nyitás|
|„Ütemezz egy értekezletet __13 órára__ __Bobbal__ a terjesztési osztályról.”|ÉrtekezletÜtemezés|13 óra, Bob|

<!--
## What is a natural language model?

A model begins with a list of general user intentions, called _intents_, such as "Book Flight" or "Contact Help Desk." You provide user's example text, called _example utterances_ for the intents. Then mark significant words or phrases in the utterance, called _entities_.


A model includes:

* **[intents](#intents)**: categories of user intentions (overall intended action or result)
* **[entities](#entities)**: specific types of data in utterances such as number, email, or name contained in text
* **[example utterances](#example-utterances)**: example text a user might enter in the client application

### Intents 

An [intent](luis-how-to-add-intents.md), short for _intention_, is a purpose or goal expressed in a user's utterance, such as booking a flight, paying a bill, or finding a news article. You create an intent for each action. A LUIS travel app may define an intent named "BookFlight." Each prediction query includes the top scored intent. 

The client application can use the top scoring intent to trigger an action. For example, when "BookFlight" intent is returned from LUIS, a client application could trigger an API call to an external service for booking a plane ticket.

### Entities

An [entity](luis-how-to-add-entities.md) represents detailed information found within the utterance that is relevant to the user's request. For example, in the utterance "Book a ticket to Paris",  a single ticket is requested, and "Paris" is a location. Two entities are found "a ticket" indicating a single ticket and "Paris" indicating the destination. 

After LUIS returns the entities found in the user’s utterance, the client application can use the list of entities as parameters to trigger an action. For example, booking a flight requires entities like the travel destination, date, and airline.
-->
<!--
### Example utterances

An example [utterance](luis-how-to-add-example-utterances.md) is text input from the user that the client application needs to understand. It may be a sentence, like "Book a ticket to Paris", or a fragment of a sentence, like "Booking" or "Paris flight." Utterances aren't always well-formed, and there can be many utterance variations for a particular intent. Add 10 to 20 example utterances to each intent and mark entities in every utterance.

|Example user utterance|Intent|Entities|
|-----------|-----------|-----------|
|"Book a flight to __Seattle__?"|BookFlight|Seattle|
|"When does your store __open__?"|StoreHoursAndLocation|open|
|"Schedule a meeting at __1pm__ with __Bob__ in Distribution"|ScheduleMeeting|1pm, Bob|
-->
## <a name="query-prediction-endpoint"></a>Előrejelzési végpont lekérdezése

A modell létrehozása és végponton való közzététele után az ügyfélalkalmazás kimondott szövegeket küld a közzétett előrejelzési [végpont](https://aka.ms/luis-endpoint-apis) API-jának. Az API alkalmazza a modellt a szövegre elemzés céljából. Az API JSON-formátumú válaszban adja vissza az előrejelzés eredményeit.  

A JSON-végpont minimális válasza tartalmazza a lekérdezési kimondott szöveget, valamint a legmagasabb pontszámú szándékot. Az olyan adatokat is képes kinyerni, mint a következő **Kapcsolat típusa** entitás. 

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

## <a name="improve-model-prediction"></a>Modell-előrejelzés javítása

Miután egy LUIS-modell a közzététele után valós felhasználói kimondott szövegeket fogad, a LUIS több módszert is biztosít az előrejelzés pontosításához: végponti kimondott szövegek [aktív tanulása](luis-concept-review-endpoint-utterances.md), [kifejezéslisták](luis-concept-feature.md) a tartomány szavainak belefoglalása érdekében, valamint [minták](luis-concept-patterns.md) a szükséges kimondott szövegek számának csökkentésére.
<!--
### Active learning

In the [active learning](luis-how-to-review-endoint-utt.md) process, LUIS allows you to adapt the LUIS app to real-world utterances by selecting utterances it received at the endpoint for your review. You can accept or correct the endpoint prediction, retrain, and republish. LUIS learns quickly with this iterative process, taking the minimum amount of your time and effort. 

### Phrase lists 

LUIS provides [phrases lists](luis-concept-feature.md) so you can indicate important words or phrases of the model. LUIS uses these lists to add additional significance to those words and phrases that would otherwise not be found in the model.

### Patterns 

Patterns allow you to simplify an intent's utterance collection into common [templates](luis-concept-patterns.md) of word choice and word order. This allows LUIS to learn quicker by needing fewer example utterances for the intents. Patterns are a hybrid system of regular expressions and machine-learned expressions. 
-->
<a name="using-luis"></a>
<!--
## Authoring and accessing models
Author LUIS from the [authoring](https://aka.ms/luis-authoring-apis) APIs or from the LUIS portal. Query the published prediction endpoint of the model from the [endpoint](https://aka.ms/luis-endpoint-apis) APIs.
-->

## <a name="development-lifecycle"></a>Fejlesztési életciklus
A LUIS eszközöket és verziókezelést, valamint a többi LUIS-szerzővel való együttműködés lehetőségét biztosítva teszi lehetővé az integrációt a teljes fejlesztési ügyfélciklusban az ügyfélalkalmazás és a nyelvi modell szintjén. 

## <a name="implementing-luis"></a>A LUIS implementálása
Mivel a LUIS egy REST API, bármely olyan termékkel, szolgáltatással vagy keretrendszerrel használható, amely HTTP-kéréseket hoz létre. Az alábbi lista tartalmazza a főbb, LUIS szolgáltatással használható Microsoft-termékeket és -szolgáltatásokat.

A LUIS leggyakoribb ügyfélalkalmazása:
* A [Web app bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-3.0) gyorsan létrehoz egy LUIS-kompatibilis csevegőrobotot, amely szövegbevitel útján kommunikál a felhasználóval. A [Bot Framework][bot-framework] [3.x](https://github.com/Microsoft/BotBuilder) vagy [4.x](https://github.com/Microsoft/botbuilder-dotnet) verzióját használja a teljes körű, robotokkal kapcsolatos szolgáltatások biztosításához.

Eszközök a LUIS robotokkal való gyors és egyszerű használatához:
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) – Az NPM-csomag tartalomkészítési és előrejelzési funkciókat biztosít különálló parancssori eszközként vagy importált tartalmakként. 
* A [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) eszközzel szigorú típusmegadású C#- és TypeScript-forráskód hozható létre exportált LUIS-modellekből.
* A [Dispatch](https://aka.ms/dispatch-tool) többféle LUIS- és QnA Maker-alkalmazás számára is lehetővé teszi, hogy elérhetők legyenek egy Dispatcher-modellt használó szülőalkalmazásból.
* A [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) a robotok nyelvi modelljeinek kezelését egyszerűsítő parancssori eszköz.

További, a LUIS szolgáltatással használható Cognitive Services szolgáltatások:
* A [QnA Maker][qnamaker] különböző típusú szövegek kombinálásával teszi lehetővé egy kérdéseken és válaszokon alapuló tudásbázis kialakítását.
* A [Bing Spell Check API](../bing-spell-check/proof-text.md) az előrejelzés előtt lehetővé teszi a szöveg javítását. 
* A [Speech service](../Speech-Service/overview.md) szöveggé alakítja a szóbeli kéréseket. 
* A [Conversation learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) robotbeszélgetések gyorsabb létrehozását teszi lehetővé a LUIS szolgáltatással.
* A [Project personality chat](https://docs.microsoft.com/azure/cognitive-services/project-personality-chat/overview) a robotokkal való társalgást kezeli.

## <a name="next-steps"></a>További lépések

Hozzon létre új LUIS alkalmazást [előre összeállított](luis-get-started-create-app.md) vagy [egyéni](luis-quickstart-intents-only.md) tartománnyal. [Kérdezze le egy nyilvános IoT-alkalmazás előrejelzési végpontját](luis-get-started-cs-get-intent.md).

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/
