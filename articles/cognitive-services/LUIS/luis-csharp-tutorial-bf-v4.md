---
title: 'Oktatóanyag: Language Understanding robot C# v4'
titleSuffix: Azure Cognitive Services
description: A C# használatával hozzon létre egy csevegőrobotot integrált nyelvfelismeréssel (LUIS). A robot a robot Framework 4-es verziójával és az Azure Web App bot Service-szel készült.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 213449a78baf8fc2b7cb6c74709efeaf2a50d5b2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495469"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Oktatóanyag: Language Understanding használatával engedélyezett webalkalmazás-robot használataC#

A C# használatával a Language Understanding (Luis) segítségével integrált csevegési robotot hozhat létre. A robot az Azure [Web App bot](https://docs.microsoft.com/azure/bot-service/) Resource és a bot Framework v4-es [verziójával](https://github.com/Microsoft/botbuilder-dotnet) készült.

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]


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
    |Földrajzi egység|Azure-régió – Nem kell a LUIS létrehozási vagy közzétételi régiójával azonosnak lennie.|`westus`|
    |Tarifacsomag|Szolgáltatáskérési korlátokhoz és számlázáshoz használható.|Az `F0` az ingyenes szint.
    |App neve|A név lesz az altartomány a robot felhőbeli üzembe helyezésekor (például humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, például: `luis-csharp-bot-johnsmith`|
    |Robotsablon|A Bot Framework beállításai – lásd a következő táblázatot|
    |A LUIS-app helye|Egyeznie kell a LUIS-erőforrás régiójával|`westus`|
    |App Service-csomag/hely|Ne módosítsa a megadott alapértelmezett értéket.|
    |Application Insights|Ne módosítsa a megadott alapértelmezett értéket.|
    |Microsoft-alkalmazás azonosítója és jelszava|Ne módosítsa a megadott alapértelmezett értéket.|

1. A **bot-sablonban**válassza ki a következőt, majd a beállítások területen válassza a **kiválasztás** gombot:

    |Beállítás|Cél|Kiválasztás|
    |--|--|--|
    |SDK verziója|Bot Framework verziója|**SDK v4**|
    |SDK nyelve|Robot programozási nyelve|**C#**|
    |Bot|Robot típusa|**Alapszintű robot**|
    
1. Kattintson a **Létrehozás** gombra. Ezzel létrehozza a robotszolgáltatást, és üzembe helyezi azt az Azure-ban. A folyamat egyik része egy `luis-csharp-bot-XXXX` nevű LUIS-appot hoz létre. Ez a név a/Azure bot Service-alkalmazás nevén alapul.

    [webalkalmazás-robot ![létrehozása](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    A folytatás előtt várjon, amíg létrejött a robot szolgáltatás.

## <a name="the-bot-has-a-language-understanding-model"></a>A robot Language Understanding modellt tartalmaz

A bot Service létrehozási folyamata egy új LUIS-alkalmazást is létrehoz a szándékokkal és példa hosszúságú kimondott szöveg. A robot szándékleképezéseket biztosít az új LUIS-apphoz a következő szándékok esetén: 

|Alapszintű robot LUIS-szándékai|példa kimondott szöveg|
|--|--|
|Repülőjegy|`Travel to Paris`|
|Mégse|`bye`|
|GetWeather|`what's the weather like?`|
|None|Az app tartományán kívül bármi.|

## <a name="test-the-bot-in-web-chat"></a>A robot tesztelése a webes csevegésben

1. Miközben az új robot Azure Portal továbbra is elérhető, válassza a **tesztelés webes csevegésben**lehetőséget. 
1. Az **üzenet típusa** szövegmezőbe írja be a `Book a flight from Seattle to Berlin tomorrow`szöveget. A robot ellenőrzi, hogy szeretne-e repülőjáratot foglalni. 

    ![A Azure Portal képernyőképe a "Hello" szöveg beírásával.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    A teszt funkció használatával gyorsan tesztelheti a robotot. A teljes teszteléshez, beleértve a hibakeresést, töltse le a robot kódját, és használja a Visual studiót. 

## <a name="download-the-web-app-bot-source-code"></a>A webalkalmazás robot-forráskódjának letöltése
A webalkalmazás-robot kódjának fejlesztéséhez töltse le a kódot a helyi számítógépre. 

1. Az Azure Portalon a **Robot felügyelete** szakaszban kattintson a **Build** elemre. 

1. Válassza a **Robot forráskódjának letöltse** lehetőséget. 

    [![webalkalmazás-robot forráskódjának letöltése az alapszintű robothoz](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Ha az előugró ablak **a letöltött zip-fájlban is**megkéri az Alkalmazásbeállítások megadását, válassza az **Igen**lehetőséget.

1. Tömörített forráskód esetén az üzenet tartalmazza a kód letöltéséhez szükséges hivatkozást. Kattintson a hivatkozásra. 

1. Mentse a .zip-fájlt a helyi számítógépére, és bontsa ki a fájlokat. Nyissa meg a projektet a Visual Studióval. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Tekintse át a kódot, hogy kinyerje a LUIS-t és a választ

1. A felhasználónak a LUIS előrejelzési végpontra történő elküldéséhez nyissa meg a **FlightBookingRecognizer.cs** fájlt. A rendszer ezen a ponton küldi a robotban megadott felhasználói kimondott szöveget a LUIS-hoz. A LUIS válaszát a **RecognizeAsync** metódus adja vissza.  

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    
    namespace Microsoft.BotBuilderSamples
    {
        public class FlightBookingRecognizer : IRecognizer
        {
            private readonly LuisRecognizer _recognizer;
    
            public FlightBookingRecognizer(IConfiguration configuration)
            {
                var luisIsConfigured = !string.IsNullOrEmpty(configuration["LuisAppId"]) && !string.IsNullOrEmpty(configuration["LuisAPIKey"]) && !string.IsNullOrEmpty(configuration["LuisAPIHostName"]);
                if (luisIsConfigured)
                {
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]);
    
                    _recognizer = new LuisRecognizer(luisApplication);
                }
            }
    
            // Returns true if luis is configured in the appsettings.json and initialized.
            public virtual bool IsConfigured => _recognizer != null;
    
            public virtual async Task<RecognizerResult> RecognizeAsync(ITurnContext turnContext, CancellationToken cancellationToken)
                => await _recognizer.RecognizeAsync(turnContext, cancellationToken);
    
            public virtual async Task<T> RecognizeAsync<T>(ITurnContext turnContext, CancellationToken cancellationToken)
                where T : IRecognizerConvert, new()
                => await _recognizer.RecognizeAsync<T>(turnContext, cancellationToken);
        }
    }
    ````

1. **Párbeszédpanelek megnyitása – > MainDialog.cs** rögzíti a megadást, és elküldi a ActStep metódus executeLuisQuery. 

    ```csharp
    public class MainDialog : ComponentDialog
    {
        private readonly FlightBookingRecognizer _luisRecognizer;

        ...

        public MainDialog(FlightBookingRecognizer luisRecognizer, BookingDialog bookingDialog, ILogger<MainDialog> logger)
                    : base(nameof(MainDialog))
        {
            _luisRecognizer = luisRecognizer;
            ...
        }

        private async Task<DialogTurnResult> ActStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
        {
            if (!_luisRecognizer.IsConfigured)
            {
                // LUIS is not configured, we just run the BookingDialog path with an empty BookingDetailsInstance.
                return await stepContext.BeginDialogAsync(nameof(BookingDialog), new BookingDetails(), cancellationToken);
            }

            // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt.)
            var luisResult = await _luisRecognizer.RecognizeAsync<FlightBooking>(stepContext.Context, cancellationToken);
            switch (luisResult.TopIntent().intent)
            {
                case FlightBooking.Intent.BookFlight:
                    await ShowWarningForUnsupportedCities(stepContext.Context, luisResult, cancellationToken);

                    // Initialize BookingDetails with any entities we may have found in the response.
                    var bookingDetails = new BookingDetails()
                    {
                        // Get destination and origin from the composite entities arrays.
                        Destination = luisResult.ToEntities.Airport,
                        Origin = luisResult.FromEntities.Airport,
                        TravelDate = luisResult.TravelDate,
                    };

                    // Run the BookingDialog giving it whatever details we have from the LUIS call, it will fill out the remainder.
                    return await stepContext.BeginDialogAsync(nameof(BookingDialog), bookingDetails, cancellationToken);

                case FlightBooking.Intent.GetWeather:
                    // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                    var getWeatherMessageText = "TODO: get weather flow here";
                    var getWeatherMessage = MessageFactory.Text(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(getWeatherMessage, cancellationToken);
                    break;

                default:
                    // Catch all for unhandled intents
                    var didntUnderstandMessageText = $"Sorry, I didn't get that. Please try asking in a different way (intent was {luisResult.TopIntent().intent})";
                    var didntUnderstandMessage = MessageFactory.Text(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(didntUnderstandMessage, cancellationToken);
                    break;
            }

            return await stepContext.NextAsync(null, cancellationToken);
        }
        
        ...

    }
    ```

## <a name="start-the-bot-code-in-visual-studio"></a>A robot kódjának elindítása a Visual Studióban

A Visual Studio 2019-ben indítsa el a robotot. Ekkor megnyílik egy böngészőablak a webalkalmazás-robot webhelyével a `http://localhost:3978/` címen. A kezdőlapon megjelennek a robottal kapcsolatos információk.

![A kezdőlapon megjelennek a robottal kapcsolatos információk.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>A bot-emulátor használata a robot teszteléséhez

1. Indítsa el a robot-emulátort, és válassza a **robot megnyitása**lehetőséget.
1. A **bot megnyitása** előugró ablakban adja meg a robot URL-címét, például `http://localhost:3978/api/messages`. A `/api/messages` útvonal a robot webes címe.
1. Adja meg a **Microsoft-alkalmazás azonosítóját** és a **Microsoft-alkalmazás jelszavát**, amely a letöltött robot-kód gyökerében található **appSettings. JSON** fájlban található.


1. A robot-emulátorban adja meg a `Book a flight from Seattle to Berlin tomorrow`t, és ugyanazt a választ kapja meg az alapszintű robot esetében, mint amit a **webes csevegésben kapott a tesztben**.

    [alapszintű bot-válasz ![az emulátorban](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Válassza az **Igen** lehetőséget. A robot a műveleteinek összegzésével válaszol. 
1. A bot Emulator naplójából válassza ki a `Luis Trace`t tartalmazó sort. Ez megjeleníti a LUIS által a cél és a Kimondás entitásai számára küldött JSON-választ.

    [alapszintű bot-válasz ![az emulátorban](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]


## <a name="next-steps"></a>További lépések

További [mintákat](https://github.com/microsoft/botframework-solutions) tekinthet meg a társalgási robotokkal. 

> [!div class="nextstepaction"]
> [Language Understanding-alkalmazás létrehozása egyéni tárgyú tartománnyal](luis-quickstart-intents-only.md)
