---
title: Language Understanding Bot C# v4
titleSuffix: Language Understanding - Azure Cognitive Services
description: A C# használatával hozzon létre egy csevegőrobotot integrált nyelvfelismeréssel (LUIS). A robot a Bot Framework 4-es verziója és az Azure Web app bot service épül.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/17/2019
ms.author: diberry
ms.openlocfilehash: f74becc24e5d04cefdd05066b8431946578cc35e
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151053"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Oktatóanyag: Használja a Web App Bot, a Language Understanding használatának engedélyezéseC#

Használat C# language understanding (LUIS) szolgáltatással integrált csevegőrobotot hozhat létre. A robot épül fel az Azure-ral [Web app bot](https://docs.microsoft.com/azure/bot-service/) erőforrás és [Bot Framework version](https://github.com/Microsoft/botbuilder-dotnet) V4.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Webalkalmazás-robot létrehozása. Ez a folyamat egy új LUIS-appot hoz létre az Ön számára.
> * Töltse le a robot-projektet a webes bot service által létrehozott
> * A robot és az emulátor elindítása a helyi számítógépen
> * Kimondottszöveg-eredmények megtekintése a robotban

## <a name="prerequisites"></a>Előfeltételek

* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Web app-robot-erőforrás létrehozása

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Új erőforrás létrehozása** lehetőséget.

1. A keresőmezőben keresse meg, majd válassza ki a **Webalkalmazás-robot** elemet. Kattintson a **Létrehozás** gombra.

1. A **Bot Service** szolgáltatásban adja meg a szükséges adatokat:

    |Beállítás|Cél|Javasolt beállítás|
    |--|--|--|
    |Robot neve|Erőforrás neve|`luis-csharp-bot-` + `<your-name>`, például: `luis-csharp-bot-johnsmith`|
    |Előfizetés|Az előfizetés, amelyben létre szeretné hozni a robotot.|Az Ön elsődleges előfizetése.
    |Erőforráscsoport|Az Azure-erőforrások logikai csoportja|Hozzon létre új csoportot a robothoz használt erőforrások tárolására. A csoport neve legyen: `luis-csharp-bot-resource-group`.|
    |Hely|Azure-régió – Nem kell a LUIS létrehozási vagy közzétételi régiójával azonosnak lennie.|`westus`|
    |Tarifacsomag|Szolgáltatáskérési korlátokhoz és számlázáshoz használható.|Az `F0` az ingyenes szint.
    |App neve|A név lesz az altartomány a robot felhőbeli üzembe helyezésekor (például humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, például: `luis-csharp-bot-johnsmith`|
    |Robotsablon|A Bot Framework beállításai – lásd a következő táblázatot|
    |A LUIS-app helye|Egyeznie kell a LUIS-erőforrás régiójával|`westus`|
    |App service-csomag/hely|Ne változtassa meg a megadott alapértelmezett értéket.|
    |Application Insights|Ne változtassa meg a megadott alapértelmezett értéket.|
    |A Microsoft App ID azonosítója és jelszava|Ne változtassa meg a megadott alapértelmezett értéket.|

1. Az a **Bot sablon**, jelölje be az alábbiakat, majd válassza ki a **kiválasztása** alatt ezek a beállítások gombra:

    |Beállítás|Cél|Kiválasztás|
    |--|--|--|
    |SDK verziója|Bot Framework verziója|**SDK v4**|
    |SDK nyelve|Robot programozási nyelve|**C#**|
    |A robot|Robot típusa|**Alapszintű robot**|
    
1. Kattintson a **Létrehozás** gombra. Ezzel létrehozza a robotszolgáltatást, és üzembe helyezi azt az Azure-ban. A folyamat egyik része egy `luis-csharp-bot-XXXX` nevű LUIS-appot hoz létre. Ez a név a /Azure Bot Service-alkalmazás neve alapján.

    [![Web app bot létrehozása](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    Várjon, amíg a bot service jön létre a folytatás előtt.

## <a name="the-bot-has-a-language-understanding-model"></a>A robot a Language Understanding modellel rendelkezik.

A bot service létrehozását is létrehoz egy új LUIS alkalmazás leképezések és példa kimondott szöveg. A robot szándékleképezéseket biztosít az új LUIS-apphoz a következő szándékok esetén: 

|Alapszintű robot LUIS-szándékai|példa kimondott szöveg|
|--|--|
|Címjegyzék repülési|`Travel to Paris`|
|Mégse|`bye`|
|None|Az app tartományán kívül bármi.|

## <a name="test-the-bot-in-web-chat"></a>A robot webes csevegési tesztelése

1. Miközben továbbra is az új robot, az Azure Portalon, válassza ki a **vizsgálat a webes csevegési**. 
1. Az a **írja be az üzenetet** szövegmezőbe írja be a szöveget `hello`. A robot a bot framework, valamint a Foglalás Párizsba repülőjegyet például adott LUIS-modellnek a példa a lekérdezésekre vonatkozó információkkal válaszol. 

    ![Képernyőkép az Azure Portalon, adja meg a "hello" szöveggel.](./media/bfv4-csharp/ask-bot-question-in-portal-test-in-web-chat.png)

    A robot gyors teszteléshez a tesztelési funkciók is használhatja. További fejezze be a tesztelés, beleértve a hibakeresés, a bot kód letöltése, és használhatja a Visual Studiót. 

## <a name="download-the-web-app-bot-source-code"></a>Töltse le a web app bot forráskód
A webalkalmazás-robot kódjának fejlesztéséhez töltse le a kódot a helyi számítógépre. 

1. Az Azure Portalon a **Robot felügyelete** szakaszban kattintson a **Build** elemre. 

1. Válassza a **Robot forráskódjának letöltse** lehetőséget. 

    [![Töltse le a web app bot forráskódja alapszintű robot](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Amikor a felugró párbeszédpanel kéri **Alkalmazásbeállítások belefoglalása a letöltött zip-fájlban?** válassza **Igen**.

1. Tömörített forráskód esetén az üzenet tartalmazza a kód letöltéséhez szükséges hivatkozást. Kattintson a hivatkozásra. 

1. Mentse a .zip-fájlt a helyi számítógépére, és bontsa ki a fájlokat. Nyissa meg a projektet a Visual Studio használatával. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Tekintse át az utterance (kifejezés) küldeni a LUIS és válasz kódja

1. Nyissa meg a **LuisHelper.cs** fájlt. A rendszer ezen a ponton küldi a robotban megadott felhasználói kimondott szöveget a LUIS-hoz. A LUIS válasz, a metódus egy **BookDetails** objektum. A saját robot létrehozásakor is készítsen a saját objektum visszaadása a LUIS a részleteket. 


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
        }
    }
    ```

1. Nyissa meg **BookingDetails.cs** hogyan az objektum kivonatolja a LUIS információk megtekintéséhez. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    namespace Microsoft.BotBuilderSamples
    {
        public class BookingDetails
        {
            public string Destination { get; set; }
    
            public string Origin { get; set; }
    
            public string TravelDate { get; set; }
        }
    }
    ```

1. Nyissa meg **párbeszédpanelek -> BookingDialog.cs** megérteni, hogyan a BookingDetails objektum a beszélgetés folyamat kezelésére szolgál. Utazás részletei a rendszer felkéri a lépéseket, majd a teljes foglalási megerősítette, és végül ismétlődik a felhasználó számára. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;
    
    namespace Microsoft.BotBuilderSamples.Dialogs
    {
        public class BookingDialog : CancelAndHelpDialog
        {
            public BookingDialog()
                : base(nameof(BookingDialog))
            {
                AddDialog(new TextPrompt(nameof(TextPrompt)));
                AddDialog(new ConfirmPrompt(nameof(ConfirmPrompt)));
                AddDialog(new DateResolverDialog());
                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    DestinationStepAsync,
                    OriginStepAsync,
                    TravelDateStepAsync,
                    ConfirmStepAsync,
                    FinalStepAsync,
                }));
    
                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }
    
            private async Task<DialogTurnResult> DestinationStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                if (bookingDetails.Destination == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where would you like to travel to?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Destination, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> OriginStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Destination = (string)stepContext.Result;
    
                if (bookingDetails.Origin == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where are you traveling from?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Origin, cancellationToken);
                }
            }
            private async Task<DialogTurnResult> TravelDateStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Origin = (string)stepContext.Result;
    
                if (bookingDetails.TravelDate == null || IsAmbiguous(bookingDetails.TravelDate))
                {
                    return await stepContext.BeginDialogAsync(nameof(DateResolverDialog), bookingDetails.TravelDate, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.TravelDate, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> ConfirmStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.TravelDate = (string)stepContext.Result;
    
                var msg = $"Please confirm, I have you traveling to: {bookingDetails.Destination} from: {bookingDetails.Origin} on: {bookingDetails.TravelDate}";
    
                return await stepContext.PromptAsync(nameof(ConfirmPrompt), new PromptOptions { Prompt = MessageFactory.Text(msg) }, cancellationToken);
            }
    
            private async Task<DialogTurnResult> FinalStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if ((bool)stepContext.Result)
                {
                    var bookingDetails = (BookingDetails)stepContext.Options;
    
                    return await stepContext.EndDialogAsync(bookingDetails, cancellationToken);
                }
                else
                {
                    return await stepContext.EndDialogAsync(null, cancellationToken);
                }
            }
    
            private static bool IsAmbiguous(string timex)
            {
                var timexProperty = new TimexProperty(timex);
                return !timexProperty.Types.Contains(Constants.TimexTypes.Definite);
            }
        }
    }
    ```


## <a name="start-the-bot-code-in-visual-studio"></a>Indítsa el a robot kódot a Visual Studióban

Indítsa el a robotot a Visual Studióban. Ekkor megnyílik egy böngészőablak a webalkalmazás-robot webhelyével a `http://localhost:3978/` címen. A kezdőlap robotjait információkat jeleníti meg.

![A kezdőlap robotjait információkat jeleníti meg.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>A robot teszteléséhez a robot emulator használata

1. A robot emulátor kezdődik, és válassza ki **nyissa meg a robot**.
1. Az a **nyissa meg a robot** felugró párbeszédpanel, írja be a robot URL-CÍMÉT, például `http://localhost:3978/api/messages`. A `/api/messages` útvonal a robot a webcímet.
1. Adja meg a **Microsoft App ID** és **Microsoft App jelszó**, található az a **appsettings.json** fájlt a letöltött bot kód gyökerében.

    Igény szerint hozhat létre egy új robot, konfigurációs, és másolja a `appId` és `appPassword` származó a **appsettings.json** fájlt a Visual Studio-projektben a robot. A robot konfigurációs fájl neve megegyezik a robot neve kell lennie. 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from appsettings.json>",
                "appPassword": "<appPassword from appsettings.json>",
                "endpoint": "http://localhost:3978/api/messages",
                "id": "<don't change this value>",
                "name": "http://localhost:3978/api/messages"
            }
        ],
        "padlock": "",
        "version": "2.0",
        "overrides": null,
        "path": "<local path to .bot file>"
    }
    ```

1. A robot emulátorban, adja meg a `Hello` és az alapszintű robot, a kapott ugyanazt a választ kaphat a **vizsgálat a webes csevegési**.

    [![Alapszintű robot válasz emulátorban](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Kérdés feltevése robot számára a címjegyzék repülési célt

1. A robot emulátorban repülőjáratra repülőjegyet írja be a következő utterance (kifejezés): 

    ```bot
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    A robot-emulátor megerősítését kéri. 

1. Válassza ki **Igen**. A robot fűzi hozzá a műveletek összegzését. 
1. A robot-emulátor a naplóból, válassza ki a tartalmazó sort `Luis Trace`. Ez megjeleníti a JSON-válasz a LUIS a leképezés és entitásai az utterance (kifejezés).

    [![Alapszintű robot válasz emulátorban](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

## <a name="learn-more-about-the-web-app-bot-and-framework"></a>További információ a Web App Bot és keretrendszer

Az Azure Bot Service a Bot Framework SDK-t használja. További információk az SDK-ról és a Bot Frameworkről:

* Az [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4 dokumentációja
* [Bot Builder minták](https://github.com/Microsoft/botbuilder-samples)
* [Bot Builder C# SDK](https://github.com/Microsoft/botbuilder-dotnet)
* [Bot Builder eszközök](https://github.com/Microsoft/botbuilder-tools):

## <a name="next-steps"></a>További lépések

Még több [minták](https://github.com/microsoft/botframework-solutions) a természetes nyelvi robotokat. 

> [!div class="nextstepaction"]
> [Egy egyéni tulajdonosnév-tartomány Language Understanding alkalmazás készítése](luis-quickstart-intents-only.md)
