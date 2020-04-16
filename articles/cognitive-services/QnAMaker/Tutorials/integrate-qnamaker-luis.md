---
title: LUIS és QnAMaker - bot integráció
titleSuffix: Azure Cognitive Services
description: Ahogy a QnA Maker tudásbázisa egyre nagyobblesz, nehéz lesz egyetlen monolitikus készletként fenntartani. A tudásbázis felosztása kisebb, logikai adattömbökre.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b0d28c77966668f919cdf1265f8cc63b4931d5fd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402723"
---
# <a name="use-a-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>A QnA Maker és a LUIS robothasználata a tudásbázis terjesztéséhez
Ahogy a QnA Maker tudásbázisa egyre nagyobblesz, nehéz lesz egyetlen monolitikus készletként fenntartani. A tudásbázis felosztása kisebb, logikai adattömbökre.

Bár a QnA Maker ben egyszerű több tudásbázist létrehozni, a bejövő kérdés megfelelő tudásbázisba való továbbításához valamilyen logikára van szükség. Ezt a LUIS használatával teheti meg.

Ez a cikk a Bot Framework v3 SDK-t használja. Ha érdekli a Bot Framework v4 SDK verziója ezt az információt, [lásd: Több LUIS és QnA modellek használata.](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp)

## <a name="architecture"></a>Architektúra

![A QnA Maker nyelvi megértéssel rendelkező architektúráját megjelenítő grafikus](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

Az előző ábra azt mutatja, hogy a QnA Maker először a bejövő kérdés szándékát kapja egy LUIS-modellből. Ezután a QnA Maker ezt a szándékot használja a kérdés megfelelő QnA Maker tudásbázishoz való továbbítására.

## <a name="create-a-luis-app"></a>LUIS-alkalmazás létrehozása

1. Jelentkezzen be [LUIS](https://www.luis.ai/) a LUIS-portálra.
1. [Hozzon létre egy alkalmazást](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. Minden QnA Maker tudásbázishoz [adjon hozzá egy szándékot.](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) A példa utterances meg kell felelnie a qna maker tudásbázisok kérdéseinek.
1. [A LUIS alkalmazás betanítása](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) és [a LUIS alkalmazás közzététele.](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp)
1. A **Kezelés** szakaszban jegyezze fel a LUIS-alkalmazásazonosítót, a LUIS-végpontkulcsot és [az egyéni tartománynevet.](../../cognitive-services-custom-subdomains.md) Ezekre az értékekre később szüksége lesz.

## <a name="create-qna-maker-knowledge-bases"></a>QnA Maker tudásbázisok létrehozása

1. Jelentkezzen be a [QnA Maker be.](https://qnamaker.ai)
1. [Hozzon létre](https://www.qnamaker.ai/Create) egy tudásbázist a LUIS-alkalmazásban minden egyes leképezéshez.
1. Tesztelje és tegye közzé a tudásbázisokat. Az egyes adatok közzétételekor jegyezze fel az azonosítót, az erőforrás nevét (a _.azurewebsites.net/qnamaker_előtti egyéni altartományt) és az engedélyezési végpontkulcsot. Ezekre az értékekre később szüksége lesz.

    Ez a cikk feltételezi, hogy a tudásbázisok ugyanabban az Azure QnA Maker-előfizetésben jönnek létre.

    ![Képernyőkép a QnA Maker HTTP-kéréséről](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Webalkalmazás-robot

1. [Hozzon létre egy "Basic" robotot az Azure Bot Service szolgáltatással,](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0)amely automatikusan tartalmaz egy LUIS-alkalmazást. Válassza ki a C# programozási nyelvet.

1. Miután létrehozta a webalkalmazás-robotot, az Azure Portalon válassza ki a webalkalmazás-robotot.
1. A webalkalmazás-robotszolgáltatás navigációs sávján válassza az **Alkalmazásbeállítások**lehetőséget. Ezután görgessen le az elérhető beállítások **Alkalmazásbeállítások** szakaszához.
1. Módosítsa a **LuisAppId** az előző szakaszban létrehozott LUIS-alkalmazás értékére. Ezután válassza a **Save** (Mentés) lehetőséget.


## <a name="change-the-code-in-the-basicluisdialogcs-file"></a>A BasicLuisDialog.cs fájlkódjának módosítása
1. Az Azure Portalon a webalkalmazás robotnavigációjának **Robotkezelése** szakaszában válassza a **Build lehetőséget.**
2. Válassza **az Online kódszerkesztő megnyitása**lehetőséget. Megnyílik egy új böngészőlap az online szerkesztési környezettel.
3. A **WWWROOT** szakaszban jelölje ki a **Párbeszédek könyvtárat,** majd nyissa meg **BasicLuisDialog.cs**.
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

5. Adja hozzá a következő osztályokat a QnA Maker válasz deszerializálásához:

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


6. Adja hozzá a következő osztályt, hogy http-kérelmet küldjön a QnA Maker szolgáltatáshoz. Figyelje meg, hogy az **Engedélyezés** fejléc értéke tartalmazza a szót, `EndpointKey`és a szót követő szóközökkel. A JSON-eredmény deszerializálódik az előző osztályokba, és az első választ adja vissza.

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


7. Módosítsa `BasicLuisDialog` az osztályt. Minden LUIS-szándéknak rendelkeznie kell egy **LuisIntent**metódussal. A dekoráció paramétere a tényleges LUIS-leképezés neve. A metódus neve, amely díszített _kell_ lennie a LUIS-leképezés neve olvashatóság és karbantarthatóság, de nem kell ugyanaz a tervezési vagy futásidejű.

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all knowledge bases are created with same Azure service
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
1. A kódszerkesztőben kattintson a jobb gombbal **a build.cmd**elemre, és válassza **a Futtatás a konzolról parancsot.**

    ![Képernyőkép: Futtatás a konzolról beállítása a kódszerkesztőben](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. A kódnézetet egy terminálablak váltja fel, amely a létrehozás előrehaladását és eredményeit mutatja.

    ![Képernyőkép a konzol buildjéről](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Tesztelje a bot
Az Azure Portalon válassza a **Tesztelés a webes csevegésben** lehetőséget a robot teszteléséhez. Írja be a különböző szándékú üzeneteket, hogy a válasz a megfelelő tudásbázisból kapjon.

![Képernyőkép a webes csevegés tesztjéről](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Integrálja tudásbázisát egy ügynökkel a virtuális energiaügynökökben](integrate-with-power-virtual-assistant-fallback-topic.md)
