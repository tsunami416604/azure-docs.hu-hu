---
title: LUIS és QnAMaker - Bot integráció
titleSuffix: Azure Cognitive Services
description: AQNA Maker tudásbázisának nagynövekedésével nehéz lesz egyetlen monolitikus készletként fenntartani, és a tudásbázist kisebb logikai adattömbökre kell osztani.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e1ea234bde96ce84259841bbc592bf6373bc639
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "71802798"
---
# <a name="use-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Használja a robotot a QnA Maker rel és a LUIS-szal a tudásbázis terjesztéséhez
AQNA Maker tudásbázisának nagynövekedésével nehéz lesz egyetlen monolitikus készletként fenntartani, és a tudásbázist kisebb logikai adattömbökre kell osztani.

Bár a QnA Maker ben egyszerű több tudásbázist létrehozni, a bejövő kérdés megfelelő tudásbázishoz való továbbításához valamilyen logikára van szükség. Ezt a LUIS használatával teheti meg.

Ez a cikk a Bot Framework v3 SDK-t használja. Kérjük, olvassa el ezt a [Bot Framework cikket,](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp)ha érdekli a Bot Framework v4 SDK változata ezt az információt.

## <a name="architecture"></a>Architektúra

![QnA Maker nyelvtudás-architektúrával](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

A fenti forgatókönyvben a QnA Maker először a bejövő kérdés szándékát kapja egy LUIS-modellből, majd ezt használja a megfelelő QnA Maker tudásbázisra való átirányításhoz.

## <a name="create-a-luis-app"></a>LUIS-alkalmazás létrehozása

1. Jelentkezzen be [LUIS](https://www.luis.ai/) a LUIS-portálra.
1. [Hozzon létre egy alkalmazást](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. Minden QnA Maker tudásbázishoz [adjon hozzá egy szándékot.](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) A példa utterances meg kell felelnie a qna maker tudásbázisok kérdéseinek.
1. [A LUIS-alkalmazás betanítása](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) és [a LUIS-alkalmazás a LUIS-alkalmazás közzététele.](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp)
1. A **Kezelés** szakaszban jegyezze fel a LUIS-alkalmazásazonosítót, a LUIS-végpontkulcsot és [az egyéni tartománynevet.](../../cognitive-services-custom-subdomains.md) Ezekre az értékekre később szüksége lesz. 

## <a name="create-qna-maker-knowledge-bases"></a>QnA Maker tudásbázisok létrehozása

1. Jelentkezzen be a [QnA Maker be.](https://qnamaker.ai)
1. [Hozzon létre](https://www.qnamaker.ai/Create) egy tudásbázisok minden szándék a LUIS alkalmazásban.
1. Tesztelje és tegye közzé a tudásbázisokat. Az egyes tudásbázisközzétételek során jegyezze fel a kb-azonosítót, az erőforrás nevét (a _.azurewebsites.net/qnamaker_előtti egyéni altartományt) és az engedélyezési végpontkulcsot. Ezekre az értékekre később szüksége lesz. 

    Ez a cikk feltételezi, hogy a kb-ek ugyanabban az Azure QnA Maker-előfizetésben jönnek létre.

    ![QnA Maker HTTP-kérelem](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Webapp Robot

1. [Hozzon létre egy "Basic" Web App bot,](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0) amely automatikusan tartalmazza a LUIS-alkalmazást. Válassza a C# programozási nyelvet.

1. A webalkalmazás-robot létrehozása után az Azure Portalon válassza ki a webalkalmazás-robotot.
1. Válassza **az Alkalmazásbeállítások lehetőséget** a Webapp botszolgáltatás-navigációjában, majd görgessen le az elérhető beállítások **Alkalmazásbeállítások** szakaszába.
1. Módosítsa a **LuisAppId-ot** az előző szakaszban létrehozott LUIS-alkalmazás értékére, majd válassza a **Mentés lehetőséget.**


## <a name="change-code-in-basicluisdialogcs"></a>Kód módosítása a BasicLuisDialog.cs
1. Az Azure Portalon a webalkalmazás robotnavigációjának **Robotkezelése** szakaszában válassza a **Build lehetőséget.**
2. Válassza **az Online kódszerkesztő megnyitása**lehetőséget. Megnyílik egy új böngészőlap az online szerkesztési környezettel. 
3. A **WWWROOT** szakaszban jelölje ki a **Párbeszédek könyvtárat,** majd nyissa meg **a BasicLuisDialog.cs**.
4. Függőségek hozzáadása a **BasicLuisDialog.cs** fájl tetejéhez:

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

5. Adja hozzá az alábbi osztályokat a QnA Maker válasz deszerializálásához:

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


6. Adja hozzá a következő osztályt, hogy http-kérelmet küldjön a QnA Maker szolgáltatáshoz. Figyelje meg, hogy az **Engedélyezés** fejléc értéke tartalmazza a szót, `EndpointKey` és a szó után szó közre van. A JSON-eredmény deszerializálódik az előző osztályokba, és az első választ adja vissza.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceResourceName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string resourceName, string kbId, string endpointkey)
        {
            qnaServiceResourceName = resourceName;
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
            string uri = qnaServiceResourceName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
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


7. Módosítsa a BasicLuisDialog osztályt. Minden LUIS-szándéknak rendelkeznie kell egy **LuisIntent**metódussal. A dekoráció paramétere a tényleges LUIS-leképezés neve. A metódus neve, amely díszített _kell_ lennie a LUIS-leképezés neve olvashatóság és karbantarthatóság, de nem kell azonos a tervezési vagy futási idő.  

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
        static string qnamaker_resourceName = "my-qnamaker-s0-s";
        
        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

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


## <a name="build-the-bot"></a>Építeni a bot
1. A kódszerkesztőben kattintson `build.cmd` a jobb gombbal, és válassza **a Futtatás a konzolról parancsot.**

    ![futtatás konzolról](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. A kódnézet et egy terminálablak váltja fel, amely a létrehozás előrehaladását és eredményeit mutatja.

    ![konzol build](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Tesztelje a bot
Az Azure Portalon válassza a **Tesztelés a webes csevegésben** lehetőséget a robot teszteléséhez. Írja be a különböző szándékú üzeneteket, hogy a válasz a megfelelő tudásbázisból kapjon.

![webes csevegés tesztje](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Üzletmenet-folytonossági terv kidolgozása a QnA Maker szolgáltatáshoz](../How-To/business-continuity-plan.md)
