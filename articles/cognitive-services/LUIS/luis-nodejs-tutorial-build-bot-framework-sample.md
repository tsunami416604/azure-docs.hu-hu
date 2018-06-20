---
title: A Botot jelentéskészítő SDK for Node.js az Azure-ban használatával bot LUIS integrálása |} Microsoft Docs
description: A Botot keretrendszer LUIS alkalmazással integrált bot felépítéséhez.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/06/2018
ms.author: v-geberr
ms.openlocfilehash: 5d9b78977457f818b964adb16ebb5e9e5872aa2c
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264973"
---
# <a name="integrate-luis-with-a-bot-using-the-bot-builder-sdk-for-nodejs"></a>A Botot jelentéskészítő SDK for Node.js használatával bot LUIS integrálása

Ez az oktatóanyag végigvezeti a bot összeállítása a [Botot keretrendszer] [ BotFramework] , amely integrálva van egy LUIS alkalmazást.

## <a name="prerequisite"></a>Előfeltétel

Mielőtt létrehozná a botot, kövesse a [hozzon létre egy alkalmazást](./luis-get-started-create-app.md) az általa használt LUIS alkalmazás elkészítésére.

A botot válaszol-e a leképezések az HomeAutomation tartományból, amelyek az LUIS alkalmazásban. Az egyes ezeket a leképezéseket a LUIS app biztosít megjelölésű, amely azt. A botot biztosít egy párbeszédpanelt, amely kezeli a célt, amely LUIS észleli.

| Kísérlet történt | Példa utterance | Botot funkció |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Kapcsolja be a fény. | A botot meghívja a `TurnOnDialog` amikor a `HomeAutomation.TurnOn` észlel. Ezen a párbeszédpanelen, ahol szeretné meghívni egy IoT-szolgáltatás kapcsolja be az eszközön, és kérje meg a felhasználót, hogy az eszköz be van kapcsolva. |
| HomeAutomation.TurnOff | Kapcsolja ki a szobája fény. | A botot meghívja a `TurnOffDialog` amikor a `HomeAutomation.TurnOff` észlel. A párbeszédpanelt, ahol szeretné meghívni egy IoT-szolgáltatás kapcsolja ki egy eszközt, és kérje meg a felhasználót, hogy az eszköz ki lett kapcsolva. |


## <a name="create-a-language-understanding-bot-with-bot-service"></a>Hozzon létre egy nyelvi ismertetése botot Botot szolgáltatással

1. Az a [Azure-portálon](https://portal.azure.com), jelölje be **hozzon létre új erőforrást** a menü panelhez, és válassza a **láthatja az összes**.

    ![Új erőforrás létrehozása](./media/luis-tutorial-node-bot/bot-service-creation.png)

2. A keresőmezőbe, keresse meg a **Web App Botot**. 

    ![Új erőforrás létrehozása](./media/luis-tutorial-node-bot/bot-service-selection.png)

3. Az a **Botot szolgáltatás** panelen adja meg a szükséges információkat, és válassza ki **létrehozása**. Ez létrehozza, és a botot szolgáltatás és az alkalmazás LUIS telepíti az Azure-bA. Ha a használni kívánt [beszéd elsődleges](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), tekintse át [régió követelmények](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming) a botot létrehozása előtt. 
    * Állítsa be **alkalmazásnév** a botot névre. A név lesz a altartomány a botot a felhőbe (például mynotesbot.azurewebsites.net) telepítésekor. <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * Válassza ki az előfizetést, [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), App service-csomag, és [hely](https://azure.microsoft.com/regions/).
    * Válassza ki a **nyelvi ismertetése (Node.js)** sablon a **Botot sablon** mező.
    * Válassza ki a **LUIS App hely**. Ez az a szerzői műveletekhez [régió] [ LUIS] az alkalmazás létrehozása.
    * Jelölje be a megerősítő a jogi nyilatkozatot. A jogi nyilatkozat feltételeit. a rendszer alatt a jelölőnégyzet jelölését.

    ![Botot szolgáltatás panel](./media/luis-tutorial-node-bot/bot-service-setting-callout-template.png)


4. Győződjön meg arról, hogy a botot szolgáltatás már alkalmazva van.
    * Válassza ki az értesítéseket (a harang ikonra, amely mentén a felső szegélyéhez, hogy az Azure portálon található). Az értesítés állapotúról **telepítése megkezdődött** való **KözpontiTelepítés sikerült**.
    * Után vált, az értesítés **KözpontiTelepítés sikerült**, jelölje be **forrást** adott értesítésre kattinthat.

## <a name="try-the-default-bot"></a>Az alapértelmezett botot próbálja

Győződjön meg arról, hogy a botot ellenőrzésével lett-e telepítve. a **értesítések**. Az értesítések állapotúról **telepítés folyamatban...**  való **KözpontiTelepítés sikerült**. Válassza ki **forrást** gombra kattintva nyissa meg a botot erőforrások panelt.

<!-- this step isn't supposed to be necessary -->
## <a name="install-npm-resources"></a>NPM erőforrások telepítése
NPM csomagok telepítése a következő lépéseket:

1. Válassza ki **Build** a a **Botot felügyeleti** a webes alkalmazás Botot szakasza. 

2. A böngésző ablak megnyitása új, második. Válassza ki **nyitott online kód szerkesztése**.

3. A felső navigációs sáv, válassza ki a webalkalmazásnév botot `homeautomationluisbot`. 

4. A legördülő listában **nyissa meg a Kudu konzolt**.

5. Egy új böngészőablakban nyílik meg. A konzolon adja meg a következő parancsot:

    ```
    cd site\wwwroot && npm install
    ```

    Várjon, amíg a telepítési folyamat befejezéséhez. Térjen vissza az első böngészőablakot. 

## <a name="test-in-web-chat"></a>A webes Csevegés tesztelése
A botot regisztrálása után válassza a **vizsgálati a webes Csevegés** a webes Csevegés ablaktábla megnyitása. Írja be a "Hello" szövegrészt webes Csevegés.

  ![A botot webes Csevegés tesztelése](./media/luis-tutorial-node-bot/bot-service-web-chat.png)

A botot válaszol kimondásával "üdvözlés elérése. Ön említett: hello ". Ez megerősíti, hogy a botot a üzenetet kapott, és egy alapértelmezett LUIS-alkalmazást, amely az jön létre, az átadott. Ez az alapértelmezett LUIS app egy üdvözlés leképezés észlelhető. A következő lépésben a botot fog csatlakozni a korábban létrehozott helyett az alapértelmezett LUIS app LUIS alkalmazást.

## <a name="connect-your-luis-app-to-the-bot"></a>Az LUIS alkalmazás csatlakoztatása a botot

Nyissa meg **Alkalmazásbeállítások** az első böngészőablakban és szerkesztése a **LuisAppId** magában foglalja az LUIS alkalmazás Alkalmazásazonosító mezőben.

  ![Frissítse a LUIS Alkalmazásazonosító az Azure-ban](./media/luis-tutorial-node-bot/bot-service-app-id.png)

Ha nem rendelkezik a LUIS Alkalmazásazonosító, jelentkezzen be a [LUIS](luis-reference-regions.md) webhely ugyanazzal a fiókkal jelentkezzen be az Azure használatával. Válassza ki a **alkalmazásaimat**. 

1. A korábban létrehozott LUIS alkalmazás, amely tartalmazza a leképezések és entitásokat a HomeAutomation tartományból megkereséséhez.

2. Az a **beállítások** LUIS alkalmazás lapon található, és másolja az alkalmazás azonosítóját.

3. Ha az alkalmazás még nem betanítása, jelölje be a **betanítása** betanítása az alkalmazás jobb felső részén gombjára.

4. Ha az alkalmazás még nem tette közzé, válassza ki a **közzététel** megnyitásához a felső navigációs sávon a **közzététel** lap. Válassza ki az éles tárhely és a **közzététel** gombra.

## <a name="modify-the-bot-code"></a>A botot kód módosítása

Ha továbbra is nyissa meg, vagy az első böngészőablakban, válassza ki a második böngészőablakban nyissa **Build** , és válassza **nyitott online kód szerkesztése**.

   ![Nyissa meg az online kód szerkesztése](./media/luis-tutorial-node-bot/bot-service-build.png)

Nyissa meg a kód szerkesztése `app.js`. A következő kódot tartalmazza:

```javascript
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

Az app.js a meglévő leképezések figyelmen kívül lesznek hagyva. Hagyhatja őket. 

## <a name="add-a-dialog-that-matches-homeautomationturnon"></a>Adja hozzá a megfelelő HomeAutomation.TurnOn párbeszédpanel

Másolja az alábbi kódot, és hozzá kell adnia `app.js`.

```javascript
bot.dialog('TurnOn',
    (session) => {
        session.send('You reached the TurnOn intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOn'
})
```

A [megfelelő] [ matches] beállítást a [triggerAction] [ triggerAction] csatolva a párbeszédpanel a leképezés nevét adja meg. A felismerő minden alkalommal, amikor a botot egy utterance kap a felhasználó futtatja. Ha a legmagasabb pontozási célt, hogy az észlelt megegyezik egy `triggerAction` kötve egy párbeszédpanelen, a botot hív meg, hogy a párbeszédpanel.

## <a name="add-a-dialog-that-matches-homeautomationturnoff"></a>Adja hozzá a megfelelő HomeAutomation.TurnOff párbeszédpanel

Másolja az alábbi kódot, és hozzá kell adnia `app.js`.

```javascript
bot.dialog('TurnOff',
    (session) => {
        session.send('You reached the TurnOff intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOff'
})
```
## <a name="test-the-bot"></a>A botot tesztelése

Az Azure portálon, válassza ki a **webes Csevegés tesztelése** a botot teszteléséhez. Próbálja típusú üzenetek like "kapcsolja be a fény" és "kapcsolja ki a melegítőkráter" hozzáadott, akkor a leképezések meghívni.
   ![Webes Csevegés HomeAutomation botot tesztelése](./media/luis-tutorial-node-bot/bot-service-chat-results.png)

> [!TIP]
> Ha talál meg, hogy a botot nem mindig ismeri fel a helyes leképezés vagy entitásokat, a LUIS app a jobb teljesítmény érdekében adjon neki további példa utterances betanítása azt. A LUIS app a botot kód módosítás nélkül is működik. Lásd: [adja hozzá például utterances](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) és [betanítása és a alkalmazás tesztelése a LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/interactive-test).

## <a name="learn-more-about-bot-framework"></a>További tudnivalók Botot keretrendszer
További információ [Botot keretrendszer](https://dev.botframework.com/) és a [3.x](https://github.com/Microsoft/BotBuilder) és [4.x](https://github.com/Microsoft/botbuilder-js) SDK-k.

## <a name="next-steps"></a>További lépések

<!-- From trying the bot, you can see that the recognizer can trigger interruption of the currently active dialog. Allowing and handling interruptions is a flexible design that accounts for what users really do. Learn more about the various actions you can associate with a recognized intent.--> Próbálkozzon más leképezések súgó, a Mégse gombra és a üdvözlés, például hozzáadása a LUIS alkalmazáshoz. Majd adja hozzá az új leképezések a párbeszédpanelek, és tesztelje azokat a botot használatával. 

<!-- 
> [!NOTE] 
> The default LUIS app that the template created contains example utterances for Cancel, Greeting, and Help intents. In the list of apps, find the app that begins with the name specified in **App name** in the **Bot Service** blade when you created the Bot Service. -->

> [!div class="nextstepaction"]
> [Adja hozzá a leképezések](./luis-how-to-add-intents.md)
> [beszéd betanítási művelet](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


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
[Github-BotFramework-Emulator-Download]: https://aka.ms/bot-framework-emulator
[Github-LUIS-Samples]: https://github.com/Microsoft/LUIS-Samples
[Github-LUIS-Samples-node-hotel-bot]: https://github.com/Microsoft/LUIS-Samples/tree/master/bot-integration-samples/hotel-finder/nodejs
[NodeJs]: https://nodejs.org/
[BFPortal]: https://dev.botframework.com/
[RegisterInstructions]: https://docs.microsoft.com/bot-framework/portal-register-bot
[BotFramework]: https://docs.microsoft.com/bot-framework/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions

