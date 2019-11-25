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

Language Understanding (LUIS) is a cloud-based API service that applies custom machine-learning intelligence to natural language text to predict overall meaning, and pull out relevant, detailed information. 

For example, when a client application sends the text, `find me a wireless keyboard for $30`, LUIS responds with the following JSON object. 

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
In the example above, the _**intent**_ , or overall meaning of the phrase is that the user is trying to find an item. The detailed pieces of information that LUIS extracts are called _**entities**_ . In this case, the entities are the name of the item the user is looking for and the amount of money they want to spend.

Client applications use LUIS's returned JSON, the _intent_ (category), and _entities_ (extracted detailed information), to drive actions in the client application. A client application for LUIS is often a conversational application that communicates with a user in natural language to complete a task. Az ügyfélalkalmazások közé sorolhatók például a közösségimédia-alkalmazások, a csevegőrobotok és a beszédalapú asztali alkalmazások. 

![Conceptual image of 3 client applications working with Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Conceptual image of 3 client applications working with Cognitive Services Language Understanding (LUIS)")

## <a name="example-use-luis-in-a-chat-bot"></a>Example use LUIS in a chat bot

<a name="Accessing-LUIS"></a>

A client application sends utterances (text) to the published LUIS natural language processing endpoint [API][endpoint-apis] and receives the results as JSON responses. A csevegőrobotok a LUIS gyakori ügyfélalkalmazásai.


![Conceptual imagery of LUIS working with Chat bot to predict user text with natural language understanding (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Conceptual imagery of LUIS working with Chat bot to predict user text with natural language understanding (NLP")

|Lépés:|Műveletek|
|:--|:--|
|1|Az ügyfélalkalmazás a következő felhasználói _kimondott szöveget_ (saját szavaikkal megfogalmazott szöveg) küldi el HTTP-kérésként a LUIS-végpontnak: „Fel szeretném hívni a HR képviselőmet.”|
|2|LUIS applies machine learned language models to the user's unstructured input text and returns a JSON-formatted response, with a top intent, `HRContact`. A JSON-végpont minimális válasza tartalmazza a lekérdezési kimondott szöveget, valamint a legmagasabb pontszámú szándékot. It can also extract data such as the _Contact Type_ entity.|
|3|Az ügyfélalkalmazás a JSON-választ használatával dönti el, hogy hogyan teljesítse a felhasználói kérést. These decisions can include a decision tree in the bot and calls to other services. |

A LUIS alkalmazás biztosítja az intelligenciát, így az ügyfélalkalmazás is intelligens döntéseket hozhat. A döntéseket nem a LUIS biztosítja. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Természetes nyelvek feldolgozása

Your LUIS app contains domain-specific natural language models, which work together. You can start the LUIS app with one or more prebuilt models, build your own model, or blend prebuilt models with your own custom information.

* **Prebuilt model** LUIS has many prebuilt domains that include intent and entity models that work together to complete common usage scenarios. These domains include labeled utterances that can be inspected and edited, allowing you to customize them. Az [Előre összeállított tartománymodellek](luis-how-to-use-prebuilt-domains.md) tartalmazzák a teljes dizájnt, így nagyszerű módot kínálnak arra, hogy a LUIS használatát gyorsan elkezdhesse. In addition, there are prebuilt entities such as currency and number that you can use independently from the prebuilt domains.

* **Custom model** LUIS gives you several ways to build your own custom models including intents, and entities. Entities include machine-learned entities, pattern matching entities, and a combination of machine-learned and pattern matching.

## <a name="build-the-luis-app"></a>Build the LUIS app
Build the app with the [authoring](https://go.microsoft.com/fwlink/?linkid=2092087) APIs or with the [LUIS portal](https://www.luis.ai).

The LUIS app begins with categories of input text called **[intents](luis-concept-intent.md)** . Minden egyes szándéknak szüksége van felhasználói **[kimondott szövegek](luis-concept-utterance.md)** példáira. Each utterance can provide data that needs to be extracted. 

|Példa felhasználói kimondott szövegre|Szándék|Extracted data|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|RepülőjegyFoglalás|Seattle|
|`When does your store __open__?`|ÜzletNyitvatartásÉsHelyszín|nyitás|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|ÉrtekezletÜtemezés|13 óra, Bob|

## <a name="query-prediction-endpoint"></a>Előrejelzési végpont lekérdezése

After your app is trained and published to the endpoint, the client application sends utterances to the prediction [endpoint](https://go.microsoft.com/fwlink/?linkid=2092356) API. The API applies the app to the utterance for analysis and responds with the prediction results in a JSON format.  

A JSON-végpont minimális válasza tartalmazza a lekérdezési kimondott szöveget, valamint a legmagasabb pontszámú szándékot. It can also extract data such as the following **Contact Type** entity and overall sentiment. 

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

After your LUIS app is published and receives real user utterances, LUIS provides [active learning](luis-concept-review-endpoint-utterances.md) of endpoint utterances to improve prediction accuracy. 

<a name="using-luis"></a>

## <a name="iterative-development-lifecycle"></a>Iterative development lifecycle
LUIS provides tools, versioning, and collaboration with other LUIS authors to integrate into the full iterative [development life cycle](luis-concept-app-iteration.md). 

## <a name="implementing-luis"></a>A LUIS implementálása
Language Understanding (LUIS), as a REST API, can be used with any product, service, or framework with an HTTP request. Az alábbi lista tartalmazza a főbb, LUIS szolgáltatással használható Microsoft-termékeket és -szolgáltatásokat.

A LUIS leggyakoribb ügyfélalkalmazása:
* A [Web app bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) gyorsan létrehoz egy LUIS-kompatibilis csevegőrobotot, amely szövegbevitel útján kommunikál a felhasználóval. Uses [Bot Framework][bot-framework] version [4.x](https://github.com/Microsoft/botbuilder-dotnet) for a complete bot experience.

Eszközök a LUIS robotokkal való gyors és egyszerű használatához:
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) The NPM package provides authoring and prediction with as either a stand-alone command line tool or as import. 
* A [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) eszközzel szigorú típusmegadású C#- és TypeScript-forráskód hozható létre exportált LUIS-modellekből.
* A [Dispatch](https://aka.ms/dispatch-tool) többféle LUIS- és QnA Maker-alkalmazás számára is lehetővé teszi, hogy elérhetők legyenek egy Dispatcher-modellt használó szülőalkalmazásból.
* A [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) a robotok nyelvi modelljeinek kezelését egyszerűsítő parancssori eszköz.
* [Bot framework - Composer](https://github.com/microsoft/BotFramework-Composer) - an integrated development tool for developers and multi-disciplinary teams to build bots and conversational experiences with the Microsoft Bot Framework

További, a LUIS szolgáltatással használható Cognitive Services szolgáltatások:
* [QnA Maker][qnamaker] allows several types of text to combine into a question and answer knowledge base.
* A [Speech service](../Speech-Service/overview.md) szöveggé alakítja a szóbeli kéréseket. 
* A [Conversation learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) robotbeszélgetések gyorsabb létrehozását teszi lehetővé a LUIS szolgáltatással.

Samples using LUIS:
* [Conversational AI](https://github.com/Microsoft/AI) GitHub repository.
* [Bot framework - Bot samples](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Következő lépések

* [Újdonságok](whats-new.md)
* Author a new LUIS app with a [prebuilt](luis-get-started-create-app.md) or [custom](luis-quickstart-intents-only.md) domain
* [Kérdezze le egy nyilvános IoT-alkalmazás előrejelzési végpontját](luis-get-started-get-intent-from-browser.md). 
* [Developer resources](developer-reference-resource.md) for LUIS. 

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
