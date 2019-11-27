---
title: Mi a Language Understanding (LUIS)?
titleSuffix: Azure Cognitive Services
description: A Language Understanding (LUIS) egy olyan felhőalapú API-szolgáltatás, amely egyéni gépi tanulási intelligenciát használ a felhasználó párbeszédszerű, természetes nyelvi szövege általános jelentésének megállapítására, valamint a releváns, részletes információk kinyerésére.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 99f312521727658788e96a57b619a7c0e3d4751b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456567"
---
# <a name="what-is-language-understanding-luis"></a>Mi a Language Understanding (LUIS)?

A Language Understanding (LUIS) egy felhőalapú API-szolgáltatás, amely egyéni gépi tanulási intelligenciát alkalmaz természetes nyelvű szövegre az általános jelentés megbecslése érdekében, valamint a releváns és részletes információk lekéréséhez. 

Ha például egy ügyfélalkalmazás elküldi a szöveget, `find me a wireless keyboard for $30`, LUIS a következő JSON-objektummal válaszol. 

```JSON
{
    "query": "find me a wireless keyboard for $30",
    "prediction": {
        "topIntent": "Finditem",
        "intents": {
            "Finditem": {
                "score": 0.934672
            }
        },
        "entities": {
            "item": [
                "wireless keyboard"
            ],
            "money": [
        {
            "number": 30,
            "units": "Dollar"
        }
           ]
        }
        
    }
}
```
A fenti példában a kifejezés _**szándéka**_ vagy általános jelentése szerint a felhasználó egy elem megtalálását kísérli meg. A LUIS-kivonatok _**entitásoknak**_ nevezett részletes információk. Ebben az esetben az entitások a felhasználó által keresett elem neve és a költeni kívánt mennyiség.

Az ügyfélalkalmazások a LUIS által visszaadott JSON-t, a _szándékot_ (kategóriát) és az _entitásokat_ (részletes információkat) használják az ügyfélalkalmazás műveleteinek elvégzéséhez. A LUIS-ügyfélalkalmazás gyakran olyan társalgási alkalmazás, amely természetes nyelven kommunikál a felhasználóval a feladat elvégzéséhez. Az ügyfélalkalmazások közé sorolhatók például a közösségimédia-alkalmazások, a csevegőrobotok és a beszédalapú asztali alkalmazások. 

![A Cognitive Services Language Understanding (LUIS)-vel dolgozó 3 ügyfélalkalmazás fogalmi képe](./media/luis-overview/luis-entry-point.png "A Cognitive Services Language Understanding (LUIS)-vel dolgozó 3 ügyfélalkalmazás fogalmi képe")

## <a name="example-use-luis-in-a-chat-bot"></a>Példa a LUIS használatára egy csevegési robotban

<a name="Accessing-LUIS"></a>

Az ügyfélalkalmazás hosszúságú kimondott szöveg (Text) küld a közzétett LUIS Natural Language Processing Endpoint [API][endpoint-apis] -nak, és JSON-válaszként fogadja az eredményeket. A csevegőrobotok a LUIS gyakori ügyfélalkalmazásai.


![A LUIS elméleti ábrázolása a csevegő használatával a természetes nyelvi megértést (NLP) használó felhasználói szöveg előrejelzéséhez](./media/luis-overview/LUIS-chat-bot-request-response.svg "A LUIS-vel kapcsolatos elméleti képek a csevegő használatával a természetes nyelvi megértéssel (NLP")

|Lépés|Műveletek|
|:--|:--|
|1|Az ügyfélalkalmazás a következő felhasználói _kimondott szöveget_ (saját szavaikkal megfogalmazott szöveg) küldi el HTTP-kérésként a LUIS-végpontnak: „Fel szeretném hívni a HR képviselőmet.”|
|2|LUIS a gépi megtanult nyelvi modelleket alkalmazza a felhasználó strukturálatlan bemeneti szöveggé, és egy JSON-formátumú választ ad vissza, amely egy felső szándékú, `HRContact`. A JSON-végpont minimális válasza tartalmazza a lekérdezési kimondott szöveget, valamint a legmagasabb pontszámú szándékot. Emellett olyan adatok kinyerésére is képes, mint például a _Contact Type_ entitás.|
|3|Az ügyfélalkalmazás a JSON-választ használatával dönti el, hogy hogyan teljesítse a felhasználói kérést. Ezek a döntések magukban foglalhatnak egy döntési fát a robotban, és más szolgáltatásokhoz is meghívhatják őket. |

A LUIS alkalmazás biztosítja az intelligenciát, így az ügyfélalkalmazás is intelligens döntéseket hozhat. A döntéseket nem a LUIS biztosítja. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Természetes nyelvek feldolgozása

A LUIS-alkalmazás olyan, tartományra jellemző természetes nyelvi modelleket tartalmaz, amelyek együtt működnek. A LUIS-alkalmazást elindíthatja egy vagy több előre elkészített modellel, saját modellt építhet ki, vagy az előre elkészített modelleket saját egyéni adatokkal is összekeverheti.

* **Előre elkészített modell** LUIS számos előre elkészített tartománnyal rendelkezik, amelyek közös használati forgatókönyvek elvégzéséhez használható szándékot és entitás-modelleket tartalmaznak. Ezek a tartományok olyan címkézett hosszúságú kimondott szöveg tartalmaznak, amelyek ellenőrizhetők és szerkeszthetők, így testre szabhatja őket. Az [Előre összeállított tartománymodellek](luis-how-to-use-prebuilt-domains.md) tartalmazzák a teljes dizájnt, így nagyszerű módot kínálnak arra, hogy a LUIS használatát gyorsan elkezdhesse. Emellett vannak olyan előre elkészített entitások is, mint például a pénznem és a szám, amelyeket az előre elkészített tartományokból függetlenül használhat.

* **Egyéni modell** LUIS számos lehetőséget kínál a saját egyéni modelljeinek, például a szándékok és az entitások kiépítésére. Az entitások többek között a géppel megtanult entitások, a mintázattal egyező entitások, valamint a gépi megtanult és a mintázatok kombinációja.

## <a name="build-the-luis-app"></a>A LUIS-alkalmazás létrehozása
Hozza létre az alkalmazást a [szerzői](https://go.microsoft.com/fwlink/?linkid=2092087) API-kkal vagy a [Luis-portálon](https://www.luis.ai).

A LUIS-alkalmazás a **[meghívott bemeneti](luis-concept-intent.md)** szöveg kategóriáit veszi kezdetbe. Minden egyes szándéknak szüksége van felhasználói **[kimondott szövegek](luis-concept-utterance.md)** példáira. Minden Kimondás lehetővé teszi a kinyerni kívánt adatgyűjtést. 

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

## <a name="iterative-development-lifecycle"></a>Ismétlődő fejlesztési életciklus
LUIS eszközöket, verziószámozást és együttműködést biztosít más LUIS-szerzőkkel, hogy integrálni lehessen a teljes körű, ismétlődő [fejlesztési](luis-concept-app-iteration.md)életciklusba. 

## <a name="implementing-luis"></a>A LUIS implementálása
Language Understanding (LUIS) REST APIként bármilyen termékkel, szolgáltatással vagy keretrendszerrel használható HTTP-kéréssel. Az alábbi lista tartalmazza a főbb, LUIS szolgáltatással használható Microsoft-termékeket és -szolgáltatásokat.

A LUIS leggyakoribb ügyfélalkalmazása:
* A [Web app bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) gyorsan létrehoz egy LUIS-kompatibilis csevegőrobotot, amely szövegbevitel útján kommunikál a felhasználóval. A [bot Framework][bot-framework] [4. x](https://github.com/Microsoft/botbuilder-dotnet) verzióját használja egy teljes körű robot-élményhez.

Eszközök a LUIS robotokkal való gyors és egyszerű használatához:
* [Luis CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) A NPM csomag szerzői és előrejelzési lehetőségeket biztosít önálló parancssori eszközként vagy importálásként. 
* A [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) eszközzel szigorú típusmegadású C#- és TypeScript-forráskód hozható létre exportált LUIS-modellekből.
* A [Dispatch](https://aka.ms/dispatch-tool) többféle LUIS- és QnA Maker-alkalmazás számára is lehetővé teszi, hogy elérhetők legyenek egy Dispatcher-modellt használó szülőalkalmazásból.
* A [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) a robotok nyelvi modelljeinek kezelését egyszerűsítő parancssori eszköz.
* [Robot Framework – zeneszerző](https://github.com/microsoft/BotFramework-Composer) – integrált fejlesztői eszköz fejlesztőknek és több tudományágat tartalmazó csapatoknak a Microsoft bot Framework használatával robotok és társalgási tapasztalatok létrehozásához

További, a LUIS szolgáltatással használható Cognitive Services szolgáltatások:
* [QnA Maker][qnamaker] lehetővé teszi, hogy több típusú szöveg egyesíthető legyen egy kérdés-és válasz-Tudásbázisban.
* A [Speech service](../Speech-Service/overview.md) szöveggé alakítja a szóbeli kéréseket. 
* A [Conversation learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) robotbeszélgetések gyorsabb létrehozását teszi lehetővé a LUIS szolgáltatással.

Minták LUIS használatával:
* [Társalgási AI](https://github.com/Microsoft/AI) GitHub-tárház.
* [Bot Framework – bot-minták](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>További lépések

* [Újdonságok](whats-new.md)
* Új LUIS-alkalmazás létrehozása [előre elkészített](luis-get-started-create-app.md) vagy [Egyéni](luis-quickstart-intents-only.md) tartománnyal
* [Kérdezze le egy nyilvános IoT-alkalmazás előrejelzési végpontját](luis-get-started-get-intent-from-browser.md). 
* A LUIS [fejlesztői erőforrásai](developer-reference-resource.md) . 

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
