---
title: A robot – node.js-szel – v3
titleSuffix: Azure Cognitive Services
description: A robot a LUIS-alkalmazás a Bot keretrendszer használatával integrálva hozhat létre.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: e73f22a2da9e8b8b3bd70f4a0231ea7932fc7a86
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436942"
---
# <a name="luis-bot-in-nodejs-with-the-bot-framework-3x-and-the-azure-web-app-bot"></a>A Bot keretrendszer Node.js-ben a LUIS-robot 3.x és az Azure Web app bot

A Node.js használatával hozzon létre egy csevegőrobotot integrált nyelvfelismeréssel (LUIS). Ez csevegőrobot az előre összeállított HomeAutomation tartomány segítségével rövid idő alatt kiépíthet egy robot megoldás. A robot épül fel a Bot keretrendszer 3.x és az Azure Web app bot.

## <a name="prerequisite"></a>Előfeltétel

Mielőtt létrehozna a robot, kövesse a [hozzon létre egy alkalmazást](./luis-get-started-create-app.md) hozhat létre a LUIS-alkalmazást, amelyet használ.

A robot a HomeAutomation tartományból, amelyek a LUIS alkalmazás válaszol szándék fog vonatkozni. Minden egyes ezeket a leképezéseket a LUIS-alkalmazás biztosítja a megjelölésű, amely hozzá van leképezve. A robot biztosít egy párbeszédpanel, amely kezeli a szándéka, hogy a LUIS észleli.

| Szándék | Példa kimondott szöveg | A robot funkció |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Kapcsolja be a lámpák. | A robot meghívja a `TurnOnDialog` amikor a `HomeAutomation.TurnOn` észlel. Ezen a párbeszédpanelen, ahol szeretné hívhat meg egy IoT-szolgáltatás, kapcsolja be az eszközön, és ossza meg a felhasználót, hogy az eszköz be van kapcsolva. |
| HomeAutomation.TurnOff | Kapcsolja ki a szobája lámpa. | A robot meghívja a `TurnOffDialog` amikor a `HomeAutomation.TurnOff` észlel. A párbeszédpanel, ahol szeretné meghívni egy IoT-szolgáltatás, kapcsolja ki az eszközt, és ossza meg a felhasználót, hogy az eszköz ki lett kapcsolva a. |


## <a name="create-a-language-understanding-bot-with-bot-service"></a>A Language Understanding bot létrehozni a Bot Service-szel

1. Az a [az Azure portal](https://portal.azure.com), jelölje be **hozzon létre új erőforrást** a menü panelre, és válassza ki a **összes**.

    ![Új erőforrás létrehozása](./media/luis-tutorial-node-bot/bot-service-creation.png)

2. A keresőmezőbe keresése **Web App Bot**. 

    ![Új erőforrás létrehozása](./media/luis-tutorial-node-bot/bot-service-selection.png)

3. Az a **Bot Service** panelen adja meg a szükséges információkat, és válassza ki **létrehozás**. Ez létrehozza és telepíti a bot service és a LUIS-alkalmazás az Azure-bA. Ha a használni kívánt [speech betanítási művelet](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), tekintse át [régió követelmények](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming) előtt a robot létrehozásának. 
    * Állítsa be **alkalmazásnév** a robot neve. A robot a felhőben (például mynotesbot.azurewebsites.net) történő telepítése során az altartomány neve lesz. <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * Válassza ki az előfizetést [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), App service-csomagot, és [hely](https://azure.microsoft.com/regions/).
    * A **Bot sablon**, jelölje be:
        * **SDK-t v3**
        * **Node.js**
        * **Hangfelismerés**
    * Válassza ki a **LUIS alkalmazás hely**. Ez az a szerzői műveletekhez részben [régió] [ LUIS] jön létre az alkalmazást.
    * Jelölje be a megerősítő a jogi nyilatkozat. A jogi nyilatkozat feltételeit az alábbiakban olvashatják be a jelölőnégyzetet.

    ![Bot Service panel](./media/luis-tutorial-node-bot/bot-service-setting-callout-template.png)


4. Győződjön meg arról, hogy a bot service van telepítve.
    * Válassza ki az értesítéseket (Harang ikon, amely mentén felső szegélyéhez, hogy az Azure Portalon található). Az értesítés módosul **központi telepítés elindítva** való **üzembe helyezés sikeres**.
    * Miután az értesítés megváltozik, és **üzembe helyezés sikeres**, jelölje be **erőforrás megnyitása** adott értesítésre.

## <a name="try-the-default-bot"></a>Próbálja ki az alapértelmezett robotot

Győződjön meg arról, hogy a robot ellenőrzésével lett-e telepítve. a **értesítések**. Az értesítés módosul **üzembe helyezés folyamatban...**  való **üzembe helyezés sikeres**. Válassza ki **erőforrás megnyitása** gombra kattintva nyissa meg a robot erőforrások panelen.

<!-- this step isn't supposed to be necessary -->
## <a name="install-npm-resources"></a>Telepítse az NPM-erőforrások
Az NPM-csomagok telepítéséhez az alábbi lépéseket követve:

1. Válassza ki **hozhat létre** származó a **Bot felügyeleti** a Web App Bot szakaszában. 

2. Egy új, a második böngésző ablak megnyílik. Válassza ki **online kódszerkesztő megnyitása**.

3. A felső navigációs sávban válassza a web app bot neve `homeautomationluisbot`. 

4. Válassza ki a legördülő listából **Kudu konzol megnyitása**.

5. Egy új böngészőablakban nyílik meg. A konzolon adja meg a következő parancsot:

    ```console
    cd site\wwwroot && npm install
    ```

    Várjon, amíg a telepítési folyamat befejezéséhez. Térjen vissza az első böngészőablakban. 

## <a name="test-in-web-chat"></a>A webes csevegési tesztelése
A robot regisztrálása után válassza ki a **vizsgálat a webes csevegési** a webes csevegési ablak megnyitásához. Írja be a "hello", a webes csevegési.

  ![A robot webes csevegési tesztelése](./media/luis-tutorial-node-bot/bot-service-web-chat.png)

A robot válaszol kimondásával "elérte üdvözlés. Azt mondta: hello ". Ez megerősíti, hogy a robot a üzenetet kapott, és alapértelmezés szerint a LUIS-alkalmazást, amely a létrehozást, az átadott. Ez az alapértelmezés a LUIS-alkalmazás egy üdvözlés leképezés észlelhető. A következő lépésben a robot a LUIS alkalmazás helyett a LUIS-alkalmazás alapértelmezett korábban létrehozott fogja kötni.

## <a name="connect-your-luis-app-to-the-bot"></a>A robot a LUIS-alkalmazás csatlakoztatása

Nyissa meg **Alkalmazásbeállítások** az első böngészőablakot és szerkesztése a **LuisAppId** mező tartalmazza a LUIS-alkalmazás Alkalmazásazonosítója.

  ![Frissítés az Azure-ban a LUIS alkalmazás azonosítója](./media/luis-tutorial-node-bot/bot-service-app-id.png)

Ha nem rendelkezik a LUIS-alkalmazás azonosítója, jelentkezzen be a [LUIS](luis-reference-regions.md) webhelyen, amellyel belép Azure-bA ugyanazzal a fiókkal. Válassza ki a **saját alkalmazások**. 

1. Keresse meg a korábban létrehozott, LUIS alkalmazást, amely tartalmazza a szándékok és entitások HomeAutomation tartományból.

2. Az a **beállítások** a LUIS alkalmazás lapon keresse meg és másolja az alkalmazás azonosítóját.

3. Ha az alkalmazás még nem betanított, válassza ki a **betanításához** gombra a jobb felső sarokban az alkalmazás betanításához.

4. Ha még nem tette közzé az alkalmazást, válassza ki a **közzététel** megnyitásához a felső navigációs sávban a **közzététel** lapot. Válasza a Production (Termelés) helyet, és kattintson a **Publish** (Közzététel) gombra.

## <a name="modify-the-bot-code"></a>A robot kód módosítása

Továbbra is nyissa meg vagy válassza ki az első böngészőablakban nyissa meg a második böngészőablakban **összeállítása** , majd **online kódszerkesztő megnyitása**.

   ![Online kódszerkesztő megnyitása](./media/luis-tutorial-node-bot/bot-service-build.png)

A kódszerkesztőben nyissa meg a `app.js`. A következő kódot tartalmazza:

```nodejs
/*-----------------------------------------------------------------------------
A simple Language Understanding (LUIS) bot for the Microsoft Bot Framework. 
-----------------------------------------------------------------------------*/

var restify = require('restify');
var builder = require('botbuilder');
var botbuilder_azure = require("botbuilder-azure");

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url); 
});
  
// Create chat connector for communicating with the Bot Framework Service
var connector = new builder.ChatConnector({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword,
    openIdMetadata: process.env.BotOpenIdMetadata 
});

// Listen for messages from users 
server.post('/api/messages', connector.listen());

/*----------------------------------------------------------------------------------------
* Bot Storage: This is a great spot to register the private state storage for your bot. 
* We provide adapters for Azure Table, CosmosDb, SQL Azure, or you can implement your own!
* For samples and documentation, see: https://github.com/Microsoft/BotBuilder-Azure
* ---------------------------------------------------------------------------------------- */

var tableName = 'botdata';
var azureTableClient = new botbuilder_azure.AzureTableClient(tableName, process.env['AzureWebJobsStorage']);
var tableStorage = new botbuilder_azure.AzureBotStorage({ gzipData: false }, azureTableClient);

// Create your bot with a function to receive messages from the user
// This default message handler is invoked if the user's utterance doesn't
// match any intents handled by other dialogs.
var bot = new builder.UniversalBot(connector, function (session, args) {
    session.send('You reached the default message handler. You said \'%s\'.', session.message.text);
});

bot.set('storage', tableStorage);

// Make sure you add code to validate these fields
var luisAppId = process.env.LuisAppId;
var luisAPIKey = process.env.LuisAPIKey;
var luisAPIHostName = process.env.LuisAPIHostName || 'westus.api.cognitive.microsoft.com';

const LuisModelUrl = 'https://' + luisAPIHostName + '/luis/v2.0/apps/' + luisAppId + '?subscription-key=' + luisAPIKey;

// Create a recognizer that gets intents from LUIS, and add it to the bot
var recognizer = new builder.LuisRecognizer(LuisModelUrl);
bot.recognizer(recognizer);

// Add a dialog for each intent that the LUIS app recognizes.
// See https://docs.microsoft.com/bot-framework/nodejs/bot-builder-nodejs-recognize-intent-luis 
bot.dialog('GreetingDialog',
    (session) => {
        session.send('You reached the Greeting intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Greeting'
})

bot.dialog('HelpDialog',
    (session) => {
        session.send('You reached the Help intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Help'
})

bot.dialog('CancelDialog',
    (session) => {
        session.send('You reached the Cancel intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Cancel'
})
```

A meglévő leképezéseket az App.js fájl a rendszer figyelmen kívül hagyja. Hagyhatja őket. 

## <a name="add-a-dialog-that-matches-homeautomationturnon"></a>Egy párbeszédpanel, amely megfelel a HomeAutomation.TurnOn hozzáadása

Másolja az alábbi kódot, majd adja hozzá a `app.js`.

```nodejs
bot.dialog('TurnOn',
    (session) => {
        session.send('You reached the TurnOn intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOn'
})
```

A [megegyezik] [ matches] beállítást a [triggerAction] [ triggerAction] csatlakozik a párbeszédpanelen adja meg a leképezés neve. A felismerő minden alkalommal, amikor a robot a felhasználó megkapja az utterance (kifejezés) futtatja. Ha a legmagasabb pontozási célt, amely észleli az megegyezik egy `triggerAction` kötve egy párbeszédpanel, a bot hív meg, hogy a párbeszédpanelen.

## <a name="add-a-dialog-that-matches-homeautomationturnoff"></a>Egy párbeszédpanel, amely megfelel a HomeAutomation.TurnOff hozzáadása

Másolja az alábbi kódot, majd adja hozzá a `app.js`.

```nodejs
bot.dialog('TurnOff',
    (session) => {
        session.send('You reached the TurnOff intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOff'
})
```
## <a name="test-the-bot"></a>A robot tesztelése

Az Azure Portalon válassza ki a **tesztelni, webes csevegési** a robot teszteléséhez. Próbálja meg típusú üzenetek like "kapcsolja fel a lámpákat" és "kapcsolja ki a saját melegítőkráter" meghívni a szándék fog vonatkozni, hogy a felvett.
   ![A webes csevegési HomeAutomation bot tesztelése](./media/luis-tutorial-node-bot/bot-service-chat-results.png)

> [!TIP]
> Ha azt tapasztalja, hogy a robot nem mindig ismeri fel a megfelelő leképezés vagy entitásokat, növelheti a teljesítményt a LUIS-alkalmazás adná azt betanításához további példa utterances. A LUIS-alkalmazás a robot kód módosítás nélkül ténytábláknál. Lásd: [példa beszédmódok hozzáadása](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) és [taníthat vagy tesztelhet a LUIS-alkalmazás](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test).

## <a name="learn-more-about-bot-framework"></a>További tudnivalók a Bot Frameworkről
Tudjon meg többet [Bot Framework](https://dev.botframework.com/) és a [3.x](https://github.com/Microsoft/BotBuilder) és [4.x](https://github.com/Microsoft/botbuilder-js) SDK-k.

## <a name="next-steps"></a>További lépések

<!-- From trying the bot, you can see that the recognizer can trigger interruption of the currently active dialog. Allowing and handling interruptions is a flexible design that accounts for what users really do. Learn more about the various actions you can associate with a recognized intent.--> Próbálkozzon más leképezések, Súgó, a Mégse gombra és üdvözlés, például a LUIS-alkalmazás hozzáadása. Ezután adja hozzá az új leképezések a párbeszédpanelek, és tesztelje a robot használatával. 

<!-- 
> [!NOTE] 
> The default LUIS app that the template created contains example utterances for Cancel, Greeting, and Help intents. In the list of apps, find the app that begins with the name specified in **App name** in the **Bot Service** blade when you created the Bot Service. -->

> [!div class="nextstepaction"]
> [Leképezések hozzáadása](./luis-how-to-add-intents.md)
> [Speech betanítási művelet](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


[intentDialog]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html

[intentDialog_matches]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html#matches 

[NotesSample]: https://github.com/Microsoft/BotFramework-Samples/tree/master/docs-samples/Node/basics-naturalLanguage

[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction

[confirmPrompt]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#confirmprompt

[waterfall]: bot-builder-nodejs-dialog-manage-conversation-flow.md#manage-conversation-flow-with-a-waterfall

[session_userData]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html#userdata

[EntityRecognizer_findEntity]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#findentity

[matches]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#matches

[LUISAzureDocs]: https://docs.microsoft.com/azure/cognitive-services/LUIS/Home

[Dialog]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html

[IntentRecognizerSetOptions]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.iintentrecognizersetoptions.html

[LuisRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.luisrecognizer

[LUISConcepts]: https://docs.botframework.com/node/builder/guides/understanding-natural-language/

[DisambiguationSample]: https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/feature-onDisambiguateRoute

[IDisambiguateRouteHandler]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.idisambiguateroutehandler.html

[RegExpRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.regexprecognizer.html

[AlarmBot]: https://github.com/Microsoft/BotBuilder/blob/master/Node/examples/basics-naturalLanguage/app.js

[LUISBotSample]: https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/intelligence-LUIS

[UniversalBot]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.universalbot.html


<!-- Old Links -->
[GitHub-BotFramework-Emulator-Download]: https://aka.ms/bot-framework-emulator
[GitHub-LUIS-Samples]: https://github.com/Microsoft/LUIS-Samples
[GitHub-LUIS-Samples-node-hotel-bot]: https://github.com/Microsoft/LUIS-Samples/tree/master/bot-integration-samples/hotel-finder/nodejs
[NodeJs]: https://nodejs.org/
[BFPortal]: https://dev.botframework.com/
[RegisterInstructions]: https://docs.microsoft.com/bot-framework/portal-register-bot
[BotFramework]: https://docs.microsoft.com/bot-framework/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions

