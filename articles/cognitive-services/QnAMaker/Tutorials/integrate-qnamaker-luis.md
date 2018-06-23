---
title: Kérdések és válaszok Maker és LUIS - Microsoft kognitív szolgáltatások integrálása |} Microsoft Docs
titleSuffix: Azure
description: kérdések és válaszok Maker és LUIS integrálásával részletes oktatóanyaga
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 0a0eeb3815b793ed81f60b2b239bc459e5574788
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348691"
---
# <a name="integrate-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Kérdések és válaszok Maker és LUIS terjeszteni a Tudásbázis integrálása
A kérdések és válaszok készítő Tudásbázis növekedésével nagy lesz nehéz fenntartani egységes egységes, és a Tudásbázis ossza fel kisebb logikai adattömbök szükség van.

Is hozzon létre több Tudásbázis körrel a kérdések és válaszok készítő magától értetődő, szüksége lesz a bejövő kérdés útvonalat a megfelelő Tudásbázis néhány programot. Ehhez LUIS használatával.

## <a name="architecture"></a>Architektúra

![Kérdések és válaszok készítő luis architektúrája](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

A fenti esetben kérdések és válaszok készítő először lekérdezi a célt a bejövő kérdés LUIS modellből, és használja, amely a megfelelő kérdések és válaszok készítő Tudásbázis irányítja.

## <a name="prerequisites"></a>Előfeltételek
- Jelentkezzen be a [LUIS](https://www.luis.ai/) portal és [hozzon létre egy alkalmazást](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app).
- [Adja hozzá a leképezések](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/add-intents) a forgatókönyv szerint.
- [Vonat](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-train) és [közzététele](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/publishapp) az LUIS alkalmazást.
- Jelentkezzen be [kérdések és válaszok készítő](https://qnamaker.ai) és [hozzon létre]() adatbázisok a forgatókönyv szerint.
- [Teszt]() és [közzététele]() a Tudásbázis alapjait.

## <a name="qna-maker--luis-bot"></a>Kérdések és válaszok készítő + LUIS Botot
1. Először hozzon létre egy webalkalmazás botot LUIS sablonnal, csatolja az előbb létrehozott LUIS alkalmazással, és módosítsa a leképezések. Tekintse meg a részletes lépéseket [Itt](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample).

2. Adja a függőségeket, a más függőségekkel rendelkező a fájl elejéhez:

    ```
    using RestSharp;
    using System.Collections.Generic;
    using Newtonsoft.Json;
    ```
3. Adja hozzá az alábbi osztály a kérdések és válaszok készítő szolgáltatás hívása:

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

3. Ugrás a https://qnamaker.ai -> saját Tudásbázis körrel nézet kódját, a megfelelő Tudásbázis ->. Szerezze be a következő adatokat:

    ![Kérdések és válaszok készítő HTTP-kérelem](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

4. A QnAMakerService osztály példányosítható a Tudásbázis körrel mindegyikéhez:
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

## <a name="build-the-bot"></a>A botot összeállítása
1. A kód-szerkesztőben kattintson a jobb gombbal a `build.cmd` válassza **futtatása konzolról**.

    ![Futtassa a konzolról](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. A kód nézetre egy terminálablakot, állapotának és eredményeinek a build megjelenítő helyére.

    ![konzol build](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>A botot tesztelése
Válassza ki az Azure-portálon **webes Csevegés tesztelése** a botot teszteléséhez. Írja be a válasz lekérése a megfelelő Tudásbázis különböző leképezések üzeneteit.

![a webteszt Csevegés](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kérdések és válaszok készítő hozzon létre egy üzletmenet folytonosságát biztosító terve](../How-To/business-continuity-plan.md)
