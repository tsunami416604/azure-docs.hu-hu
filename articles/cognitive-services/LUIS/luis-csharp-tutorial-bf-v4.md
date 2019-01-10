---
title: A robot - C# -v4
titleSuffix: Language Understanding - Azure Cognitive Services
description: A C# használatával hozzon létre egy csevegőrobotot integrált nyelvfelismeréssel (LUIS). Ez csevegőrobot a Human Resources app használatával rövid idő alatt megvalósít egy robotmegoldást. A robot összeállításához a Bot Framework 4-es verzióját és az Azure webalkalmazás-robotot használja.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: d233f1df40d9580edfaaeb6b819c014952ad3b0c
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189274"
---
# <a name="tutorial-luis-bot-in-c-with-the-bot-framework-4x-and-the-azure-web-app-bot"></a>Oktatóanyag: A LUIS-robot C# a Bot keretrendszer 4.x-es és az Azure Web app bot
A C# használatával létrehozhat egy integrált nyelvfelismerési (LUIS) képességekkel rendelkező csevegőrobotot. Ez a robot a HomeAutomation app használatával valósítja meg a robotmegoldást. A robot összeállításához a [Bot Framework 4-es verzióját](https://github.com/Microsoft/botbuilder-js) és az Azure [webalkalmazás-robotot](https://docs.microsoft.com/azure/bot-service/) használja.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Webalkalmazás-robot létrehozása. Ez a folyamat egy új LUIS-appot hoz létre az Ön számára.
> * Előre összeállított tartomány hozzáadása az új LUIS-modellhez
> * A Web Bot Service által létrehozott projekt letöltése
> * A robot és az emulátor elindítása a helyi számítógépen
> * A robot kódjának módosítása az új LUIS-szándékokhoz
> * Kimondottszöveg-eredmények megtekintése a robotban

## <a name="prerequisites"></a>Előfeltételek

* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-web-app-bot"></a>Webalkalmazás-robot létrehozása

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Új erőforrás létrehozása** lehetőséget.

2. A keresőmezőben keresse meg, majd válassza ki a **Webalkalmazás-robot** elemet. Kattintson a **Létrehozás** gombra.

3. A **Bot Service** szolgáltatásban adja meg a szükséges adatokat:

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

4. A **Bot template settings** (Robotsablon beállításai) menüpontban válassza az alábbi lehetőségeket, majd kattintson a beállítások alatt található **Select** (Kiválasztás) gombra:

    |Beállítás|Cél|Kiválasztás|
    |--|--|--|
    |SDK verziója|Bot Framework verziója|**SDK v4**|
    |SDK nyelve|Robot programozási nyelve|**C#**|
    |Echo/Alapszintű robot|Robot típusa|**Alapszintű robot**|
    
5. Kattintson a **Létrehozás** gombra. Ezzel létrehozza a robotszolgáltatást, és üzembe helyezi azt az Azure-ban. A folyamat egyik része egy `luis-csharp-bot-XXXX` nevű LUIS-appot hoz létre. Ez a név a robot és az app előző szakaszban szereplő nevén alapul.

    [ ![Webalkalmazás-robot létrehozása](./media/bfv4-csharp/create-web-app-service.png) ](./media/bfv4-csharp/create-web-app-service.png#lightbox)

6. Hagyja megnyitva ezt a böngészőlapot. A LUIS portálon elvégzendő lépésekhez nyisson új böngészőlapot. Folytassa a következő szakasszal, amely az új robotszolgáltatás üzembe helyezését mutatja be.

## <a name="add-prebuilt-domain-to-model"></a>Előre összeállított tartomány hozzáadása a modellhez
A robotszolgáltatás üzembehelyezési folyamatának egyik része egy szándékokat és példaszövegeket tartalmazó új LUIS-appot hoz létre. A robot szándékleképezéseket biztosít az új LUIS-apphoz a következő szándékok esetén: 

|Alapszintű robot LUIS-szándékai|példa kimondott szöveg|
|--|--|
|Mégse|`stop`|
|Üdvözlés|`hello`|
|Súgó|`help`|
|None|Az app tartományán kívül bármi.|

Adja hozzá az előre összeállított HomeAutomation alkalmazást a modellhez, a következőhöz hasonló kimondott szövegek kezelése érdekében: `Turn off the living room lights`

1. Nyissa meg a [LUIS portált](https://www.luis.ai), és jelentkezzen be.
2. A **My Apps** (Saját alkalmazások) lapon kattintson a **Created date** (Létrehozás dátuma) oszlopra, így az app létrehozásának dátuma szerint rendezheti azt. Az Azure Bot Service egy új appot hozott létre az előző szakaszban. A neve `luis-csharp-bot-` + `<your-name>` + 4 véletlenszerű karakter.
3. Nyissa meg az appot, és a felső navigációs ablakban lépjen a **Build** (Létrehozás) szakaszhoz.
4. Válassza a **Prebuilt Domains** (Előre összeállított tartományok) elemet a bal oldali menüben.
5. Válassza ki a **HomeAutomation** tartományt a kártyán szereplő **Add domain** (Tartomány hozzáadása) lehetőségre kattintva.
6. A jobb felső menüben válassza a **Train** (Betanítás) elemet.
7. A jobb felső menüben válassza a **Publish** (Közzététel) lehetőséget. 

    Az Azure Bot Service által létrehozott app most már tartalmazza az új szándékokat:

    |Az alapszintű robot új szándékai|példa kimondott szöveg|
    |--|--|
    |HomeAutomation.TurnOn|`turn the fan to high`
    |HomeAutomation.TurnOff|`turn off ac please`|

## <a name="download-the-web-app-bot"></a>A webalkalmazás-robot letöltése 
A webalkalmazás-robot kódjának fejlesztéséhez töltse le a kódot a helyi számítógépre. 

1. Az Azure Portalon, a webalkalmazás-robot erőforrásán kattintson az **Alkalmazásbeállítások** menüpontra, és másolja a **botFilePath** és a **botFileSecret** értékeit. Ezeket a későbbiekben hozzá kell adnia egy környezeti fájlhoz. 

2. Az Azure Portalon a **Robot felügyelete** szakaszban kattintson a **Build** elemre. 

3. Válassza a **Robot forráskódjának letöltse** lehetőséget. 

    [ ![Webalkalmazás-robot forráskódjának letöltése alapszintű robothoz](../../../includes/media/cognitive-services-luis/bfv4/download-code.png) ](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

4. Tömörített forráskód esetén az üzenet tartalmazza a kód letöltéséhez szükséges hivatkozást. Kattintson a hivatkozásra. 

5. Mentse a .zip-fájlt a helyi számítógépére, és bontsa ki a fájlokat. Nyissa meg a projektet. 

6. Nyissa meg a bot.cs fájlt, és keresse meg a `_services.LuisServices` kifejezést. A rendszer ezen a ponton küldi a robotban megadott felhasználói kimondott szöveget a LUIS-hoz.

    ```csharp
    /// <summary>
    /// Run every turn of the conversation. Handles orchestration of messages.
    /// </summary>
    /// <param name="turnContext">Bot Turn Context.</param>
    /// <param name="cancellationToken">Task CancellationToken.</param>
    /// <returns>A <see cref="Task"/> representing the asynchronous operation.</returns>
    public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    {
        var activity = turnContext.Activity;

        if (activity.Type == ActivityTypes.Message)
        {
            // Perform a call to LUIS to retrieve results for the current activity message.
            var luisResults = await _services.LuisServices[LuisConfiguration].RecognizeAsync(turnContext, cancellationToken).ConfigureAwait(false);

            // If any entities were updated, treat as interruption.
            // For example, "no my name is tony" will manifest as an update of the name to be "tony".
            var topScoringIntent = luisResults?.GetTopScoringIntent();

            var topIntent = topScoringIntent.Value.intent;
            switch (topIntent)
            {
                case GreetingIntent:
                    await turnContext.SendActivityAsync("Hello.");
                    break;
                case HelpIntent:
                    await turnContext.SendActivityAsync("Let me try to provide some help.");
                    await turnContext.SendActivityAsync("I understand greetings, being asked for help, or being asked to cancel what I am doing.");
                    break;
                case CancelIntent:
                    await turnContext.SendActivityAsync("I have nothing to cancel.");
                    break;
                case NoneIntent:
                default:
                    // Help or no intent identified, either way, let's provide some help.
                    // to the user
                    await turnContext.SendActivityAsync("I didn't understand what you just said to me.");
                    break;
            }
        }
        else if (activity.Type == ActivityTypes.ConversationUpdate)
        {
            if (activity.MembersAdded.Any())
            {
                // Iterate over all new members added to the conversation.
                foreach (var member in activity.MembersAdded)
                {
                    // Greet anyone that was not the target (recipient) of this message.
                    // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
                    if (member.Id != activity.Recipient.Id)
                    {
                        var welcomeCard = CreateAdaptiveCardAttachment();
                        var response = CreateResponse(activity, welcomeCard);
                        await turnContext.SendActivityAsync(response).ConfigureAwait(false);
                    }
                }
            }
        }

    }
    ```

    A robot elküldi a felhasználói kimondott szöveget a LUIS-hoz, és lekéri az eredményeket. A felső szándék határozza meg a beszélgetés folyamát. 


## <a name="start-the-bot"></a>A robot indítása
A kód vagy a beállítások módosítása előtt ellenőrizze a robot működését. 

1. Nyissa meg a megoldásfájlt a Visual Studióban. 

2. Hozzon létre egy `appsettings.json` fájlt, amely a robot kódja által keresett robotváltozókat tartalmazza:

    ```JSON
    {
    "botFileSecret": "",
    "botFilePath": ""

    }
    ```

    Állítsa be a változók értékét a **[Webalkalmazás-robot letöltése](#download-the-web-app-bot)** című szakasz 1. lépésében az Azure Bot Service alkalmazásbeállításaiból kimásolt értékekre.

3. Indítsa el a robotot a Visual Studióban. Ekkor megnyílik egy böngészőablak a webalkalmazás-robot webhelyével a `http://localhost:3978/` címen.

## <a name="start-the-emulator"></a>Az emulátor indítása

1. Indítsa el a Bot Emulatort.

2. A Bot Emulatorban válassza ki a projekt gyökérmappájában található *.bot fájlt. Ez a `.bot` fájl tartalmazza a robot üzenetekhez tartozó URL-végpontját:

    [ ![Bot Emulator v4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png) ](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. Adja meg a robot titkos kódját, amelyet a **[Webalkalmazás-robot letöltése](#download-the-web-app-bot)** című szakasz 1. lépésében az Azure Bot Service alkalmazásbeállításaiból másolt ki. Ez lehetővé teszi, hogy az emulátor hozzáférjen a `.bot` fájl titkosított mezőihez.

    ![A Bot Emulator v4 titkos kódja](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)

4. A Bot Emulatorban írja be a `Hello` kifejezést, és kérje le az alapszintű robothoz tartozó megfelelő választ.

    [ ![Alapszintű robot válasza az emulátorban](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png) ](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>A robot kódjának módosítása 

A `BasicBot.cs` fájlban adja hozzá az új szándékok kezeléséhez szükséges kódot. 

1. A fájl tetején keresse meg a **Támogatott LUIS-szándékok** szakaszt, és adja hozzá a HomeAutomation-szándékokhoz tartozó állandókat:

    ```csharp
    // Supported LUIS Intents
    public const string GreetingIntent = "Greeting";
    public const string CancelIntent = "Cancel";
    public const string HelpIntent = "Help";
    public const string NoneIntent = "None";
    public const string TurnOnIntent = "HomeAutomation_TurnOn"; // new intent
    public const string TurnOffIntent = "HomeAutomation_TurnOff"; // new intent
    ```

    Figyelje meg, hogy a tartomány és a LUIS portálról származó app szándéka között elhelyezkedő pontot (`.`) a rendszer aláhúzásjelre (`_`) cserélte. 

2. Keresse meg az **OnTurnAsync** metódust, amely a kimondott szöveg LUIS-előrejelzését fogadja. Adja hozzá a kódot a switch utasításhoz, hogy a rendszer visszaadja a két HomeAutomation-szándékra adott választ. 

    ```csharp
    case TurnOnIntent:
        await turnContext.SendActivityAsync("TurnOn intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    case TurnOffIntent:
        await turnContext.SendActivityAsync("TurnOff intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    ```

    A robot nem pontosan ugyanazt a választ adja, mint a LUIS REST API-kérés, ezért fontos, hogy a válaszként kapott JSON-ban ellenőrizze a különbségeket. A szöveg és a szándék tulajdonságai azonosak, de az entitások tulajdonságértékei módosultak. 

    ```JSON
    {
        "$instance": {
            "HomeAutomation_Device": [
                {
                    "startIndex": 23,
                    "endIndex": 29,
                    "score": 0.9776345,
                    "text": "lights",
                    "type": "HomeAutomation.Device"
                }
            ],
            "HomeAutomation_Room": [
                {
                    "startIndex": 12,
                    "endIndex": 22,
                    "score": 0.9079433,
                    "text": "livingroom",
                    "type": "HomeAutomation.Room"
                }
            ]
        },
        "HomeAutomation_Device": [
            "lights"
        ],
        "HomeAutomation_Room": [
            "livingroom"
        ]
    }
    ```



## <a name="view-results-in-bot"></a>Eredmények megtekintése a robotban

1. A Bot Emulatorban írja be a következő szöveget: `Turn on the livingroom lights to 50%`

2. A robot a következővel válaszol:

    ```JSON
    TurnOn intent found, JSON response: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```    

## <a name="learn-more-about-bot-framework"></a>További tudnivalók a Bot Frameworkről
Az Azure Bot Service a Bot Framework SDK-t használja. További információk az SDK-ról és a Bot Frameworkről:

* Az [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4 dokumentációja
* [Bot Builder minták](https://github.com/Microsoft/botbuilder-samples)
* [Bot Builder SDK](https://docs.microsoft.com/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Bot Builder eszközök](https://github.com/Microsoft/botbuilder-tools):

## <a name="next-steps"></a>További lépések

Létrehozott egy Azure Bot Service-szolgáltatást, kimásolta a robot titkos kódját, valamint a `.bot`-fájl elérési útját, és letöltötte a kódot tartalmazó zip-fájlt. Hozzáadta az előre összeállított HomeAutomation-tartományt az új Azure Bot Service részeként létrehozott LUIS-apphoz, majd ismét betanította és közzétette az alkalmazást. Kibontotta a kódprojektet, létrehozott egy környezeti fájlt (`.env`), és beállította a robot titkos kódját, valamint a `.bot`-fájl elérési útját. A bot.js fájlban hozzáadta a két új szándék kezeléséhez szükséges kódot. Majd letesztelte a robotot a Bot Emulatorral, hogy láthassa az új szándékok egyikéhez tartozó kimondott szövegre adott LUIS-választ. 

Még több [minták](https://github.com/Microsoft/AI) a természetes nyelvi robotokat. 

> [!div class="nextstepaction"]
> [Egyéni tartomány létrehozása a LUIS-ban](luis-quickstart-intents-only.md)
