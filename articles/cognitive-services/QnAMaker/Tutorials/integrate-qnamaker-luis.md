---
title: LUIS és QnAMaker – bot-integráció
titleSuffix: Azure Cognitive Services
description: Mivel a QnA Maker Tudásbázis egyre nagyobb, nehéz lesz egyetlen monolitikus-készletként fenntartani. A Tudásbázis felosztása kisebb, logikai adattömbökbe.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81402723"
---
# <a name="use-a-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>A Tudásbázis kiosztása QnA Maker és LUIS használatával
Mivel a QnA Maker Tudásbázis egyre nagyobb, nehéz lesz egyetlen monolitikus-készletként fenntartani. A Tudásbázis felosztása kisebb, logikai adattömbökbe.

Habár egyértelmű, hogy több tudásbázist is létre kell hozni a QnA Makerban, némi logikára van szükség a beérkező kérdés megfelelő tudásbázisba való átirányításához. Ezt a LUIS használatával teheti meg.

Ez a cikk a robot Framework v3 SDK-t használja. Ha érdekli a bot Framework v4 SDK-verziója ezen információkkal kapcsolatban, tekintse meg a [több Luis-és QnA-modell használatát](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp)ismertető témakört.

## <a name="architecture"></a>Architektúra

![A QnA Maker architektúráját bemutató ábra Language Understanding](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

Az előző ábrán látható, hogy QnA Maker először a LUIS-modellből beérkező kérdés szándékát kapja meg. Ezután QnA Maker ezt a szándékot használja arra, hogy a kérdést a megfelelő QnA Maker tudásbázisba irányítsa.

## <a name="create-a-luis-app"></a>LUIS-alkalmazás létrehozása

1. Jelentkezzen be a [Luis](https://www.luis.ai/) portálra.
1. [Hozzon létre egy alkalmazást](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Adjon hozzá egy szándékot](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) minden QnA Maker tudásbázishoz. A példa hosszúságú kimondott szöveg meg kell felelnie a QnA Maker Tudásbázisban feltett kérdésekre.
1. [A Luis-alkalmazás betanítása](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) és [a Luis-alkalmazás közzététele](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp).
1. A **kezelés** szakaszban jegyezze fel a Luis-alkalmazás azonosítóját, a Luis-végpont kulcsát és az [Egyéni tartománynevet](../../cognitive-services-custom-subdomains.md). Ezekre az értékekre később szüksége lesz.

## <a name="create-qna-maker-knowledge-bases"></a>QnA Maker tudásbázisok létrehozása

1. Jelentkezzen be [QnA Makerba](https://qnamaker.ai).
1. [Hozzon létre](https://www.qnamaker.ai/Create) egy tudásbázist a Luis-alkalmazás minden szándékához.
1. A tudásbázisok tesztelése és közzététele. Amikor közzéteszi mindegyiket, jegyezze fel az azonosítót, az erőforrás nevét (az egyéni altartomány előtt _. azurewebsites.net/qnamaker_), valamint az engedélyezési végpont kulcsát. Ezekre az értékekre később szüksége lesz.

    Ez a cikk azt feltételezi, hogy a tudásbázisok mind ugyanabban az Azure QnA Maker-előfizetésben jönnek létre.

    ![QnA Maker HTTP-kérelem képernyőképe](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Webalkalmazás-robot

1. [Hozzon létre egy "alapszintű" robotot Azure bot Serviceval](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0), amely automatikusan egy Luis-alkalmazást tartalmaz. Válassza ki a C# programozási nyelvet.

1. A webalkalmazás-robot létrehozása után a Azure Portal válassza ki a webalkalmazás-robotot.
1. A Web App bot Service navigáció területén válassza az **Alkalmazásbeállítások**lehetőséget. Ezután görgessen le az elérhető beállítások **Alkalmazásbeállítások** szakaszára.
1. Módosítsa a **LuisAppId** az előző szakaszban létrehozott Luis-alkalmazás értékére. Ezután válassza a **Save** (Mentés) lehetőséget.


## <a name="change-the-code-in-the-basicluisdialogcs-file"></a>A kód módosítása a BasicLuisDialog.cs fájlban
1. A Azure Portal a webalkalmazás robot-navigációjának **robot-kezelés** szakaszában válassza a **Létrehozás**lehetőséget.
2. Válassza az **online Kódszerkesztő megnyitása**lehetőséget. Megnyílik egy új böngésző lap az online szerkesztési környezettel.
3. A **WWWROOT** szakaszban válassza a **párbeszédablakok** könyvtárat, majd nyissa meg a **BasicLuisDialog.cs**.
4. Függőségek hozzáadása a **BasicLuisDialog.cs** fájl elejéhez:

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


6. Adja hozzá a következő osztályt egy HTTP-kérelem elvégzéséhez a QnA Maker szolgáltatáshoz. Figyelje meg, hogy az **engedélyezési** fejléc értéke tartalmazza a szót `EndpointKey`, a szót követő szóközzel. A JSON-eredmény deszerializálása az előző osztályokba történik, és az első választ adja vissza.

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


7. Módosítsa az `BasicLuisDialog` osztályt. Minden LUIS-szándéknak rendelkeznie kell egy **LuisIntent**-vel díszített módszerrel. A díszítés paramétere a tényleges LUIS-cél neve. A dekorált metódus nevének az olvashatóság és a karbantartás érdekében a LUIS-cél _névnek kell_ lennie, de nem feltétlenül kell megegyeznie a tervezéssel vagy a futtatókörnyezettel.

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


## <a name="build-the-bot"></a>A robot létrehozása
1. A Kódszerkesztő alkalmazásban kattintson a jobb gombbal a **Build. cmd**fájlra, majd válassza a **Futtatás a konzolról**lehetőséget.

    ![Képernyőkép a Futtatás konzolról beállításról a kódszerkesztő programban](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. A kód nézet helyére egy olyan Terminálablak kerül, amely a Build folyamatának állapotát és eredményét jeleníti meg.

    ![Képernyőkép a konzol létrehozásáról](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>A robot tesztelése
A Azure Portal a robot teszteléséhez válassza a **tesztelés a webes csevegésben** lehetőséget. Írja be a különböző szándékokból származó üzeneteket a megfelelő Tudásbázis válaszának beolvasásához.

![Képernyőfelvétel a Web Chat tesztről](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis integrálása egy ügynökkel a Power Virtual Agents szolgáltatásban](integrate-with-power-virtual-assistant-fallback-topic.md)
