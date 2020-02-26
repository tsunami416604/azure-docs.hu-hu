---
title: Mi a Language Understanding (LUIS)?
description: A Language Understanding (LUIS) egy olyan felhőalapú API-szolgáltatás, amely egyéni gépi tanulási intelligenciát használ a felhasználó párbeszédszerű, természetes nyelvi szövege általános jelentésének megállapítására, valamint a releváns, részletes információk kinyerésére.
ms.topic: overview
ms.date: 02/23/2020
ms.openlocfilehash: c74b842d27146683ef3168a534b9a012ae48fcad
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587039"
---
# <a name="what-is-language-understanding-luis"></a>Mi a Language Understanding (LUIS)?

A Language Understanding (LUIS) egy olyan felhőalapú API-szolgáltatás, amely egyéni gépi tanulási intelligenciát használ a felhasználó párbeszédszerű, természetes nyelvi szövege általános jelentésének megállapítására, valamint a releváns, részletes információk kinyerésére.

A LUIS ügyfélalkalmazása lehet bármely beszélgetési alkalmazás, amely a felhasználóval természetes nyelven kommunikál egy adott feladat végrehajtásához. Az ügyfélalkalmazások közé sorolhatók például a közösségimédia-alkalmazások, a csevegőrobotok és a beszédalapú asztali alkalmazások.

![A Cognitive Services Language Understanding (LUIS)-vel dolgozó 3 ügyfélalkalmazás fogalmi képe](./media/luis-overview/luis-entry-point.png "A Cognitive Services Language Understanding (LUIS)-vel dolgozó 3 ügyfélalkalmazás fogalmi képe")

## <a name="use-luis-in-a-chat-bot"></a>A LUIS használata csevegőrobotban

<a name="Accessing-LUIS"></a>

Miután közzétette a LUIS alkalmazást, egy ügyfélalkalmazás hosszúságú kimondott szöveg (Text) küld a LUIS Natural Language Processing Endpoint [API][endpoint-apis] -nak, és JSON-válaszként fogadja az eredményeket. A csevegőrobotok a LUIS gyakori ügyfélalkalmazásai.


![A LUIS elméleti ábrázolása a csevegő használatával a természetes nyelvi megértést (NLP) használó felhasználói szöveg előrejelzéséhez](./media/luis-overview/LUIS-chat-bot-request-response.svg "A LUIS-vel kapcsolatos elméleti képek a csevegő használatával a természetes nyelvi megértéssel (NLP")

|Lépés|Műveletek|
|:--|:--|
|1|Az ügyfélalkalmazás a következő felhasználói _kimondott szöveget_ (saját szavaikkal megfogalmazott szöveg) küldi el HTTP-kérésként a LUIS-végpontnak: „Fel szeretném hívni a HR képviselőmet.”|
|2|A LUIS lehetővé teszi, hogy egyéni nyelvi modelljeit az alkalmazáshoz való intelligenciával bővítse. A gépi megtanult nyelvi modellek a felhasználó strukturálatlan bemeneti szövegét, és egy JSON-formátumú választ adnak vissza, amely egy felső szándékú, `HRContact`. A JSON-végpont minimális válasza tartalmazza a lekérdezési kimondott szöveget, valamint a legmagasabb pontszámú szándékot. Emellett olyan adatok kinyerésére is képes, mint például a _Contact Type_ entitás.|
|3|Az ügyfélalkalmazás a JSON-választ használatával dönti el, hogy hogyan teljesítse a felhasználói kérést. Ezek a döntések magukban foglalhatják a bot Framework kódjának döntési fáját, és más szolgáltatásokhoz is meghívhatják azokat. |

A LUIS alkalmazás biztosítja az intelligenciát, így az ügyfélalkalmazás is intelligens döntéseket hozhat. A döntéseket nem a LUIS biztosítja.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Természetes nyelvek feldolgozása

A LUIS-alkalmazás egy tartományszintű természetes nyelvi modellt tartalmaz. A LUIS alkalmazás elindításához használhat előre összeállított tartománymodellt, elkészítheti a saját modelljét vagy kombinálhatja egy előre elkészített tartomány elemeit saját egyéni adataival.

* **Előre összeállított modell** A LUIS számos előre összeállított, szándékokat, kimondott szövegeket és előre létrehozott entitásokat tartalmazó tartománymodellel rendelkezik. Az előre létrehozott entitásokat a szándékok és kimondott szövegek használata nélkül is alkalmazhatja. Az [Előre összeállított tartománymodellek](luis-how-to-use-prebuilt-domains.md) tartalmazzák a teljes dizájnt, így nagyszerű módot kínálnak arra, hogy a LUIS használatát gyorsan elkezdhesse.

* **Egyéni modell** LUIS számos lehetőséget kínál a saját egyéni modelljeinek, például a szándékok és az entitások azonosítására. Az entitások többek között gépi megtanult entitások, konkrét vagy literális entitások, valamint gépi megtanult és literálos kombinációk.

## <a name="build-the-luis-model"></a>A LUIS-modell létrehozása
Hozza létre a modellt a [szerzői](https://go.microsoft.com/fwlink/?linkid=2092087) API-kkal vagy a [Luis-portálon](https://www.luis.ai).

A LUIS-modell a **[szándékok](luis-concept-intent.md)** elnevezésű felhasználói szándékok kategóriáival kezdődik. Minden egyes szándéknak szüksége van felhasználói **[kimondott szövegek](luis-concept-utterance.md)** példáira. Minden Kimondás lehetővé teszi a kinyerni kívánt adatgyűjtést.

|Példa felhasználói kimondott szövegre|Szándék|Kinyert adathalmazok|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|RepülőjegyFoglalás|Seattle|
|`When does your store __open__?`|ÜzletNyitvatartásÉsHelyszín|nyitás|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|ÉrtekezletÜtemezés|13 óra, Bob|

## <a name="query-prediction-endpoint"></a>Előrejelzési végpont lekérdezése

Miután az alkalmazást betanítják és közzétették a végponton, az ügyfélalkalmazás hosszúságú kimondott szöveg küld az előrejelzési [végpont](https://go.microsoft.com/fwlink/?linkid=2092356) API-nak. Az API a teljes elemzésre alkalmazza az alkalmazást, és az előrejelzési eredményeket JSON formátumban válaszolja meg.

A JSON-végpont minimális válasza tartalmazza a lekérdezési kimondott szöveget, valamint a legmagasabb pontszámú szándékot. Emellett olyan adatok kinyerése is kinyerhető, mint például a következő **kapcsolatfelvételi típus** entitás és az általános hangulat.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "normalizedQuery": "i want to call my hr rep",
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "negative",
            "score": 0.103343368
        }
    }
}
```

## <a name="improve-model-prediction"></a>Modell-előrejelzés javítása

Miután közzétette a LUIS-alkalmazást, és valós felhasználói hosszúságú kimondott szöveg kapott, a LUIS a végpontok hosszúságú kimondott szöveg [aktív tanulását](luis-concept-review-endpoint-utterances.md) teszi lehetővé az előrejelzés pontosságának javítása érdekében.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Fejlesztési életciklus
LUIS eszközöket, verziószámozást és együttműködést biztosít más LUIS szerzőkkel, hogy integrálni lehessen a teljes [fejlesztési](luis-concept-app-iteration.md)életciklusba.

## <a name="implementing-luis"></a>A LUIS implementálása
Language Understanding (LUIS) REST APIként bármilyen termékkel, szolgáltatással vagy keretrendszerrel használható HTTP-kéréssel. Az alábbi lista tartalmazza a főbb, LUIS szolgáltatással használható Microsoft-termékeket és -szolgáltatásokat.

A LUIS leggyakoribb ügyfélalkalmazása:
* A [Web app bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) gyorsan létrehoz egy LUIS-kompatibilis csevegőrobotot, amely szövegbevitel útján kommunikál a felhasználóval. A [bot Framework][bot-framework] [4. x](https://github.com/Microsoft/botbuilder-dotnet) verzióját használja egy teljes körű robot-élményhez.

Eszközök a LUIS robotokkal való gyors és egyszerű használatához:
* [Luis CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) A NPM csomag szerzői és előrejelzési lehetőségeket biztosít önálló parancssori eszközként vagy importálásként.
* A [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) eszközzel szigorú típusmegadású C#- és TypeScript-forráskód hozható létre exportált LUIS-modellekből.
* A [Dispatch](https://aka.ms/dispatch-tool) többféle LUIS- és QnA Maker-alkalmazás számára is lehetővé teszi, hogy elérhetők legyenek egy Dispatcher-modellt használó szülőalkalmazásból.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) A LUDown egy parancssori eszköz, amely segít a robot nyelvi modelljeinek kezelésében.
* [Robot Framework – zeneszerző](https://github.com/microsoft/BotFramework-Composer) – integrált fejlesztői eszköz fejlesztőknek és több tudományágat tartalmazó csapatoknak a Microsoft bot Framework használatával robotok és társalgási tapasztalatok létrehozásához

További, a LUIS szolgáltatással használható Cognitive Services szolgáltatások:
* [QnA Maker][qnamaker] lehetővé teszi, hogy több típusú szöveg egyesíthető legyen egy kérdés-és válasz-Tudásbázisban.
* A [Speech service](../Speech-Service/overview.md) szöveggé alakítja a szóbeli kéréseket.
* A [Conversation learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) robotbeszélgetések gyorsabb létrehozását teszi lehetővé a LUIS szolgáltatással.

Minták LUIS használatával:
* [Társalgási AI](https://github.com/Microsoft/AI) GitHub-tárház.
* [Bot Framework – bot-minták](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Következő lépések

* [Újdonságok](whats-new.md)
* Hozzon létre új LUIS alkalmazást [előre összeállított](luis-get-started-create-app.md) vagy [egyéni](luis-quickstart-intents-only.md) tartománnyal.
* [Kérdezze le egy nyilvános IoT-alkalmazás előrejelzési végpontját](luis-get-started-get-intent-from-browser.md).
* A LUIS [fejlesztői erőforrásai](developer-reference-resource.md) .

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/