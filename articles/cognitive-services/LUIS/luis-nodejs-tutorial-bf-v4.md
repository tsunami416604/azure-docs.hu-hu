---
title: 'Oktatóanyag: Language Understanding bot Node. js v4'
titleSuffix: Azure Cognitive Services
description: A Node.js használatával hozzon létre egy csevegőrobotot integrált nyelvfelismeréssel (LUIS). Ez csevegőrobot a Human Resources app használatával rövid idő alatt megvalósít egy robotmegoldást. A robot összeállításához a Bot Framework 4-es verzióját és az Azure webalkalmazás-robotot használja.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 8227a7683c1333b21537630f7f074f624926f984
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375771"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Oktatóanyag: Webalkalmazás-robot használata Language Understanding a Node. js-ben 

A Node. js használatával a Language Understanding (LUIS) nyelvvel integrált csevegési robot hozható létre. A robot az Azure [Web App bot](https://docs.microsoft.com/azure/bot-service/) Resource és a bot Framework v4-es [verziójával](https://github.com/Microsoft/botbuilder-dotnet) készült.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Webalkalmazás-robot létrehozása. Ez a folyamat egy új LUIS-appot hoz létre az Ön számára.
> * A web bot Service által létrehozott robot-projekt letöltése
> * A robot és az emulátor elindítása a helyi számítógépen
> * Kimondottszöveg-eredmények megtekintése a robotban

## <a name="prerequisites"></a>Előfeltételek

* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Webalkalmazás-robot erőforrás létrehozása

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Új erőforrás létrehozása** lehetőséget.

1. A keresőmezőben keresse meg, majd válassza ki a **Webalkalmazás-robot** elemet. Kattintson a **Létrehozás** gombra.

1. A **Bot Service** szolgáltatásban adja meg a szükséges adatokat:

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
    |App Service-csomag/hely|Ne módosítsa a megadott alapértelmezett értéket.|
    |Application Insights|Ne módosítsa a megadott alapértelmezett értéket.|
    |Microsoft-alkalmazás azonosítója és jelszava|Ne módosítsa a megadott alapértelmezett értéket.|

1. A **bot-sablonban**válassza ki a következőt, majd a beállítások területen válassza a **kiválasztás** gombot:

    |Beállítás|Cél|Kiválasztás|
    |--|--|--|
    |SDK verziója|Bot Framework verziója|**SDK v4**|
    |SDK nyelve|Robot programozási nyelve|**Node.js**|
    |Bot|Robot típusa|**Alapszintű robot**|
    
1. Kattintson a **Létrehozás** gombra. Ezzel létrehozza a robotszolgáltatást, és üzembe helyezi azt az Azure-ban. A folyamat egyik része egy `luis-nodejs-bot-XXXX` nevű LUIS-appot hoz létre. Ez a név a/Azure bot Service-alkalmazás nevén alapul.

    [![Webalkalmazás robotjának létrehozása](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

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

    ![A Azure Portal képernyőképe a "Hello" szöveg beírásával.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

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

1. Nyissa meg a **párbeszédpaneleket – > luisHelper. js** fájlt. A rendszer ezen a ponton küldi a robotban megadott felhasználói kimondott szöveget a LUIS-hoz. A LUIS válaszát a metódus **bookDetails** JSON-objektumként adja vissza. Saját robot létrehozásakor létre kell hoznia egy saját objektumot is, amely a LUIS-ből származó részleteket adja vissza. 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    
    class LuisHelper {
        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {*} logger
         * @param {TurnContext} context
         */
        static async executeLuisQuery(logger, context) {
            const bookingDetails = {};
    
            try {
                const recognizer = new LuisRecognizer({
                    applicationId: process.env.LuisAppId,
                    endpointKey: process.env.LuisAPIKey,
                    endpoint: `https://${ process.env.LuisAPIHostName }`
                }, {}, true);
    
                const recognizerResult = await recognizer.recognize(context);
    
                const intent = LuisRecognizer.topIntent(recognizerResult);
    
                bookingDetails.intent = intent;
    
                if (intent === 'Book_flight') {
                    // We need to get the result from the LUIS JSON which at every level returns an array
    
                    bookingDetails.destination = LuisHelper.parseCompositeEntity(recognizerResult, 'To', 'Airport');
                    bookingDetails.origin = LuisHelper.parseCompositeEntity(recognizerResult, 'From', 'Airport');
    
                    // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                    // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                    bookingDetails.travelDate = LuisHelper.parseDatetimeEntity(recognizerResult);
                }
            } catch (err) {
                logger.warn(`LUIS Exception: ${ err } Check your LUIS configuration`);
            }
            return bookingDetails;
        }
    
        static parseCompositeEntity(result, compositeName, entityName) {
            const compositeEntity = result.entities[compositeName];
            if (!compositeEntity || !compositeEntity[0]) return undefined;
    
            const entity = compositeEntity[0][entityName];
            if (!entity || !entity[0]) return undefined;
    
            const entityValue = entity[0][0];
            return entityValue;
        }
    
        static parseDatetimeEntity(result) {
            const datetimeEntity = result.entities['datetime'];
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;
    
            const timex = datetimeEntity[0]['timex'];
            if (!timex || !timex[0]) return undefined;
    
            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }
    
    module.exports.LuisHelper = LuisHelper;
    ```

1. Nyissa meg a **párbeszédpaneleket – > bookingDialog. js fájlt** , hogy megtudja, hogyan használják a BookingDetails objektumot a beszélgetési folyamat kezeléséhez. Az utazási adatokat a rendszer a következő lépésekben kérdezi le: a teljes foglalás megerősítve, végül pedig visszakerül a felhasználóhoz. 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { TimexProperty } = require('@microsoft/recognizers-text-data-types-timex-expression');
    const { ConfirmPrompt, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');
    const { CancelAndHelpDialog } = require('./cancelAndHelpDialog');
    const { DateResolverDialog } = require('./dateResolverDialog');
    
    const CONFIRM_PROMPT = 'confirmPrompt';
    const DATE_RESOLVER_DIALOG = 'dateResolverDialog';
    const TEXT_PROMPT = 'textPrompt';
    const WATERFALL_DIALOG = 'waterfallDialog';
    
    class BookingDialog extends CancelAndHelpDialog {
        constructor(id) {
            super(id || 'bookingDialog');
    
            this.addDialog(new TextPrompt(TEXT_PROMPT))
                .addDialog(new ConfirmPrompt(CONFIRM_PROMPT))
                .addDialog(new DateResolverDialog(DATE_RESOLVER_DIALOG))
                .addDialog(new WaterfallDialog(WATERFALL_DIALOG, [
                    this.destinationStep.bind(this),
                    this.originStep.bind(this),
                    this.travelDateStep.bind(this),
                    this.confirmStep.bind(this),
                    this.finalStep.bind(this)
                ]));
    
            this.initialDialogId = WATERFALL_DIALOG;
        }
    
        /**
         * If a destination city has not been provided, prompt for one.
         */
        async destinationStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            if (!bookingDetails.destination) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'To what city would you like to travel?' });
            } else {
                return await stepContext.next(bookingDetails.destination);
            }
        }
    
        /**
         * If an origin city has not been provided, prompt for one.
         */
        async originStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the response to the previous step's prompt
            bookingDetails.destination = stepContext.result;
            if (!bookingDetails.origin) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'From what city will you be travelling?' });
            } else {
                return await stepContext.next(bookingDetails.origin);
            }
        }
    
        /**
         * If a travel date has not been provided, prompt for one.
         * This will use the DATE_RESOLVER_DIALOG.
         */
        async travelDateStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.origin = stepContext.result;
            if (!bookingDetails.travelDate || this.isAmbiguous(bookingDetails.travelDate)) {
                return await stepContext.beginDialog(DATE_RESOLVER_DIALOG, { date: bookingDetails.travelDate });
            } else {
                return await stepContext.next(bookingDetails.travelDate);
            }
        }
    
        /**
         * Confirm the information the user has provided.
         */
        async confirmStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.travelDate = stepContext.result;
            const msg = `Please confirm, I have you traveling to: ${ bookingDetails.destination } from: ${ bookingDetails.origin } on: ${ bookingDetails.travelDate }.`;
    
            // Offer a YES/NO prompt.
            return await stepContext.prompt(CONFIRM_PROMPT, { prompt: msg });
        }
    
        /**
         * Complete the interaction and end the dialog.
         */
        async finalStep(stepContext) {
            if (stepContext.result === true) {
                const bookingDetails = stepContext.options;
    
                return await stepContext.endDialog(bookingDetails);
            } else {
                return await stepContext.endDialog();
            }
        }
    
        isAmbiguous(timex) {
            const timexPropery = new TimexProperty(timex);
            return !timexPropery.types.has('definite');
        }
    }
    
    module.exports.BookingDialog = BookingDialog;
    ```


## <a name="install-dependencies-and-start-the-bot-code-in-visual-studio"></a>Függőségek telepítése és a robot kódjának elindítása a Visual Studióban

1. A VSCode-ben az integrált terminálból telepítse a függőségeket a `npm install`paranccsal.
1. Az integrált terminálból is indítsa el a robotot a paranccsal `npm start`. 


## <a name="use-the-bot-emulator-to-test-the-bot"></a>A bot-emulátor használata a robot teszteléséhez

1. Indítsa el a robot-emulátort, és válassza a **robot megnyitása**lehetőséget.
1. A **bot megnyitása** előugró ablakban adja meg a robot URL-címét, például `http://localhost:3978/api/messages`:. Az `/api/messages` útvonal a robot webes címe.
1. Adja meg a **Microsoft-alkalmazás azonosítóját** és a **Microsoft-alkalmazás jelszavát**, amely a letöltött robot gyökerében található **. env** fájlban található.

    Szükség esetén létrehozhat egy új bot-konfigurációt, és a robothoz `MicrosoftAppPassword` tartozó Visual Studio-projektben található **. env** fájlból másolhatja `MicrosoftAppId` . A bot-konfigurációs fájl nevének meg kell egyeznie a robot nevével. 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from .env>",
                "appPassword": "<appPassword from .env>",
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

    [![Alapszintű bot-válasz az emulátorban](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Kérdezze meg a robotot a könyv repülési szándékáról

1. A robot-emulátorban foglaljon le egy repülőjáratot a következő kifejezés beírásával: 

    ```console
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    A robot-emulátor megkéri a megerősítést. 

1. Válassza az **Igen**lehetőséget. A robot a műveleteinek összegzésével válaszol. 
1. A robot-emulátor naplójából válassza ki a sort, amely tartalmazza `Luis Trace`a elemet. Ez megjeleníti a LUIS által a cél és a Kimondás entitásai számára küldött JSON-választ.

    [![Alapszintű bot-válasz az emulátorban](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>További lépések

További [mintákat](https://github.com/microsoft/botframework-solutions) tekinthet meg a társalgási robotokkal. 

> [!div class="nextstepaction"]
> [Language Understanding-alkalmazás létrehozása egyéni tárgyú tartománnyal](luis-quickstart-intents-only.md)