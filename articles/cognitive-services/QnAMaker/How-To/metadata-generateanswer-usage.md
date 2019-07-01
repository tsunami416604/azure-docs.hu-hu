---
title: Az API – QnA Maker GenerateAnswer metaadatok
titleSuffix: Azure Cognitive Services
description: QnA Maker segítségével adhat hozzá a metaadatokat, kulcs/érték párok formájában a kérdés-válasz csoportjai. Szűrheti az eredményeket a felhasználói lekérdezések, és tárolására is használható információk követő témák.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/27/2019
ms.author: diberry
ms.openlocfilehash: b691d447f51165ea3cb56410da9cd2d4d00ce913
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490205"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Válasz a GenerateAnswer API és a metaadatok

Az előre jelzett a felhasználó kérdésre adott válasz, amelyet a GenerateAnswer API-t. Amikor közzétesz egy Tudásbázis, láthatja az API használata az információ a **közzététel** lapot. Is konfigurálhatja a válaszok metaadatokat címkék alapján szűrheti az API-t, és a teszt lekérdezési karakterlánc paraméterrel a végpontról a Tudásbázis tesztelése.

QnA Maker segítségével adhat hozzá a metaadatokat, kulcs-érték párok formájában a kérdések és válaszok részhalmazához. Ezután használhatja ezeket az információkat, szűrheti az eredményeket a felhasználói lekérdezések és tárolására is használható információk követő témák. További információkért lásd: [Tudásbázis](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Kérdések és válaszok QnA entitáshoz Store

Fontos tudni, hogyan a QnA Maker a kérdés és válasz adatokat tároló. Az alábbi ábrán egy kérdés-válasz entitás:

![A QnA entitás ábrája](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Minden egyes QnA entitás rendelkezik egy egyedi és állandó. Az Alkalmazásazonosítót használhatja, hogy a frissítések adott QnA entitáson.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Első válasz előrejelzéseket GenerateAnswer API-val

Használja a [GenerateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) a robot vagy alkalmazás a felhasználói kérdés a Tudásbázisban, a legmegfelelőbb beszerezni a kérdés és válasz úgy állítja be.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>GenerateAnswer végpont közzététele 

A Tudásbázis, vagy a közzététel után a [QnA Maker portal](https://www.qnamaker.ai), vagy a [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), kérheti a GenerateAnswer végpont adatait.

A végpont adatait beolvasni:
1. Jelentkezzen be itt: [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. A **saját tudásbázisok**, jelölje be **nézet kód** a Tudásbázis számára.
    ![Képernyőfelvétel a My tudásbázisok](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. A GenerateAnswer végpontjával kapcsolatos részletek beszerzése.

    ![Képernyőkép a végpont részletei](../media/qnamaker-how-to-metadata-usage/view-code.png)

A végpont adatait is beszerezheti a **beállítások** a Tudásbázis lapján.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer kérelem konfiguráció

A HTTP POST-kérelmet GenerateAnswer hívható meg. Az mintakódot, amely bemutatja, hogyan hívhat meg GenerateAnswer, lásd: a [útmutatóink](../quickstarts/csharp.md). 

A POST-kérés használja:

* Szükséges [URI-paraméterek](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Szükséges [fejléc tulajdonság](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer), `Authorization`, a biztonság
* Szükséges [törzs tulajdonságai](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto). 

A GenerateAnswer URL-cím formátuma a következő: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Állítsa be a HTTP-fejléc tulajdonságot, ne felejtse el `Authorization` értéke az a karakterlánc `EndpointKey` a záró szóköz majd a végpont kulcs található a **beállítások** lap.

Egy példa JSON-törzse hasonlóan néz ki:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer válasz tulajdonságai

A [válasz](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful_query) egy JSON-objektum, például az összes információhoz hozzájuthat a választ, a következő megjelenítéséhez kapcsolja be a beszélgetést, ha elérhető.

```json
{
    "answers": [
        {
            "score": 28.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

## <a name="use-qna-maker-with-a-bot-in-c"></a>A robot a QnA Maker használataC#

A bot framework a QnA Maker tulajdonságok hozzáférést biztosít:

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

A támogatási robot rendelkezik [például](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) ezzel a kóddal.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>A QnA Maker használata a Node.js-ben a robotot

A bot framework a QnA Maker tulajdonságok hozzáférést biztosít:

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.03,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

A támogatási robot rendelkezik [például](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) ezzel a kóddal.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Metaadatok használata egyéni metaadatokat címkék alapján szűri az adott válaszok

Metaadatok hozzáadása lehetővé teszi, hogy a válaszok e metaadatokat címkék alapján szűri. A metaadatok oszlop a következő oszlop hozzáadása a **beállításai** menü. Kiválasztásával a metaadatokat ad hozzá metaadatokat a Tudásbázis **+** ikonra kattintva adja hozzá a metaadatokat pár. Ez a páros egy kulcs és a egy értéke áll.

![Képernyőkép a metaadatok hozzáadása](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>A metaadat-címkéket strictFilters az eredmények szűréséhez

Fontolja meg a felhasználói kérdés "Ha nem zárja be ezt a Szálloda?", ahol a cél implicit az étterem egyik "Paradicsom."

Eredmények csak a "Paradicsom" éttermi szükségesek, mert a metaadatok "Éttermi Name" GenerateAnswer hívásában beállíthatja a szűrőt. A következő példa ezt mutatja be:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Kérdést és választ eredmények beszélgetés környezet használata

A GenerateAnswer adott válasz tartalmazza az egyező kérdést és választ készlet megfelelő metaadat-információkat. Ez az információ az ügyfélalkalmazásban található segítségével tárolja a korábbi beszélgetés használatra kontextusában újabb beszélgetések. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="match-questions-only-by-text"></a>Kérdések, csak megfelelő szöveg szerint

Alapértelmezés szerint a QnA Maker – kérdések és válaszok keres. Ha azt szeretné, csak a kérdések keresgélnie, készítése a választ valamely kérdésre, használja a `RankerType=QuestionOnly` a GenerateAnswer kérelem bejegyzés törzse.

A közzétett kb is kereshet használatával `isTest=false`, vagy a tesztelési kb használatával `isTest=true`.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="next-steps"></a>További lépések

A **közzététel** oldal is nyújt választ létrehozásához szükséges adatok [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) és [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása](./create-knowledge-base.md)
