---
title: Language Understanding bot C# v4
titleSuffix: Azure Cognitive Services
description: A C# használatával hozzon létre egy csevegőrobotot integrált nyelvfelismeréssel (LUIS). A robot a robot Framework 4-es verziójával és az Azure Web App bot Service-szel készült.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: diberry
ms.openlocfilehash: c78359920ebc5faab2e0a678a901bcb8581a4e45
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207332"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Oktatóanyag: Webalkalmazás-robot használata Language Understanding használatávalC#

A C# használatával a Language Understanding (Luis) segítségével integrált csevegési robotot hozhat létre. A robot az Azure [Web App bot](https://docs.microsoft.com/azure/bot-service/) Resource és a [bot Framework](https://github.com/Microsoft/botbuilder-dotnet) v4-es verziójával készült.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Webalkalmazás-robot létrehozása. Ez a folyamat egy új LUIS-appot hoz létre az Ön számára.
> * A web bot Service által létrehozott robot-projekt letöltése
> * A robot és az emulátor elindítása a helyi számítógépen
> * Kimondottszöveg-eredmények megtekintése a robotban

## <a name="prerequisites"></a>Előfeltételek

* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Webalkalmazás-robot erőforrás létrehozása

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
    |App Service-csomag/hely|Ne módosítsa a megadott alapértelmezett értéket.|
    |Application Insights|Ne módosítsa a megadott alapértelmezett értéket.|
    |Microsoft-alkalmazás azonosítója és jelszava|Ne módosítsa a megadott alapértelmezett értéket.|

1. A **bot**-sablonban válassza ki a következőt, majd a beállítások területen válassza a **kiválasztás** gombot:

    |Beállítás|Cél|Kiválasztás|
    |--|--|--|
    |SDK verziója|Bot Framework verziója|**SDK v4**|
    |SDK nyelve|Robot programozási nyelve|**C#**|
    |Bot|Robot típusa|**Alapszintű robot**|
    
1. Kattintson a **Létrehozás** gombra. Ezzel létrehozza a robotszolgáltatást, és üzembe helyezi azt az Azure-ban. A folyamat egyik része egy `luis-csharp-bot-XXXX` nevű LUIS-appot hoz létre. Ez a név a/Azure bot Service-alkalmazás nevén alapul.

    [![Webalkalmazás robotjának létrehozása](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    A folytatás előtt várjon, amíg létrejött a robot szolgáltatás.

## <a name="the-bot-has-a-language-understanding-model"></a>A robot Language Understanding modellt tartalmaz

A bot Service létrehozási folyamata egy új LUIS-alkalmazást is létrehoz a szándékokkal és példa hosszúságú kimondott szöveg. A robot szándékleképezéseket biztosít az új LUIS-apphoz a következő szándékok esetén: 

|Alapszintű robot LUIS-szándékai|példa kimondott szöveg|
|--|--|
|Repülőjegy|`Travel to Paris`|
|Mégse|`bye`|
|Nincsenek|Az app tartományán kívül bármi.|

## <a name="test-the-bot-in-web-chat"></a>A robot webes csevegési tesztelése

1. Miközben az új robot Azure Portal továbbra is elérhető, válassza a **tesztelés webes csevegésben**lehetőséget. 
1. Az írja be az **üzenet** szövegmezőbe írja be a `hello`szöveget. A robot a bot-keretrendszerre vonatkozó információkkal válaszol, valamint az adott LUIS-modell lekérdezéseit is, például egy repülés Párizsba való foglalását. 

    ![A Azure Portal képernyőképe a "Hello" szöveg beírásával.](./media/bfv4-csharp/ask-bot-question-in-portal-test-in-web-chat.png)

    A tesztelési funkció használatával gyorsan tesztelheti a robotot. A teljes teszteléshez, beleértve a hibakeresést, töltse le a robot kódját, és használja a Visual studiót. 

## <a name="download-the-web-app-bot-source-code"></a>A webalkalmazás robot-forráskódjának letöltése
A webalkalmazás-robot kódjának fejlesztéséhez töltse le a kódot a helyi számítógépre. 

1. Az Azure Portalon a **Robot felügyelete** szakaszban kattintson a **Build** elemre. 

1. Válassza a **Robot forráskódjának letöltse** lehetőséget. 

    [![Webalkalmazás-robot forráskódjának letöltése alapszintű robothoz](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Ha az előugró ablak **a letöltött zip-fájlban is**megkéri az Alkalmazásbeállítások megadását, válassza az **Igen**lehetőséget.

1. Tömörített forráskód esetén az üzenet tartalmazza a kód letöltéséhez szükséges hivatkozást. Kattintson a hivatkozásra. 

1. Mentse a .zip-fájlt a helyi számítógépére, és bontsa ki a fájlokat. Nyissa meg a projektet a Visual Studióval. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Tekintse át a kódot, hogy kinyerje a LUIS-t és a választ

1. Nyissa meg a **LuisHelper.cs** fájlt. A rendszer ezen a ponton küldi a robotban megadott felhasználói kimondott szöveget a LUIS-hoz. A LUIS válaszát a metódus **BookDetails** objektumként adja vissza. Saját robot létrehozásakor létre kell hoznia egy saját objektumot is, amely a LUIS-ből származó részleteket adja vissza. 


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

1. A **BookingDetails.cs** megnyitásával megtekintheti, hogy az objektum hogyan olvassa el a Luis-információkat. 

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

1. Párbeszédpanelek megnyitása **– > BookingDialog.cs** , hogy megtudja, hogyan használják a BookingDetails objektumot a beszélgetési folyamat kezeléséhez. Az utazási adatokat a rendszer a következő lépésekben kérdezi le: a teljes foglalás megerősítve, végül pedig visszakerül a felhasználóhoz. 

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


## <a name="start-the-bot-code-in-visual-studio"></a>A robot kódjának elindítása a Visual Studióban

Indítsa el a robotot a Visual Studióban. Ekkor megnyílik egy böngészőablak a webalkalmazás-robot webhelyével a `http://localhost:3978/` címen. A kezdőlapon megjelennek a robottal kapcsolatos információk.

![A kezdőlapon megjelennek a robottal kapcsolatos információk.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>A bot-emulátor használata a robot teszteléséhez

1. Indítsa el a robot-emulátort, és válassza a **robot megnyitása**lehetőséget.
1. A **bot megnyitása** előugró ablakban adja meg a robot URL-címét, például `http://localhost:3978/api/messages`:. Az `/api/messages` útvonal a robot webes címe.
1. Adja meg a **Microsoft-alkalmazás azonosítóját** és a Microsoft- **alkalmazás jelszavát**, amely a letöltött robot-kód gyökerében található **appSettings. JSON** fájlban található.

    Szükség esetén létrehozhat egy új bot-konfigurációt, és `appId` átmásolhatja `appPassword` a **appSettings. JSON** fájlt a robothoz tartozó Visual Studio-projektből. A bot-konfigurációs fájl nevének meg kell egyeznie a robot nevével. 

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

1. A bot-emulátorban adja `Hello` meg és kérje le ugyanazt a választ az alapszintű robotra, mint amit a **teszt webes csevegésben**kapott.

    [![Alapszintű bot-válasz az emulátorban](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Kérdezze meg a robotot a könyv repülési szándékáról

1. A robot-emulátorban foglaljon le egy repülőjáratot a következő kifejezés beírásával: 

    ```console
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    A robot-emulátor megkéri a megerősítést. 

1. Válassza az **Igen**lehetőséget. A robot a műveleteinek összegzésével válaszol. 
1. A robot-emulátor naplójából válassza ki a sort, amely tartalmazza `Luis Trace`a elemet. Ez megjeleníti a LUIS által a cél és a Kimondás entitásai számára küldött JSON-választ.

    [![Alapszintű bot-válasz az emulátorban](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>További lépések

További [mintákat](https://github.com/microsoft/botframework-solutions) tekinthet meg a társalgási robotokkal. 

> [!div class="nextstepaction"]
> [Language Understanding-alkalmazás létrehozása egyéni tárgyú tartománnyal](luis-quickstart-intents-only.md)
