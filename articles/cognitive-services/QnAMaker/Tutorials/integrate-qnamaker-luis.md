---
title: A QnA Maker és a LUIS - a Microsoft Cognitive Services integrálása |} A Microsoft Docs
titleSuffix: Azure
description: részletes útmutató a QnA Maker és a LUIS integrálása
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 18eae69867dc9774f63b11c762b22df4595bdce6
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43781747"
---
# <a name="integrate-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>A QnA Maker és terjeszteni a Tudásbázis LUIS integrálása
A QnA Maker Tudásbázis növekedésével nagy válik nehezen fenntartható, monolitikus egységes és a egy kell a Tudásbázis ossza fel kisebb logikai adattömböket.

Bár a QnA Maker több tudásbázisok létrehozása egyszerű, szüksége lesz egy logikai irányíthatja a bejövő kérdést a megfelelő tudásbázisba. Ez a LUIS használatával teheti meg.

## <a name="architecture"></a>Architektúra

![A QnA Maker luis-architektúra](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

Ebben az esetben QnA Maker először a bejövő kérdés szándéka olvas be egy LUIS-modellnek, és használja a QnA Maker megfelelő Tudásbázis irányítja.

## <a name="prerequisites"></a>Előfeltételek
- Jelentkezzen be a [LUIS](https://www.luis.ai/) portál és [hozzon létre egy alkalmazást](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app).
- [Leképezések hozzáadása](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/add-intents) forgatókönyvnek megfelelően.
- [Train](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-train) és [közzététele](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/publishapp) a LUIS-alkalmazás.
- Jelentkezzen be [QnA Maker](https://qnamaker.ai) és [létrehozása](https://www.qnamaker.ai/Create) tudásbázisok forgatókönyvnek megfelelően.
- Tesztelje, és tegye közzé a tudásbázisok.

## <a name="qna-maker--luis-bot"></a>A QnA Maker + robot a LUIS
1. Először hozzon létre egy Web App-robot a LUIS-sablonnal, a fent létrehozott LUIS alkalmazás Összekapcsoláshoz, majd módosítsa a szándék fog vonatkozni. Részletes lépések a [Itt](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample).

2. Adja a függőségeket, a más függőségekkel a fájl elejéhez:

    ```
    using RestSharp;
    using System.Collections.Generic;
    using Newtonsoft.Json;
    ```
3. Adja hozzá az alábbi osztály számára a QnA Maker szolgáltatás hívása:

    ```
        /// <summary>
        /// QnAMakerService is a wrapper over the QnA Maker REST endpoint
        /// </summary>
        [Serializable]
        public class QnAMakerService
        {
            private string qnaServiceHostName;
            private string knowledgeBaseId;
            private string endpointKey;
    
            /// <summary>
            /// Initialize a particular endpoint with it's details
            /// </summary>
            /// <param name="hostName">Hostname of the endpoint</param>
            /// <param name="kbId">Knowledge base ID</param>
            /// <param name="ek">Endpoint Key</param>
            public QnAMakerService(string hostName, string kbId, string ek)
            {
                qnaServiceHostName = hostName;
                knowledgeBaseId = kbId;
                endpointKey = ek;
            }
    
            /// <summary>
            /// Call the QnA Maker endpoint and get a response
            /// </summary>
            /// <param name="query">User question</param>
            /// <returns></returns>
            public string GetAnswer(string query)
            {
                var client = new RestClient( qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer");
                var request = new RestRequest(Method.POST);
                request.AddHeader("authorization", "EndpointKey " + endpointKey);
                request.AddHeader("content-type", "application/json");
                request.AddParameter("application/json", "{\"question\": \"" + query + "\"}", ParameterType.RequestBody);
                IRestResponse response = client.Execute(request);
    
                // Deserialize the response JSON
                QnAAnswer answer = JsonConvert.DeserializeObject<QnAAnswer>(response.Content);
    
                // Return the answer if present
                if (answer.answers.Count > 0)
                    return answer.answers[0].answer;
                else
                    return "No good match found.";
            }
        }
    
        /* START - QnA Maker Response Class */
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
        /* END - QnA Maker Response Class */
    ```

3. Lépjen a https://qnamaker.ai -> saját tudásbázisok nézet kódját, a kapcsolódó Tudásbázis ->. Kérje le a következő információkat:

    ![A QnA Maker HTTP-kérelem](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

4. A QnAMakerService osztály példányosítható a tudásbázisok mindegyike esetében:
    ```
            // Instantiate the knowledge bases
            public QnAMakerService hrQnAService = new QnAMakerService("https://hrkb.azurewebsites.net", "<HR knowledge base id>", "<HR endpoint key>");
            public QnAMakerService payrollQnAService = new QnAMakerService("https://payrollkb.azurewebsites.net", "<Payroll knowledge base id>", "<Payroll endpoint key>");
            public QnAMakerService financeQnAService = new QnAMakerService("https://financekb.azurewebsites.net", "<Finance knowledge base id>", "<Finance endpoint key>");
    ```

5. A megfelelő Tudásbázis kérjen a célt.
    ```
            // HR Intent
            [LuisIntent("HR")]
            public async Task CancelIntent(IDialogContext context, LuisResult result)
            {
                // Ask the HR knowledge base
                await context.PostAsync(hrQnAService.GetAnswer(result.Query));
            }
    
            // Payroll intent
            [LuisIntent("Payroll")]
            public async Task GreetingIntent(IDialogContext context, LuisResult result)
            {
                // Ask the payroll knowledge base
                await context.PostAsync(payrollQnAService.GetAnswer(result.Query));
            }
    
            // Finance intent
            [LuisIntent("Finance")]
            public async Task HelpIntent(IDialogContext context, LuisResult result)
            {
                // Ask the finance knowledge base
                await context.PostAsync(financeQnAService.GetAnswer(result.Query));
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
