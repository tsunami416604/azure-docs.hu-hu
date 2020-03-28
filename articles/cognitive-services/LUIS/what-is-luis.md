---
title: Mi a Language Understanding (LUIS)?
description: A Language Understanding (LUIS) egy olyan felhőalapú API-szolgáltatás, amely egyéni gépi tanulási intelligenciát használ a felhasználó párbeszédszerű, természetes nyelvi szövege általános jelentésének megállapítására, valamint a releváns, részletes információk kinyerésére.
ms.topic: overview
ms.date: 02/23/2020
ms.openlocfilehash: 98fb936422f8e23e728efea19fa2cd75d90fac57
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80053355"
---
# <a name="what-is-language-understanding-luis"></a>Mi a Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A Language Understanding (LUIS) egy olyan felhőalapú API-szolgáltatás, amely egyéni gépi tanulási intelligenciát használ a felhasználó párbeszédszerű, természetes nyelvi szövege általános jelentésének megállapítására, valamint a releváns, részletes információk kinyerésére.

A LUIS ügyfélalkalmazása lehet bármely beszélgetési alkalmazás, amely a felhasználóval természetes nyelven kommunikál egy adott feladat végrehajtásához. Az ügyfélalkalmazások közé sorolhatók például a közösségimédia-alkalmazások, a csevegőrobotok és a beszédalapú asztali alkalmazások.

![A Cognitive Services nyelvi ismertetésével (LUIS) dolgozó 3 ügyfélalkalmazás fogalmi képe](./media/luis-overview/luis-entry-point.png "A Cognitive Services nyelvi ismertetésével (LUIS) dolgozó 3 ügyfélalkalmazás fogalmi képe")

## <a name="use-luis-in-a-chat-bot"></a>A LUIS használata csevegőrobotban

<a name="Accessing-LUIS"></a>

A LUIS alkalmazás közzététele után az ügyfélalkalmazás kimondott szöveget (szöveg) küld a LUIS természetes nyelvi feldolgozási végpont [API-jának][endpoint-apis], és az eredményeket JSON-válaszok formájában kapja meg. A csevegőrobotok a LUIS gyakori ügyfélalkalmazásai.


![A LUIS csevegőrobottal együttműködve a felhasználói szöveg természetes nyelvi megértéssel (NLP) való előrejelzésének fogalma](./media/luis-overview/LUIS-chat-bot-request-response.svg "A LUIS-nak a Chat botdal együttműködve végzett koncepcionális képei a felhasználói szöveg természetes nyelvi megértéssel való előrejelzésére (NLP")

|Lépés|Műveletek|
|:--|:--|
|1|Az ügyfélalkalmazás a következő felhasználói _kimondott szöveget_ (saját szavaikkal megfogalmazott szöveg) küldi el HTTP-kérésként a LUIS-végpontnak: „Fel szeretném hívni a HR képviselőmet.”|
|2|A LUIS lehetővé teszi, hogy az egyéni nyelvi modellek et az alkalmazáshoz való hozzáadáshoz hoz. A gépmegtanult nyelvi modellek a felhasználó strukturálatlan beviteli szövegét veszik `HRContact`fel, és json-formátumú választ adnak vissza, felső szándékkal. A JSON-végpont minimális válasza tartalmazza a lekérdezési kimondott szöveget, valamint a legmagasabb pontszámú szándékot. Azt is kinyerheti az adatokat, például a _Kapcsolat típusa_ entitás.|
|3|Az ügyfélalkalmazás a JSON-választ használatával dönti el, hogy hogyan teljesítse a felhasználói kérést. Ezek a döntések tartalmazhatnak döntési fát a robotkeretkódban és más szolgáltatásokhoz való hívásokat. |

A LUIS alkalmazás biztosítja az intelligenciát, így az ügyfélalkalmazás is intelligens döntéseket hozhat. A döntéseket nem a LUIS biztosítja.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Természetes nyelvek feldolgozása

A LUIS alkalmazás tartományspecifikus természetes nyelvi modellt tartalmaz. A LUIS alkalmazás elindításához használhat előre összeállított tartománymodellt, elkészítheti a saját modelljét vagy kombinálhatja egy előre elkészített tartomány elemeit saját egyéni adataival.

* **Előre összeállított modell** A LUIS számos előre összeállított, szándékokat, kimondott szövegeket és előre létrehozott entitásokat tartalmazó tartománymodellel rendelkezik. Az előre létrehozott entitásokat a szándékok és kimondott szövegek használata nélkül is alkalmazhatja. Az [Előre összeállított tartománymodellek](luis-how-to-use-prebuilt-domains.md) tartalmazzák a teljes dizájnt, így nagyszerű módot kínálnak arra, hogy a LUIS használatát gyorsan elkezdhesse.

* **Egyéni modell** A LUIS számos lehetőséget biztosít a saját egyéni modellek azonosítására, beleértve a szándékokat és az entitásokat. Entitások közé tartozik a gép által megtanult entitások, konkrét vagy literális entitások, valamint a gép megtanult és a literális kombinációja.

## <a name="build-the-luis-model"></a>A LUIS-modell létrehozása
A modell létrehozása a [szerzői](https://go.microsoft.com/fwlink/?linkid=2092087) API-kkal vagy a [LUIS portállal.](https://www.luis.ai)

A LUIS-modell a **[szándékok](luis-concept-intent.md)** elnevezésű felhasználói szándékok kategóriáival kezdődik. Minden egyes szándéknak szüksége van felhasználói **[kimondott szövegek](luis-concept-utterance.md)** példáira. Minden utterance (kifejezés) olyan adatokat biztosíthat, amelyeket ki kell bontani.

|Példa felhasználói kimondott szövegre|Szándék|Kinyert adatok|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|RepülőjegyFoglalás|Seattle|
|`When does your store __open__?`|ÜzletNyitvatartásÉsHelyszín|nyitás|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|ÉrtekezletÜtemezés|13 óra, Bob|

## <a name="query-prediction-endpoint"></a>Előrejelzési végpont lekérdezése

Miután az alkalmazás bevan tanítva, és a végponton közzétett, az ügyfélalkalmazás küld utterances az előrejelzési [végpont](https://go.microsoft.com/fwlink/?linkid=2092356) API-t. Az API az alkalmazást alkalmazza az elemzésre vonatkozó utterance (kifejezés) kifejezésre, és json formátumban válaszol az előrejelzési eredményekre.

A JSON-végpont minimális válasza tartalmazza a lekérdezési kimondott szöveget, valamint a legmagasabb pontszámú szándékot. Azt is kinyerheti az adatokat, például a következő **Kapcsolat típusa** entitás és az általános hangulat.

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

A LUIS-alkalmazás közzététele után, és valós felhasználói utterances fogadása, LUIS biztosítja a végpont kimondott szöveg [aktív tanulása](luis-concept-review-endpoint-utterances.md) az előrejelzés pontosságának javítása érdekében.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Fejlesztési életciklus
A LUIS eszközöket, verziószámozást és együttműködést biztosít más LUIS-szerzőkkel a teljes [fejlesztési életciklusba](luis-concept-app-iteration.md)való integráláshoz.

## <a name="implementing-luis"></a>A LUIS implementálása
Language Understanding (LUIS), mint REST API, használható bármely termék, szolgáltatás vagy keretrendszer HTTP-kérelemmel. Az alábbi lista tartalmazza a főbb, LUIS szolgáltatással használható Microsoft-termékeket és -szolgáltatásokat.

A LUIS leggyakoribb ügyfélalkalmazása:
* A [Web app bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) gyorsan létrehoz egy LUIS-kompatibilis csevegőrobotot, amely szövegbevitel útján kommunikál a felhasználóval. A [Bot Framework][bot-framework] [4.x-es](https://github.com/Microsoft/botbuilder-dotnet) verzióját használja a teljes botélményhez.

Eszközök a LUIS robotokkal való gyors és egyszerű használatához:
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Az NPM-csomag önálló parancssori eszközként vagy importálásként biztosít szerzői és előrejelzési lehetőséget.
* A [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) eszközzel szigorú típusmegadású C#- és TypeScript-forráskód hozható létre exportált LUIS-modellekből.
* A [Dispatch](https://aka.ms/dispatch-tool) többféle LUIS- és QnA Maker-alkalmazás számára is lehetővé teszi, hogy elérhetők legyenek egy Dispatcher-modellt használó szülőalkalmazásból.
* [LUDown (LUDown)](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) A LUDown egy parancssori eszköz, amely segít a robot nyelvi modelljeinek kezelésében.
* [Bot framework - Composer](https://github.com/microsoft/BotFramework-Composer) - integrált fejlesztési eszköz a fejlesztők és a multidiszciplináris csapatok építeni botok és társalgási tapasztalatok a Microsoft Bot Framework

További, a LUIS szolgáltatással használható Cognitive Services szolgáltatások:
* A [QnA Maker][qnamaker] különböző típusú szövegek kombinálásával teszi lehetővé egy kérdéseken és válaszokon alapuló tudásbázis kialakítását.
* A [Speech service](../Speech-Service/overview.md) szöveggé alakítja a szóbeli kéréseket.
* A [Conversation learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) robotbeszélgetések gyorsabb létrehozását teszi lehetővé a LUIS szolgáltatással.

Luis-t használó minták:
* [Társalgási AI](https://github.com/Microsoft/AI) GitHub-tárház.
* [Bot keret - Bot minták](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>További lépések

* [Újdonságok](whats-new.md)
* Hozzon létre új LUIS alkalmazást [előre összeállított](luis-get-started-create-app.md) vagy [egyéni](luis-quickstart-intents-only.md) tartománnyal.
* [Kérdezze le egy nyilvános IoT-alkalmazás előrejelzési végpontját](luis-get-started-get-intent-from-browser.md).
* [Fejlesztői erőforrások](developer-reference-resource.md) a LUIS számára.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
