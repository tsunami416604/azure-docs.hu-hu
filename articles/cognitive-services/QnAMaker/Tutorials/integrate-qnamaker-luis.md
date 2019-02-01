---
title: A LUIS és QnAMaker - robot-integráció
titleSuffix: Azure Cognitive Services
description: A QnA Maker Tudásbázis növekedésével nagy válik nehezen fenntartható, monolitikus egységes és a egy kell a Tudásbázis ossza fel kisebb logikai adattömböket.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 416295b9dc7736d66515ebcbf9caa52053027c85
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55489954"
---
# <a name="use-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>A robot használata a QnA Maker és a LUIS terjeszteni a Tudásbázis
A QnA Maker Tudásbázis növekedésével nagy válik nehezen fenntartható, monolitikus egységes és a egy kell a Tudásbázis ossza fel kisebb logikai adattömböket.

Bár a QnA Maker több tudásbázisok létrehozása egyszerű, szüksége lesz egy logikai irányíthatja a bejövő kérdést a megfelelő tudásbázisba. Ez a LUIS használatával teheti meg.

Ez a cikk a Bot Framework v3 SDK-t használja. Tekintse meg a [Bot Framework cikk](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp), ha érdekli a Bot Framework v4 SDK verziója ezt az információt.

## <a name="architecture"></a>Architektúra

![A QnA Maker luis-architektúra](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

Ebben az esetben QnA Maker először a bejövő kérdés szándéka olvas be egy LUIS-modellnek, és használja a QnA Maker megfelelő Tudásbázis irányítja.

## <a name="create-a-luis-app"></a>A LUIS-alkalmazás létrehozása

1. Jelentkezzen be a [LUIS](https://www.luis.ai/) portálon.
1. [Hozzon létre egy alkalmazást](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Adja hozzá a megjelölésű](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) az egyes QnA Maker Tudásbázis. Példa megcímkézzen meg kell egyeznie a QnA Maker tudásbázisok szereplő kérdéseket.
1. [A LUIS alkalmazás betanításához](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) és [a LUIS alkalmazás közzététele](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp) a LUIS-alkalmazás.
1. Az a **kezelés** részen jegyezze fel a LUIS alkalmazás azonosítója, LUIS végponti kulcs, és régió üzemeltetéséhez. Később szüksége lesz ezekre az értékekre. 

## <a name="create-qna-maker-knowledge-bases"></a>A QnA Maker tudásbázisok létrehozása

1. Jelentkezzen be a [a QnA Maker](https://qnamaker.ai).
1. [Hozzon létre](https://www.qnamaker.ai/Create) egy tudásbázisok egyes leképezés a LUIS alkalmazás számára.
1. Tesztelje, és tegye közzé a tudásbázisok. Ha közzé minden KB, jegyezze fel a Tudásbázis Azonosítóját, a gazdagép (előtt altartomány _.azurewebsites.net/qnamaker_), és az engedélyezési végpont kulcs. Később szüksége lesz ezekre az értékekre. 

    Ez a cikk feltételezi, hogy a Tudásbázis összes jönnek létre az Azure QnA Maker ugyanabban az előfizetésben.

    ![A QnA Maker HTTP-kérelem](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Web app Bot

1. [Hozzon létre egy Web App bot](https://docs.microsoft.com/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample) a LUIS-sablonnal. Válassza ki a 3.x SDK-t és a C# programozási nyelv.

1. Az Azure Portalon, a web app bot létrehozása után válassza ki a web app bot.
1. Válassza ki **Alkalmazásbeállítások** Web app bot service navigációs sávján görgessen le a **Alkalmazásbeállítások** rendelkezésre álló beállítások szakaszában.
1. Módosítsa a **LuisAppId** , az érték az előző szakaszban létrehozott a majd válassza ki a LUIS alkalmazás **mentése**.


## <a name="change-code-in-basicluisdialogcs"></a>BasicLuisDialog.cs a kód módosítása
1. Az a **Bot felügyeleti** szakaszában az Azure Portalon, válassza a web app bot navigáció **hozhat létre**.
2. Válassza ki **online kódszerkesztő megnyitása**. Egy új böngészőlapot nyit meg az online Kódszerkesztő környezetében. 
3. Az a **WWWROOT** szakaszban jelölje be a **párbeszédpanelek** könyvtárat, majd nyissa meg **BasicLuisDialog.cs**.
4. A felső részén adja a függőségeket a **BasicLuisDialog.cs** fájlt:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Luis;
    using Microsoft.Bot.Builder.Luis.Models;
    using Newtonsoft.Json;
    using System.Text;
    ```

5. Adja hozzá az alábbi deszerializálni a QnA Maker válasz osztályok:

    ```csharp
    public class Metadata
    {
        public string name { get; set; }
        public string value { get; set; }
    }

    public class Answer
    {
        public IList<string> questions { get; set; }
        public string answer { get; set; }
        public double score { get; set; }
        public int id { get; set; }
        public string source { get; set; }
        public IList<object> keywords { get; set; }
        public IList<Metadata> metadata { get; set; }
    }

    public class QnAAnswer
    {
        public IList<Answer> answers { get; set; }
    }
    ```


6. Adja hozzá a következő osztályok, hogy a HTTP-kérést a QnA Maker szolgáltatás. Figyelje meg, hogy a **engedélyezési** fejléc értékének magában foglalja a word-, `EndpointKey` egy-egy szóközzel, a következő szót. A JSON-eredményt van deszerializálni az előző osztályokba, és az első választ adja vissza.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceHostName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string hostName, string kbId, string endpointkey)
        {
            qnaServiceHostName = hostName;
            knowledgeBaseId = kbId;
            endpointKey = endpointkey;

        }
        async Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpointKey);

                var response = await client.SendAsync(request);
                return  await response.Content.ReadAsStringAsync();
            }
        }
        public async Task<string> GetAnswer(string question)
        {
            string uri = qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = "{\"question\": \"" + question.Replace("\"","'") +  "\"}";

            var response = await Post(uri, questionJSON);

            var answers = JsonConvert.DeserializeObject<QnAAnswer>(response);
            if (answers.answers.Count > 0)
            {
                return answers.answers[0].answer;
            }
            else
            {
                return "No good match found.";
            }
        }
    }
    ```


7. Módosítsa a BasicLuisDialog osztály. Minden egyes LUIS szándékot rendelkeznie kell egy metódust az kitüntetett **LuisIntent**. Paraméter azonosítására szolgál a tényleges LUIS leképezés neve. A metódus nevét, amely van _kell_ kimenettel, a Karbantarthatóság LUIS leképezés neve, de nem kell azonos tervezési vagy futási időt.  

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all KBs are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_endpointDomain = "my-qnamaker-s0-s";
        
        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_endpointDomain + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_endpointDomain + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            LUIS_appId,
            LUIS_apiKey,
            domain: LUIS_hostRegion)))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            HttpClient client = new HttpClient();
            await this.ShowLuisResult(context, result);
        }

        // HR Intent
        [LuisIntent("HR")]
        public async Task HumanResourcesIntent(IDialogContext context, LuisResult result)
        {
            // Ask the HR knowledge base
            var qnaMakerAnswer = await hrQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }

        // Finance intent
        [LuisIntent("Finance")]
        public async Task FinanceIntent(IDialogContext context, LuisResult result)
        {
            // Ask the finance knowledge base
            var qnaMakerAnswer = await financeQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }
        private async Task ShowLuisResult(IDialogContext context, LuisResult result)
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
    }
    ```


## <a name="build-the-bot"></a>A robot létrehozása
1. A kódszerkesztőben, kattintson a jobb gombbal a `build.cmd` válassza **futtatása konzolról**.

    ![a konzol futtatása](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. A kód nézet megjelenítése a állapotának és eredményeinek megtekintéséhez a build terminálablak cseréli le.

    ![konzol build](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>A robot tesztelése
Az Azure Portalon válassza ki a **tesztelni, webes csevegési** a robot teszteléséhez. Írja be a különböző leképezések lekérni a választ, a kapcsolódó Tudásbázis üzeneteit.

![webes csevegési teszt](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A QnA Maker egy üzleti folytonossági terv létrehozása](../How-To/business-continuity-plan.md)
