---
title: Metaadatok generateanswer API-val – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker lehetővé teszi, hogy a kérdés-válasz készletekhez kulcs/érték párok formájában metaadatokat adjon hozzá. Az eredményeket felhasználói lekérdezésekre szűrheti, és további információkat tárolhat, amelyek felhasználhatók a nyomon követési beszélgetéseksorán.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: diberry
ms.openlocfilehash: 9beb6dbbba1c5855b8bfa97fc02f50aa59225d78
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474851"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Válasz beszerezése a GenerateAnswer API-val és metaadatokkal

A felhasználó kérdésére adott előre jelzett válasz lehívásához használja a GenerateAnswer API-t. Tudásbázis közzétételekor az API használatával kapcsolatos információkat a Közzététel lapon **láthatja.** Az API-t úgy is konfigurálhatja, hogy a válaszokat metaadat-címkék alapján szűrje, és tesztelje a tudásbázist a végpontról a tesztlekérdezési karakterlánc paraméterrel.

A QnA Maker lehetővé teszi, hogy a kérdések és válaszok készleteihez kulcs- és értékpárok formájában metaadatokat adjon hozzá. Ezt az információt arra használhatja, hogy az eredményeket felhasználói lekérdezésekre szűrje, és további információkat tároljon, amelyek felhasználhatók a nyomon követési beszélgetésekben. További információ: [Tudásbázis](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Kérdések és válaszok tárolása QnA entitással

Fontos megérteni, hogy a QnA Maker hogyan tárolja a kérdés- és válaszadatokat. A következő ábra egy QnA entitást mutat be:

![QnA entitás illusztrációja](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Minden QnA entitás egyedi és állandó azonosítóval rendelkezik. Az azonosító segítségével frissítéseket egy adott QnA entitás.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Válaszelőrejelzések beszereznie a GenerateAnswer API-val

A [GenerateAnswer API-t](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) használja a robotban vagy az alkalmazásban a tudásbázis lekérdezéséhez egy felhasználói kérdéssel, hogy a lehető legjobban illeszkedjen a kérdés- és válaszkészletekből.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Közzététel a GenerateAnswer végpont leéséhez

Miután közzétette a tudásbázist, akár a [QnA Maker portálról,](https://www.qnamaker.ai)akár az [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)használatával, beszerezheti a GenerateAnswer végpont részleteit.

A végpont részleteinek beszerezése:
1. Jelentkezzen be [https://www.qnamaker.ai](https://www.qnamaker.ai)a ikonra.
1. A **Saját tudásbázisban**válassza a **Kód megtekintése** lehetőséget a tudásbázisához.
    ![Képernyőkép a Saját tudásbázisomról](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. A GenerateAnswer végpont részleteinek beszerezése.

    ![Képernyőkép a végpont részleteiről](../media/qnamaker-how-to-metadata-usage/view-code.png)

A végpont adatait a tudásbázis **Beállítások** lapján is beszerezheti.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer-kérelem konfigurációja

A GenerateAnswer hívása HTTP POST-kérelemmel. A GenerateAnswer hívását bemutató mintakódot a [rövid útmutatóban tanévenként láthatja el.](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base)

A POST kérelem a következőket használja:

* Szükséges [URI-paraméterek](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Kötelező fejléctulajdonság `Authorization`, , biztonságesetén
* Szükséges [testtulajdonságok](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto).

A GenerateAnswer URL formátuma a következő:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Ne felejtse el beállítani `Authorization` a HTTP fejléc `EndpointKey` tulajdonságát a záró szóközrel rendelkező karakterlánc értékével, majd a **Beállítások** lapon található végpontkulcsot.

Egy példa JSON test néz ki:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

További információ a [rankerType típusról.](../concepts/best-practices.md#choosing-ranker-type)

Az előző JSON csak olyan válaszokat kért, amelyek 30%-os vagy a küszöbérték felettiek.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer válasz tulajdonságai

A [válasz](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) egy JSON-objektum, amely tartalmazza a válasz megjelenítéséhez szükséges összes információt, és a beszélgetés következő fordulatát, ha rendelkezésre áll.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
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

Az előző JSON 38,5%-os pontszámmal válaszolt.

## <a name="use-qna-maker-with-a-bot-in-c"></a>A QnA Maker használata c-ben lévő robottal #

A bot keretrendszer hozzáférést biztosít a QnA Maker tulajdonságait a getAnswer API:The bot framework provides access to the QnA Maker's properties with the [getAnswer API:](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__)

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

Az előző JSON csak olyan válaszokat kért, amelyek 30%-os vagy a küszöbérték felettiek.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Használja a QnA Maker-t egy robottal a Node.js-ben

A bot keretrendszer hozzáférést biztosít a QnA Maker tulajdonságait a getAnswer API:The bot framework provides access to the QnA Maker's properties with the [getAnswer API:](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-)

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

Az előző JSON csak olyan válaszokat kért, amelyek 30%-os vagy a küszöbérték felettiek.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>A válaszok szűrése metaadatok használatával egyéni metaadat-címkék szerint

A metaadatok hozzáadásával a válaszokat ezek a metaadat-címkék alapján szűrheti. Adja hozzá a metaadatoszlopot a **Nézet beállításai** menüből. Metaadatok hozzáadása a tudásbázishoz a **+** metaadat-pár hozzáadásával a metaadat-ikon kiválasztásával. Ez a pár egy kulcsból és egy értékből áll.

![Képernyőkép: metaadatok hozzáadása](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Eredmények szűrése a metaadat-címkék szigorúszűrőivel

Tekintsük a "Mikor zárja be ezt a szállodát?" felhasználói kérdést, ahol a szándék a "Paradise" étteremre vonatkozik.

Mivel az eredmények csak a "Paradise" étteremhez szükségesek, a GenerateAnswer hívásban szűrőt állíthat be az "Étterem neve" metaadatokon. A következő példa a következőket mutatja be:

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

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>A kérdések és megválaszolások eredményeinek használata a beszélgetési környezet megtartásához

A GenerateAnswer válasz tartalmazza az egyező kérdés- és válaszkészlet megfelelő metaadat-adatait. Ezt az információt az ügyfélalkalmazásban használhatja az előző beszélgetés környezetének tárolására későbbi beszélgetésekhez.

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

## <a name="match-questions-only-by-text"></a>Csak kérdések egyeztetése, szöveg szerint

Alapértelmezés szerint a QnA Maker kérdések és válaszok között keres. Ha csak a kérdések között szeretne keresni, hogy `RankerType=QuestionOnly` választ hozzon létre, használja a GenerateAnswer kérés POST törzsében lévő teste.

Kereshet a közzétett kb, `isTest=false`a használatával, vagy `isTest=true`a teszt kb a .

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>Gyakori HTTP-hibák

|Kód|Magyarázat|
|:--|--|
|2xx|Sikeres|
|400|A kérelem paraméterei helytelenek, ami azt jelenti, hogy a szükséges paraméterek hiányoznak, hibásak vagy túl nagyok|
|400|A kérelem teste helytelen, ami azt jelenti, hogy a JSON hiányzik, hibás vagy túl nagy|
|401|Érvénytelen kulcs|
|403|Tiltott - nem rendelkezik megfelelő engedélyekkel|
|404|KB nem létezik|
|410|Ez az API elavult, és már nem érhető el|

## <a name="next-steps"></a>További lépések

A **Közzététel** lap a Postman vagy a cURL segítségével is tartalmaz információkat a [válasz létrehozásához.](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

> [!div class="nextstepaction"]
> [Tudásbázisrobot létrehozása](../tutorials/integrate-qnamaker-luis.md)
