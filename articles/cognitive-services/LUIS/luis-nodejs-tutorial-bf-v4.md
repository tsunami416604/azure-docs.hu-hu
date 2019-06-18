---
title: Language Understanding Bot Node.js v4
titleSuffix: Azure Cognitive Services
description: A Node.js használatával hozzon létre egy csevegőrobotot integrált nyelvfelismeréssel (LUIS). Ez csevegőrobot a Human Resources app használatával rövid idő alatt megvalósít egy robotmegoldást. A robot összeállításához a Bot Framework 4-es verzióját és az Azure webalkalmazás-robotot használja.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 832a62c5cc5440d81f4b92d2463a563f5bb884a3
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150828"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Oktatóanyag: Használja a Web App Bot engedélyezve van, a Language Understanding használatánál a node.js-ben 

A Node.js használatával hozhat létre csevegőrobotot integrálva van a language understanding (LUIS). A robot épül fel az Azure-ral [Web app bot](https://docs.microsoft.com/azure/bot-service/) erőforrás és [Bot Framework version](https://github.com/Microsoft/botbuilder-dotnet) V4.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Webalkalmazás-robot létrehozása. Ez a folyamat egy új LUIS-appot hoz létre az Ön számára.
> * Töltse le a robot-projektet a webes bot service által létrehozott
> * A robot és az emulátor elindítása a helyi számítógépen
> * Kimondottszöveg-eredmények megtekintése a robotban

## <a name="prerequisites"></a>Előfeltételek

* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Web app-robot-erőforrás létrehozása

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
    |App service-csomag/hely|Ne változtassa meg a megadott alapértelmezett értéket.|
    |Application Insights|Ne változtassa meg a megadott alapértelmezett értéket.|
    |A Microsoft App ID azonosítója és jelszava|Ne változtassa meg a megadott alapértelmezett értéket.|

1. Az a **Bot sablon**, jelölje be az alábbiakat, majd válassza ki a **kiválasztása** alatt ezek a beállítások gombra:

    |Beállítás|Cél|Kiválasztás|
    |--|--|--|
    |SDK verziója|Bot Framework verziója|**SDK v4**|
    |SDK nyelve|Robot programozási nyelve|**Node.js**|
    |A robot|Robot típusa|**Alapszintű robot**|
    
1. Kattintson a **Létrehozás** gombra. Ezzel létrehozza a robotszolgáltatást, és üzembe helyezi azt az Azure-ban. A folyamat egyik része egy `luis-nodejs-bot-XXXX` nevű LUIS-appot hoz létre. Ez a név a /Azure Bot Service-alkalmazás neve alapján.

    [![Web app bot létrehozása](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

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

    ![Képernyőkép az Azure Portalon, adja meg a "hello" szöveggel.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

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

1. Nyissa meg a **párbeszédpanelek -> luisHelper.js** fájlt. A rendszer ezen a ponton küldi a robotban megadott felhasználói kimondott szöveget a LUIS-hoz. A LUIS válasz, a metódus egy **bookDetails** JSON-objektum. A saját robot létrehozásakor is készítsen a saját objektum visszaadása a LUIS a részleteket. 

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

1. Nyissa meg **párbeszédpanelek -> bookingDialog.js** megérteni, hogyan a BookingDetails objektum a beszélgetés folyamat kezelésére szolgál. Utazás részletei a rendszer felkéri a lépéseket, majd a teljes foglalási megerősítette, és végül ismétlődik a felhasználó számára. 

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


## <a name="install-dependencies-and-start-the-bot-code-in-visual-studio"></a>Függőségek telepítése, és indítsa el a robot kódot a Visual Studióban

1. A vscode-ban, az integrált terminálon, a függőségek telepítéséhez a parancs `npm install`.
1. Emellett az integrált terminálon indítsa el a robot a paranccsal `npm start`. 


## <a name="use-the-bot-emulator-to-test-the-bot"></a>A robot teszteléséhez a robot emulator használata

1. A robot emulátor kezdődik, és válassza ki **nyissa meg a robot**.
1. Az a **nyissa meg a robot** felugró párbeszédpanel, írja be a robot URL-CÍMÉT, például `http://localhost:3978/api/messages`. A `/api/messages` útvonal a robot a webcímet.
1. Adja meg a **Microsoft App ID** és **Microsoft App jelszó**, található az a **.env** fájlt a letöltött bot kód gyökerében.

    Igény szerint hozhat létre egy új robot, konfigurációs, és másolja a `MicrosoftAppId` és `MicrosoftAppPassword` származó a **.env** fájlt a Visual Studio-projektben a robot. A robot konfigurációs fájl neve megegyezik a robot neve kell lennie. 

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

1. A robot emulátorban, adja meg a `Hello` és az alapszintű robot, a kapott ugyanazt a választ kaphat a **vizsgálat a webes csevegési**.

    [![Alapszintű robot válasz emulátorban](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Kérdés feltevése robot számára a címjegyzék repülési célt

1. A robot emulátorban repülőjáratra repülőjegyet írja be a következő utterance (kifejezés): 

    ```bot
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    A robot-emulátor megerősítését kéri. 

1. Válassza ki **Igen**. A robot fűzi hozzá a műveletek összegzését. 
1. A robot-emulátor a naplóból, válassza ki a tartalmazó sort `Luis Trace`. Ez megjeleníti a JSON-válasz a LUIS a leképezés és entitásai az utterance (kifejezés).

    [![Alapszintű robot válasz emulátorban](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

## <a name="learn-more-about-the-web-app-bot-and-framework"></a>További információ a Web App Bot és keretrendszer

Az Azure Bot Service a Bot Framework SDK-t használja. További információk az SDK-ról és a Bot Frameworkről:

* Az [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4 dokumentációja
* [Bot Builder minták](https://github.com/Microsoft/botbuilder-samples)
* [Bot Builder Node.js SDK](https://github.com/Microsoft/botbuilder-js)
* [Bot Builder eszközök](https://github.com/Microsoft/botbuilder-tools):

## <a name="next-steps"></a>További lépések

Még több [minták](https://github.com/microsoft/botframework-solutions) a természetes nyelvi robotokat. 

> [!div class="nextstepaction"]
> [Egy egyéni tulajdonosnév-tartomány Language Understanding alkalmazás készítése](luis-quickstart-intents-only.md)