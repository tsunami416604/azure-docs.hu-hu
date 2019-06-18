---
title: Application InsightsC#
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban hozzáadja az Application Insights telemetriai adatokat tároló robot és a Language Understanding információkat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/16/2019
ms.author: diberry
ms.openlocfilehash: fa7147dd1b5f22ead17a60042c1c35c4b770cd18
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154914"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-c"></a>Az Application Insights származó a robot a LUIS eredmények hozzáadásaC#

Ez az oktatóanyag hozzáad robot és a Language Understanding információkat [Application Insights](https://azure.microsoft.com/services/application-insights/) telemetriai adatok tárolására. Miután az adatokat, kérdezhet le róla a Kusto-nyelv vagy a Power BI segítségével elemezheti, összesítése, és a jelentés a szándékok és entitások, valós idejű utterance (kifejezés). Az elemzés segít annak meghatározásában, ha kell hozzáadása vagy szerkesztése a szándékok és entitások, a LUIS-alkalmazás.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A robot és a Language understanding adatok az Application Insights rögzítése
> * Az Application Insights lekérdezni a Language Understanding data

## <a name="prerequisites"></a>Előfeltételek

* Az Azure bot service robot, engedélyezve van az Application Insights használatával létrehozott.
* Bot kód az előző robot-től letöltött  **[oktatóanyag](luis-csharp-tutorial-bf-v4.md)** . 
* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Ebben az oktatóanyagban kódja megtalálható a [Azure-minták Language Understanding GitHub-adattár](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-csharp-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Az Application Insights hozzáadása a web app bot projekthez

Jelenleg az Application Insights szolgáltatás a web app bot használt gyűjt a robot telemetriája általános állapotát. A LUIS-adatokat nem gyűjt. 

Annak érdekében, hogy rögzítheti a LUIS-információkat, a web app bot van szüksége a **[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** NuGet-csomag telepítését és konfigurálását.  

1. A Visual Studióban válassza a függőség hozzáadása a megoldáshoz. Az a **Megoldáskezelőben**, kattintson a jobb gombbal a projekt nevére, és válassza ki **NuGet-csomagok kezelése...** . A NuGet Package manager egy telepített csomagok listáját jeleníti meg. 
1. Válassza ki **Tallózás** keressen **Microsoft.ApplicationInsights**.
1. Telepítse a csomagot. 

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Rögzíti és továbbítja a LUIS lekérdezés eredményeit az Application Insights

1. Nyissa meg a `LuisHelper.cs` fájlt, és cserélje ki annak tartalmát az alábbira. A **LogToApplicationInsights** metódus a robot és a LUIS-adatok rögzítése, és elküldi azt az Application Insights egy nyomkövetési eseménynek nevű `LUIS`.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    using Microsoft.ApplicationInsights;
    using System.Collections.Generic;
    
    namespace Microsoft.BotBuilderSamples
    {
        public static class LuisHelper
        {
            public static async Task<BookingDetails> ExecuteLuisQuery(IConfiguration configuration, ILogger logger, ITurnContext turnContext, CancellationToken cancellationToken)
            {
                var bookingDetails = new BookingDetails();
    
                try
                {
                    // Create the LUIS settings from configuration.
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]
                    );
    
                    var recognizer = new LuisRecognizer(luisApplication);
    
                    // The actual call to LUIS
                    var recognizerResult = await recognizer.RecognizeAsync(turnContext, cancellationToken);
    
                    LuisHelper.LogToApplicationInsights(configuration, turnContext, recognizerResult);
    
                    var (intent, score) = recognizerResult.GetTopScoringIntent();
                    if (intent == "Book_flight")
                    {
                        // We need to get the result from the LUIS JSON which at every level returns an array.
                        bookingDetails.Destination = recognizerResult.Entities["To"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
                        bookingDetails.Origin = recognizerResult.Entities["From"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
    
                        // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                        // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                        bookingDetails.TravelDate = recognizerResult.Entities["datetime"]?.FirstOrDefault()?["timex"]?.FirstOrDefault()?.ToString().Split('T')[0];
                    }
                }
                catch (Exception e)
                {
                    logger.LogWarning($"LUIS Exception: {e.Message} Check your LUIS configuration.");
                }
    
                return bookingDetails;
            }
            public static void LogToApplicationInsights(IConfiguration configuration, ITurnContext turnContext, RecognizerResult result)
            {
                // Create Application Insights object
                TelemetryClient telemetry = new TelemetryClient();
    
                // Set Application Insights Instrumentation Key from App Settings
                telemetry.InstrumentationKey = configuration["BotDevAppInsightsKey"];
    
                // Collect information to send to Application Insights
                Dictionary<string, string> logProperties = new Dictionary<string, string>();
    
                logProperties.Add("BotConversation", turnContext.Activity.Conversation.Name);
                logProperties.Add("Bot_userId", turnContext.Activity.Conversation.Id);
    
                logProperties.Add("LUIS_query", result.Text);
                logProperties.Add("LUIS_topScoringIntent_Name", result.GetTopScoringIntent().intent);
                logProperties.Add("LUIS_topScoringIntentScore", result.GetTopScoringIntent().score.ToString());
    
    
                // Add entities to collected information
                int i = 1;
                if (result.Entities.Count > 0)
                {
                    foreach (var item in result.Entities)
                    {
                        logProperties.Add("LUIS_entities_" + i++ + "_" + item.Key, item.Value.ToString());
                    }
                }
    
                // Send to Application Insights
                telemetry.TrackTrace("LUIS", ApplicationInsights.DataContracts.SeverityLevel.Information, logProperties);
    
            }
        }
    }
    ```

## <a name="add-application-insights-instrumentation-key"></a>Az Application Insights-eszközkulcs felvétele 

Adatok hozzáadása az application insights, a kialakítási kulcsot kell.

1. Egy böngészőben a a [az Azure portal](https://portal.azure.com), keresse meg a robot **Application Insights** erőforrás. A név lesz a legtöbb a robot neve, majd a véletlenszerű karaktert a név végén `luis-csharp-bot-johnsmithxqowom`. 
1. Az Application Insights-erőforrás a a **áttekintése** lapon, másolja a **kialakítási kulcs**.
1. A Visual Studióban nyissa meg a **appsettings.json** fájlt robot projekt gyökérmappájában. Ebben a fájlban található összes környezeti változót.
1. Adjon hozzá egy új változó `BotDevAppInsightsKey` a kialakítási kulcs értékét. Az érték az idézőjelek között kell lennie. 

## <a name="build-and-start-the-bot"></a>Hozhat létre, és indítsa el a robotot

1. A Visual Studióban hozhat létre, és futtassa a robot. 
1. A robot-emulátor elindítása, és nyissa meg a robot. Ez [lépés](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) az előző oktatóanyagban található.

1. A robot tegyen fel kérdést. Ez [lépés](luis-csharp-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) az előző oktatóanyagban található.

## <a name="view-luis-entries-in-application-insights"></a>Nézet LUIS bejegyzések az Application insights szolgáltatásban

Nyissa meg az Application Insights a LUIS-bejegyzések megtekintéséhez. Az adatai megjelennek az Application Insights pár percet is igénybe vehet.

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a robot Application Insights-erőforrást. 
1. Az erőforrás megnyitása után válassza ki a **keresési** , és keresse meg az összes adat az utolsó **30 perc** az esemény típusú **nyomkövetési**. Válassza ki a nyomkövetés nevű **LUIS**. 
1. A robot és a LUIS információ érhető el **egyéni tulajdonságok**. 

    ![Tekintse át az Application insights szolgáltatásban tárolt LUIS egyéni tulajdonságok](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-csharp.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Lekérdezés az Application Insights szándékot, pontszám és utterance (kifejezés)
Az Application Insights lehetővé teszi az adatok lekérdezéséhez a [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) nyelven, valamint exportálási, hogy [Power BI](https://powerbi.microsoft.com). 

1. Válassza ki **Log (Analytics)** . Egy lekérdezési ablak tetején, és a egy tábla ablak, amely alatt megjelenik egy új ablak. Ha a használt adatbázisokat korábban, ezzel az elrendezéssel fokozott tisztában van-e. A lekérdezés az előző szűrt adatokat jelöli. A **CustomDimensions** oszlopnak a robot és a LUIS-információkat.
1. Is, a felső szándékot, pontszám és utterance (kifejezés), adja hozzá a következő csak az utolsó sort fent (a `|top...` sor) a lekérdezési ablakban:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent_Name)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

1. Futtassa a lekérdezést. Az új oszlopok topIntent, pontszám és utterance (kifejezés) érhetők el. Válassza ki a topIntent oszlopra kattintva rendezheti.

Tudjon meg többet a [Kusto-lekérdezés nyelvi](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) vagy [exportálja az adatokat a Power bi-bA](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>További tudnivalók a Bot Frameworkről

Tudjon meg többet [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>További lépések

Egyéb információkat, érdemes hozzáadni az application insights-adatok tartalmazza Alkalmazásazonosító, a verzió azonosítója, a legutóbbi modell dátuma, a legutóbbi train dátuma, a legutóbbi közzététel dátuma. Ezeket az értékeket is vagy lekérje a végponti URL-cím (az alkalmazás és verzió-azonosító), vagy egy jelentéskészítő API-hívás, majd a web app bot beállítások és onnan kéri le.  

Végpont ugyanahhoz az előfizetéshez egynél több LUIS alkalmazás használ, akkor is tartalmaznia kell az előfizetés-azonosító és a egy tulajdonság arról, hogy egy megosztott kulcsot.

> [!div class="nextstepaction"]
> [További tudnivalók a példa kimondott szöveg](luis-how-to-add-example-utterances.md)
