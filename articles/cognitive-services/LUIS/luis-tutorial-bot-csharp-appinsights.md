---
title: Application Insights, C# -Luis
titleSuffix: Azure Cognitive Services
description: Ez az oktatóanyag a robotokat és a Language Understanding információt adja hozzá Application Insights telemetria adattároláshoz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/16/2019
ms.author: diberry
ms.openlocfilehash: bc8cf9973ed6889b0820e5ada5565d0541532fa3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560061"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-c"></a>LUIS-eredmények hozzáadása a Application Insights egy robotbólC#

Ez az oktatóanyag a robotokat és a Language Understanding információt adja hozzá [Application Insights](https://azure.microsoft.com/services/application-insights/) telemetria adattároláshoz. Az ilyen adatokat lekérdezheti a Kusto nyelvével vagy Power BI a leképezések elemzéséhez, összesítéséhez és jelentésekhez, valamint a teljes körű, valós idejű entitások megjelenítéséhez. Az elemzés segít annak meghatározásában, ha kell hozzáadása vagy szerkesztése a szándékok és entitások, a LUIS-alkalmazás.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A robot és a Language Understanding-adatértelmezések rögzítése Application Insights
> * Language Understanding-adatApplication Insights lekérdezése

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure bot Service-robot, amely Application Insights engedélyezve lett létrehozva.
* Az előző robot-oktatóanyagból letöltött **[](luis-csharp-tutorial-bf-v4.md)** robot-kód. 
* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Az oktatóanyagban szereplő összes kód elérhető az [Azure-samples Language Understanding GitHub-tárházban](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-csharp-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Application Insights hozzáadása a webalkalmazás-robot projekthez

Jelenleg az Application Insights szolgáltatás a web app bot használt gyűjt a robot telemetriája általános állapotát. Nem gyűjti a LUIS-információkat. 

A LUIS-információk rögzítéséhez a webalkalmazás-robotnak telepítve és konfigurálva kell lennie a **[Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** NuGet csomagnak.  

1. A Visual studióból adja hozzá a függőséget a megoldáshoz. A **megoldáskezelő**kattintson a jobb gombbal a projekt nevére, és válassza a **NuGet-csomagok kezelése..** . lehetőséget. A NuGet Package manager egy telepített csomagok listáját jeleníti meg. 
1. Válassza a **Tallózás** lehetőséget, majd keressen rá a **Microsoft. ApplicationInsights**kifejezésre.
1. Telepítse a csomagot. 

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Rögzíti és továbbítja a LUIS lekérdezés eredményeit az Application Insights

1. Nyissa `LuisHelper.cs` meg a fájlt, és cserélje le a tartalmát a következő kódra. A **LogToApplicationInsights** metódus rögzíti a robotot és a Luis-adatokat, és elküldi Application Insightsként `LUIS`egy nevű nyomkövetési eseményként.

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

## <a name="add-application-insights-instrumentation-key"></a>Application Insights kialakítási kulcs hozzáadása 

Az Application-elemzésekhez való adathozzáadáshoz szükség van a kialakítási kulcsra.

1. A böngészőben a [Azure Portal](https://portal.azure.com)keresse meg a robot **Application Insights** erőforrását. A neve lesz a robot neve, majd a név végén található véletlenszerű karakterek, például `luis-csharp-bot-johnsmithxqowom`:. 
1. A Application Insights erőforrás **Áttekintés** lapján másolja a kialakítási **kulcsot**.
1. A Visual Studióban nyissa meg a **appSettings. JSON** fájlt a robot-projekt gyökerében. Ez a fájl tartalmazza az összes környezeti változót.
1. Adjon hozzá egy új változót `BotDevAppInsightsKey` a kialakítási kulcs értékével. A értékének idézőjelek közé kell esnie. 

## <a name="build-and-start-the-bot"></a>A robot létrehozása és elindítása

1. A Visual Studióban hozza létre és futtassa a robotot. 
1. Indítsa el a robot-emulátort, és nyissa meg a robotot. Ez a [lépés](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) az előző oktatóanyagban van megadva.

1. Kérdezze meg a robot kérdését. Ez a [lépés](luis-csharp-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) az előző oktatóanyagban van megadva.

## <a name="view-luis-entries-in-application-insights"></a>Nézet LUIS bejegyzések az Application insights szolgáltatásban

Nyissa meg az Application Insights a LUIS-bejegyzések megtekintéséhez. Néhány percet is igénybe vehet, hogy az adat megjelenjen a Application Insightsban.

1. A [Azure Portal](https://portal.azure.com)nyissa meg a robot Application Insights erőforrását. 
1. Amikor megnyílik az erőforrás, válassza a **Keresés** lehetőséget, és az utolsó **30 percben** keresse meg az összes adat kifejezést a **nyomkövetési**esemény típusával. Válassza ki a **Luis**nevű nyomkövetést. 
1. A bot és a LUIS információ az **Egyéni tulajdonságok**területen érhető el. 

    ![Az Application Insightsban tárolt LUIS egyéni tulajdonságok áttekintése](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-csharp.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Lekérdezés az Application Insights szándékot, pontszám és utterance (kifejezés)
Application Insights lehetővé teszi az adatlekérdezést a [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) nyelvével, valamint a Power BIba való exportálással. [](https://powerbi.microsoft.com) 

1. Válassza a **napló (elemzés)** lehetőséget. Egy lekérdezési ablak tetején, és a egy tábla ablak, amely alatt megjelenik egy új ablak. Ha a használt adatbázisokat korábban, ezzel az elrendezéssel fokozott tisztában van-e. A lekérdezés az előző szűrt adatait jelöli. A **CustomDimensions** oszlop a bot és a Luis információval rendelkezik.
1. A felső szándék, a pontszám és a Kimondás lekéréséhez adja hozzá a következőt a lekérdezési ablak utolsó `|top...` sora (a sor) fölé:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent_Name)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

1. Futtassa a lekérdezést. Az új oszlopok topIntent, pontszám és utterance (kifejezés) érhetők el. Válassza ki a rendezni kívánt topIntent oszlopot.

Tudjon meg többet a [Kusto lekérdezési nyelvéről](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) , vagy [exportálja az adatait Power BIba](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>További tudnivalók a Bot Frameworkről

Tudjon meg többet [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>További lépések

Egyéb információkat, érdemes hozzáadni az application insights-adatok tartalmazza Alkalmazásazonosító, a verzió azonosítója, a legutóbbi modell dátuma, a legutóbbi train dátuma, a legutóbbi közzététel dátuma. Ezek az értékek lekérhető a végpont URL-címéről (az alkalmazás azonosítója és a verziószáma), vagy egy szerzői API-hívásból, majd a webalkalmazás bot-beállításaiban, majd onnan kihúzva.  

Végpont ugyanahhoz az előfizetéshez egynél több LUIS alkalmazás használ, akkor is tartalmaznia kell az előfizetés-azonosító és a egy tulajdonság arról, hogy egy megosztott kulcsot.

> [!div class="nextstepaction"]
> [További tudnivalók a példa kimondott szöveg](luis-how-to-add-example-utterances.md)
