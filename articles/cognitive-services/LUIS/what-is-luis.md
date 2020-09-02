---
title: Mi a Language Understanding (LUIS)?
description: Language Understanding (LUIS) – felhőalapú API-szolgáltatás gépi tanulással, természetes nyelvvel, a jelentés és az adatok kinyerése érdekében.
keywords: Azure, mesterséges intelligencia, AI, természetes nyelvi feldolgozás, NLP, természetes nyelvi megértés, NLU, AI-beszélgetés, társalgási AI, AI Csevegőrobot, Csevegőrobot Maker, LUIS, NLP AI, Luis AI, Azure Luis, természetes nyelv megismerése
ms.topic: overview
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 98d801f6a34feb40d56215f8b6257a68ec628e10
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89320052"
---
# <a name="what-is-language-understanding-luis"></a>Mi a Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A Language Understanding (LUIS) egy olyan felhőalapú API-szolgáltatás, amely egyéni gépi tanulási intelligenciát használ a felhasználó párbeszédszerű, természetes nyelvi szövege általános jelentésének megállapítására, valamint a releváns, részletes információk kinyerésére.

A LUIS ügyfélalkalmazása lehet bármely beszélgetési alkalmazás, amely a felhasználóval természetes nyelven kommunikál egy adott feladat végrehajtásához. Az ügyfélalkalmazások közé sorolhatók például a közösségimédia-alkalmazások, a csevegőrobotok és a beszédalapú asztali alkalmazások.

![A Cognitive Services Language Understanding (LUIS)-vel dolgozó 3 ügyfélalkalmazás fogalmi képe](./media/luis-overview/luis-entry-point.png "A Cognitive Services Language Understanding (LUIS)-vel dolgozó 3 ügyfélalkalmazás fogalmi képe")

## <a name="use-luis-in-a-chat-bot"></a>A LUIS használata csevegőrobotban

<a name="Accessing-LUIS"></a>

A LUIS alkalmazás közzététele után az ügyfélalkalmazás kimondott szöveget (szöveg) küld a LUIS természetes nyelvi feldolgozási végpont [API-jának][endpoint-apis], és az eredményeket JSON-válaszok formájában kapja meg. A csevegőrobotok a LUIS gyakori ügyfélalkalmazásai.


![A LUIS elméleti ábrázolása a csevegő használatával a természetes nyelvi megértést (NLP) használó felhasználói szöveg előrejelzéséhez](./media/luis-overview/LUIS-chat-bot-request-response.svg "A LUIS-vel kapcsolatos elméleti képek a csevegő használatával a természetes nyelvi megértéssel (NLP")

|Lépés|Művelet|
|:--|:--|
|1|Az ügyfélalkalmazás a következő felhasználói _kimondott szöveget_ (saját szavaikkal megfogalmazott szöveg) küldi el HTTP-kérésként a LUIS-végpontnak: „Fel szeretném hívni a HR képviselőmet.”|
|2|A LUIS lehetővé teszi, hogy egyéni nyelvi modelljeit az alkalmazáshoz való intelligenciával bővítse. A gépi megtanult nyelvi modellek a felhasználó strukturálatlan bemeneti szövegét, és egy JSON-formátumú választ adnak vissza, amely a legfelső szándékot használja `HRContact` . A JSON-végpont minimális válasza tartalmazza a lekérdezési kimondott szöveget, valamint a legmagasabb pontszámú szándékot. Emellett olyan adatok kinyerésére is képes, mint például a _Contact Type_ entitás.|
|3|Az ügyfélalkalmazás a JSON-választ használatával dönti el, hogy hogyan teljesítse a felhasználói kérést. Ezek a döntések magukban foglalhatják a bot Framework kódjának döntési fáját, és más szolgáltatásokhoz is meghívhatják azokat. |

A LUIS alkalmazás biztosítja az intelligenciát, így az ügyfélalkalmazás is intelligens döntéseket hozhat. A döntéseket nem a LUIS biztosítja.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-understanding-nlu"></a>Természetes nyelvi megértés (NLU)

A [Luis mesterséges intelligenciát (AI) biztosít](artificial-intelligence.md) a NLU, a természetes nyelvi feldolgozás (NLP) egy részhalmaza formájában.

A LUIS-alkalmazás egy tartományszintű természetes nyelvi modellt tartalmaz. A LUIS alkalmazás elindításához használhat előre összeállított tartománymodellt, elkészítheti a saját modelljét vagy kombinálhatja egy előre elkészített tartomány elemeit saját egyéni adataival.

* **Előre összeállított modell** A LUIS számos előre összeállított, szándékokat, kimondott szövegeket és előre létrehozott entitásokat tartalmazó tartománymodellel rendelkezik. Az előre létrehozott entitásokat a szándékok és kimondott szövegek használata nélkül is alkalmazhatja. Az [Előre összeállított tartománymodellek](luis-how-to-use-prebuilt-domains.md) tartalmazzák a teljes dizájnt, így nagyszerű módot kínálnak arra, hogy a LUIS használatát gyorsan elkezdhesse.

* **Egyéni modell** LUIS számos lehetőséget kínál a saját egyéni modelljeinek, például a szándékok és az entitások azonosítására. Az entitások például a gépi tanulási entitások, a konkrét vagy literális entitások, valamint a gépi tanulás és a literál kombinációja.

További információ az [NLP](artificial-intelligence.md)-ról és a NLU Luis-specifikus területéről.

## <a name="step-1-design-and-build-your-model"></a>1. lépés: a modell megtervezése és összeállítása

Tervezze meg a modellt a **[felhasználói szándékok nevű](luis-concept-intent.md)** kategóriával. Minden egyes szándéknak szüksége van felhasználói **[kimondott szövegek](luis-concept-utterance.md)** példáira. Minden Kimondás lehetővé teszi a [gépi tanulási entitásokkal](luis-concept-entity-types.md#effective-machine-learned-entities)kinyerni kívánt adatgyűjtést.

|Példa felhasználói kimondott szövegre|Szándék|Kinyert adathalmazok|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|RepülőjegyFoglalás|Seattle|
|`When does your store open?`|ÜzletNyitvatartásÉsHelyszín|nyitás|
|`Schedule a meeting at 1pm with Bob in Distribution`|ÉrtekezletÜtemezés|13 óra, Bob|

Hozza létre a modellt a [szerzői](https://go.microsoft.com/fwlink/?linkid=2092087) API-kkal vagy a [**Luis-portálral**](https://www.luis.ai), vagy mindkettővel. További információ a [portál](get-started-portal-build-app.md) és az [SDK-ügyfél kódtárainak](azure-sdk-quickstart.md)létrehozásáról.

## <a name="step-2-get-the-query-prediction"></a>2. lépés: a lekérdezés előrejelzésének beolvasása

Miután az alkalmazás modelljét betanítják és közzétették a végponton, egy ügyfélalkalmazás (például egy chat-bot) elküldi a hosszúságú kimondott szöveg az előrejelzési [végpont](https://go.microsoft.com/fwlink/?linkid=2092356) API-nak. Az API a modellt a teljes elemzésre alkalmazza, és az előrejelzési eredményeket JSON formátumban adja meg.

A JSON-végpont minimális válasza tartalmazza a lekérdezési kimondott szöveget, valamint a legmagasabb pontszámú szándékot. Emellett olyan adatok kinyerése is kinyerhető, mint például a következő **kapcsolatfelvételi típus** entitás és az általános hangulat.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
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
            "label": "neutral",
            "score": 0.5
        }
    }
}
```

## <a name="step-3-improve-model-prediction"></a>3. lépés: a modell előrejelzésének javítása

Miután közzétette a LUIS-alkalmazást, és valós felhasználói hosszúságú kimondott szöveg kapott, a LUIS a végpontok hosszúságú kimondott szöveg [aktív tanulását](luis-concept-review-endpoint-utterances.md) teszi lehetővé az előrejelzés pontosságának javítása érdekében. Tekintse át ezeket a javaslatokat a fejlesztési életciklusának rendszeres karbantartási munkájának részeként.

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>Fejlesztési életciklus és eszközök
LUIS eszközöket, verziószámozást és együttműködést biztosít más LUIS szerzőkkel, hogy integrálni lehessen a teljes [fejlesztési](luis-concept-app-iteration.md)életciklusba.

Language Understanding (LUIS) REST APIként bármilyen termékkel, szolgáltatással vagy keretrendszerrel használható HTTP-kéréssel. A LUIS az ügyféloldali kódtárakat (SDK-kat) is biztosítja számos legnépszerűbb programozási nyelvhez. További információ a megadott [fejlesztői erőforrásokról](developer-reference-resource.md) .

Eszközök a LUIS robotokkal való gyors és egyszerű használatához:
* [Luis CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) A NPM csomag szerzői és előrejelzési lehetőségeket biztosít önálló parancssori eszközként vagy importálásként.
* A [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) eszközzel szigorú típusmegadású C#- és TypeScript-forráskód hozható létre exportált LUIS-modellekből.
* A [Dispatch](https://aka.ms/dispatch-tool) többféle LUIS- és QnA Maker-alkalmazás számára is lehetővé teszi, hogy elérhetők legyenek egy Dispatcher-modellt használó szülőalkalmazásból.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) A LUDown egy parancssori eszköz, amely segít a robot nyelvi modelljeinek kezelésében.

## <a name="integrate-with-a-bot"></a>Integrálás robottal

Használja az [Azure bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) -t a [Microsoft bot Framework](https://dev.botframework.com/) használatával egy csevegési robot létrehozásához és üzembe helyezéséhez. Megtervezheti és fejlesztheti a grafikus kezelőfelülettel, a [zeneszerzővel](https://docs.microsoft.com/composer/)vagy a leggyakoribb bot-forgatókönyvekhez tervezett [munkabot-mintákkal](https://github.com/microsoft/BotBuilder-Samples) .

## <a name="integrate-with-other-cognitive-services"></a>Integrálás más Cognitive Servicesokkal

További, a LUIS szolgáltatással használható Cognitive Services szolgáltatások:
* A [QnA Maker][qnamaker] különböző típusú szövegek kombinálásával teszi lehetővé egy kérdéseken és válaszokon alapuló tudásbázis kialakítását.
* A [Speech service](../Speech-Service/overview.md) szöveggé alakítja a szóbeli kéréseket.

A LUIS a meglévő LUIS-erőforrások részeként Text Analytics funkciókat biztosít. Ez a funkció magában foglalja az [érzelmek elemzését](luis-how-to-publish-app.md#configuring-publish-settings) és a [kulcsfontosságú kifejezés kinyerését](luis-reference-prebuilt-keyphrase.md) az előre elkészített keyPhrase-entitással.

## <a name="learn-with-the-quickstarts"></a>Ismerkedjen meg a gyors útmutatókkal

Ismerje meg a LUIS-t gyakorlati útmutatókkal a [portál](get-started-portal-build-app.md) és az [SDK ügyféloldali kódtárak](azure-sdk-quickstart.md)használatával.


## <a name="next-steps"></a>Következő lépések

* A szolgáltatással és a dokumentációval kapcsolatos [Újdonságok](whats-new.md)
* [Tervezze meg alkalmazását](luis-how-plan-your-app.md) [szándékokkal](luis-concept-intent.md) és [entitásokkal](luis-concept-entity-types.md).
* [Az előrejelzési végpont lekérdezése](luis-get-started-get-intent-from-browser.md).
* A LUIS [fejlesztői erőforrásai](developer-reference-resource.md) .

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
