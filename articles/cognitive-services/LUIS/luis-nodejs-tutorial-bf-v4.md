---
title: LUIS-robot létrehozása a Node.js használatával – Oktatóanyag – Webalkalmazás-robot – Bot Framework SDK 4.0
titleSuffix: Azure Cognitive Services
description: A Node.js használatával hozzon létre egy csevegőrobotot integrált nyelvfelismeréssel (LUIS). Ez csevegőrobot a Human Resources app használatával rövid idő alatt megvalósít egy robotmegoldást. A robot összeállításához a Bot Framework 4-es verzióját és az Azure webalkalmazás-robotot használja.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: ad21754b3f55a0d14bb43a2898d5bd4b8b8150ae
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385906"
---
# <a name="tutorial-luis-bot-in-nodejs"></a>Oktatóanyag: LUIS-robot a Node.js-ben
A Node.js használatával létrehozhat egy integrált nyelvfelismerési (LUIS) képességekkel rendelkező csevegőrobotot. Ez a robot a HomeAutomation app használatával valósítja meg a robotmegoldást. A robot összeállításához a [Bot Framework 4-es verzióját](https://github.com/Microsoft/botbuilder-js) és az Azure [webalkalmazás-robotot](https://docs.microsoft.com/azure/bot-service/) használja.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Webalkalmazás-robot létrehozása. Ez a folyamat egy új LUIS-appot hoz létre az Ön számára.
> * Előre összeállított tartomány hozzáadása az új LUIS-modellhez
> * A Web Bot Service által létrehozott projekt letöltése
> * A robot és az emulátor elindítása a helyi számítógépen
> * A robot kódjának módosítása az új LUIS-szándékokhoz
> * Kimondottszöveg-eredmények megtekintése a robotban

## <a name="prerequisites"></a>Előfeltételek

<!--* Samples from 
https://github.com/Microsoft/BotBuilder-Samples/tree/v4/javascript_nodejs/12.nlp-with-luis-->
* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-web-app-bot"></a>Webalkalmazás-robot létrehozása

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Új erőforrás létrehozása** lehetőséget.

2. A keresőmezőben keresse meg, majd válassza ki a **Webalkalmazás-robot** elemet. Kattintson a **Létrehozás** gombra.

3. A **Bot Service** szolgáltatásban adja meg a szükséges adatokat:

    |Beállítás|Cél|Javasolt beállítás|
    |--|--|--|
    |Robot neve|Erőforrás neve|`luis-nodejs-bot-` + `<your-name>`, például: `luis-nodejs-bot-johnsmith`|
    |Előfizetés|Az előfizetés, amelyben létre szeretné hozni a robotot.|Az Ön elsődleges előfizetése.
    |Erőforráscsoport|Az Azure-erőforrások logikai csoportja|Hozzon létre új csoportot a robothoz használt erőforrások tárolására. A csoport neve legyen: `luis-nodejs-bot-resource-group`.|
    |Hely|Azure-régió – Nem kell a LUIS létrehozási vagy közzétételi régiójával azonosnak lennie.|`westus`|
    |Tarifacsomag|Szolgáltatáskérési korlátokhoz és számlázáshoz használható.|Az `F0` az ingyenes szint.
    |App neve|A név lesz az altartomány a robot felhőbeli üzembe helyezésekor (például humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, például: `luis-nodejs-bot-johnsmith`|
    |Robotsablon|A Bot Framework beállításai – lásd a következő táblázatot|
    |A LUIS-app helye|Egyeznie kell a LUIS-erőforrás régiójával|`westus`|

4. A **Bot template settings** (Robotsablon beállításai) menüpontban válassza az alábbi lehetőségeket, majd kattintson a beállítások alatt található **Select** (Kiválasztás) gombra:

    |Beállítás|Cél|Kiválasztás|
    |--|--|--|
    |SDK verziója|Bot Framework verziója|**SDK v4**|
    |SDK nyelve|Robot programozási nyelve|**Node.js**|
    |Echo/Alapszintű robot|Robot típusa|**Alapszintű robot**|
    
5. Kattintson a **Létrehozás** gombra. Ezzel létrehozza a robotszolgáltatást, és üzembe helyezi azt az Azure-ban. A folyamat egyik része egy `luis-nodejs-bot-XXXX` nevű LUIS-appot hoz létre. Ez a név a robot és az app előző szakaszban szereplő nevén alapul.

    [ ![Webalkalmazás-robot létrehozása](./media/bfv4-nodejs/create-web-app-service.png) ](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

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
2. A **My Apps** (Saját alkalmazások) lapon kattintson a **Created date** (Létrehozás dátuma) oszlopra, így az app létrehozásának dátuma szerint rendezheti azt. Az Azure Bot Service egy új appot hozott létre az előző szakaszban. A neve `luis-nodejs-bot-` + `<your-name>` + 4 véletlenszerű karakter.
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

6. Nyissa meg a bot.js fájlt, és keresse meg a `const results = await this.luisRecognizer.recognize(context);` kifejezést. A rendszer ezen a ponton küldi a robotban megadott felhasználói kimondott szöveget a LUIS-hoz.

    ```javascript
    /**
     * Driver code that does one of the following:
     * 1. Display a welcome card upon startup
     * 2. Use LUIS to recognize intents
     * 3. Start a greeting dialog
     * 4. Optionally handle Cancel or Help interruptions
     *
     * @param {Context} context turn context from the adapter
     */
    async onTurn(context) {
        // Create a dialog context
        const dc = await this.dialogs.createContext(context);

        if(context.activity.type === ActivityTypes.Message) {
            // Perform a call to LUIS to retrieve results for the current activity message.
            const results = await this.luisRecognizer.recognize(context);
            
            const topIntent = LuisRecognizer.topIntent(results);

            // handle conversation interrupts first
            const interrupted = await this.isTurnInterrupted(dc, results);
            if(interrupted) {
                return;
            }

            // Continue the current dialog
            const dialogResult = await dc.continue();

            switch(dialogResult.status) {
                case DialogTurnStatus.empty:
                    switch (topIntent) {
                        case GREETING_INTENT:
                            await dc.begin(GREETING_DIALOG);
                            break;

                        case NONE_INTENT:
                        default:
                            // help or no intent identified, either way, let's provide some help
                            // to the user
                            await dc.context.sendActivity(`I didn't understand what you just said to me. topIntent ${topIntent}`);
                            break;
                    }

                case DialogTurnStatus.waiting:
                    // The active dialog is waiting for a response from the user, so do nothing
                break;

                case DialogTurnStatus.complete:
                    await dc.end();
                    break;

                default:
                    await dc.cancelAll();
                    break;

            }

        } else if (context.activity.type === 'conversationUpdate' && context.activity.membersAdded[0].name === 'Bot') {
            // When activity type is "conversationUpdate" and the member joining the conversation is the bot
            // we will send our Welcome Adaptive Card.  This will only be sent once, when the Bot joins conversation
            // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
            const welcomeCard = CardFactory.adaptiveCard(WelcomeCard);
            await context.sendActivity({ attachments: [welcomeCard] });
        }
    }
    ```

    A robot elküldi a felhasználói kimondott szöveget a LUIS-hoz, és lekéri az eredményeket. A felső szándék határozza meg a beszélgetés folyamát. 


## <a name="start-the-bot"></a>A robot indítása
A kód vagy a beállítások módosítása előtt ellenőrizze a robot működését. 

1. A Visual Studio Code-ban nyisson meg egy terminálablakot. 

2. Telepítse a robothoz szükséges NPM-függőségeket. 

    ```bash
    npm install
    ```
3. Hozzon létre egy fájlt, amely a robot kódja által keresett környezeti változókat tartalmazza. A fájl neve legyen `.env`. Adja hozzá az alábbi környezet változókat:

    <!--there is no code language that represents an .env file correctly-->
    ```
    botFilePath=
    botFileSecret=
    ```

    Állítsa be a környezeti változók értékét a **[Webalkalmazás-robot letöltése](#download-the-web-app-bot)** című szakasz 1. lépésében az Azure Bot Service alkalmazásbeállításaiból kimásolt értékekre.

4. Indítsa el a robotot figyelés üzemmódban. A kódon az indítást követően végrehajtott módosítások az app automatikus újraindítását eredményezik.

    ```bash
    npm run watch
    ```

5. A robot indulásakor a terminálablakban megjelennek azok a porton, amelyeket a robot használ:

    ```
    > basic-bot@0.1.0 start C:\Users\pattiowens\repos\BFv4\luis-nodejs-bot-src
    > node ./index.js NODE_ENV=development

    restify listening to http://[::]:3978
    
    Get the Emulator: https://aka.ms/botframework-emulator
    
    To talk to your bot, open the luis-nodejs-bot-pattiowens.bot file in the Emulator
    ```

## <a name="start-the-emulator"></a>Az emulátor indítása

1. Indítsa el a Bot Emulatort. 

2. A Bot Emulatorban válassza ki a projekt gyökérmappájában található *.bot fájlt. Ez a `.bot` fájl tartalmazza a robot üzenetekhez tartozó URL-végpontját:

    [ ![Bot Emulator v4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png) ](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. Adja meg a robot titkos kódját, amelyet a **[Webalkalmazás-robot letöltése](#download-the-web-app-bot)** című szakasz 1. lépésében az Azure Bot Service alkalmazásbeállításaiból másolt ki. Ez lehetővé teszi, hogy az emulátor hozzáférjen a .bot fájl titkosított mezőihez.

    ![A Bot Emulator v4 titkos kódja](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)


4. A Bot Emulatorban írja be a `Hello` kifejezést, és kérje le az alapszintű robothoz tartozó megfelelő választ.

    [ ![Alapszintű robot válasza az emulátorban](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png) ](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>A robot kódjának módosítása 

A `bot.js` fájlban adja hozzá az új szándékok kezeléséhez szükséges kódot. 

1. A fájl tetején keresse meg a **Támogatott LUIS-szándékok** szakaszt, és adja hozzá a HomeAutomation-szándékokhoz tartozó állandókat:

    ```javascript
    // Supported LUIS Intents
    const GREETING_INTENT = 'Greeting';
    const CANCEL_INTENT = 'Cancel';
    const HELP_INTENT = 'Help';
    const NONE_INTENT = 'None';
    const TURNON_INTENT = 'HomeAutomation_TurnOn'; // new intent
    const TURNOFF_INTENT = 'HomeAutomation_TurnOff'; // new intent
    ```

    Figyelje meg, hogy a tartomány és a LUIS portálról származó app szándéka között elhelyezkedő pontot (`.`) a rendszer aláhúzásjelre (`_`) cserélte. 

2. Keresse meg az **isTurnInterrupted** kifejezést, amely a kimondott szöveg LUIS-előrejelzését fogadja, és adjon hozzá egy sort az eredmény konzolon való kiíratásához.

    ```node
    /**
     * Look at the LUIS results and determine if we need to handle
     * an interruptions due to a Help or Cancel intent
     *
     * @param {DialogContext} dc - dialog context
     * @param {LuisResults} luisResults - LUIS recognizer results
     */
    async isTurnInterrupted(dc, luisResults) {
        console.log(JSON.stringify(luisResults));
    ...
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

3. Adja hozzá a szándékokat az onTurn metódus switch utasításához a `DialogTurnStatus.empty` esetben:

    ```javascript
    switch (topIntent) {
        case GREETING_INTENT:
            await dc.begin(GREETING_DIALOG);
            break;

        // New HomeAutomation.TurnOn intent
        case TURNON_INTENT: 

            await dc.context.sendActivity(`TurnOn intent found, entities included: ${JSON.stringify(results.entities)}`);
            break;

        // New HomeAutomation.TurnOff intent
        case TURNOFF_INTENT: 

            await dc.context.sendActivity(`TurnOff intent found, entities included: ${JSON.stringify(results.entities)}`);
            break;

        case NONE_INTENT:
        default:
            // help or no intent identified, either way, let's provide some help
            // to the user
            await dc.context.sendActivity(`I didn't understand what you just said to me. topIntent ${topIntent}`);
            break;
    }
    ```

## <a name="view-results-in-bot"></a>Eredmények megtekintése a robotban

1. A Bot Emulatorban írja be a következő szöveget: `Turn on the livingroom lights to 50%`

2. A robot a következővel válaszol:

    ```JSON
    TurnOn intent found, entities included: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```

## <a name="learn-more-about-bot-framework"></a>További tudnivalók a Bot Frameworkről
Az Azure Bot Service a Bot Framework SDK-t használja. További információk az SDK-ról és a Bot Frameworkről:

* Az [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4 dokumentációja
* [Bot Builder minták](https://github.com/Microsoft/botbuilder-samples)
* [Bot Builder SDK](https://docs.microsoft.com/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Bot Builder eszközök](https://github.com/Microsoft/botbuilder-tools):

## <a name="next-steps"></a>További lépések

Létrehozott egy Azure Bot Service szolgáltatást, kimásolta a robot titkos kódját, valamint a .bot-fájl elérési útját, és letöltötte a kódot tartalmazó zip-fájlt. Hozzáadta az előre összeállított HomeAutomation-tartományt az új Azure Bot Service részeként létrehozott LUIS-apphoz, majd ismét betanította és közzétette az alkalmazást. Kibontotta a kódprojektet, létrehozott egy környezeti fájlt (`.env`), és beállított a robot titkos kódját, valamint a .bot-fájl elérési útját. A bot.js fájlban hozzáadta a két új szándék kezeléséhez szükséges kódot. Majd letesztelte a robotot a Bot Emulatorral, hogy láthassa az új szándékok egyikéhez tartozó kimondott szövegre adott LUIS-választ. 


> [!div class="nextstepaction"]
> [Egyéni tartomány létrehozása a LUIS-ban](luis-quickstart-intents-only.md)
