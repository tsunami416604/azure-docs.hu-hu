---
title: 'Oktatóanyag: Nyelvmegértés Bot Node.js v4'
description: A Node.js használatával hozzon létre egy, a nyelvi megértéssel (LUIS) integrált csevegőrobotot ebben az oktatóanyagban. Ez csevegőrobot a Human Resources app használatával rövid idő alatt megvalósít egy robotmegoldást. A robot összeállításához a Bot Framework 4-es verzióját és az Azure webalkalmazás-robotot használja.
ms.topic: tutorial
ms.date: 02/03/2020
ms.openlocfilehash: 3ce12176957412a5599ced8b043f553969194efb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987840"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Oktatóanyag: A Node.js nyelvismertetési funkcióval rendelkező webalkalmazás-robot használata

A Node.js használatával a nyelvi megértéssel (LUIS) integrált csevegőrobotot hozhat létre. A robot az Azure [Web app bot](https://docs.microsoft.com/azure/bot-service/) erőforrásés a Robot keretrendszer V4-es [verziója.](https://github.com/Microsoft/botbuilder-dotnet)

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Webalkalmazás-robot létrehozása. Ez a folyamat egy új LUIS-appot hoz létre az Ön számára.
> * A webrobot szolgáltatás által létrehozott robotprojekt letöltése
> * A robot és az emulátor elindítása a helyi számítógépen
> * Kimondottszöveg-eredmények megtekintése a robotban

## <a name="prerequisites"></a>Előfeltételek

* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio kód](https://code.visualstudio.com/Download)


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
    |App Service-csomag/Hely|Ne változtasson a megadott alapértelmezett értékről.|
    |Application Insights|Ne változtasson a megadott alapértelmezett értékről.|
    |Microsoft App Azonosító és jelszó|Ne változtasson a megadott alapértelmezett értékről.|

1. A **Robotsablonban**válassza a következőket, majd a beállítások alatt válassza a **Kijelölés** gombot:

    |Beállítás|Cél|Kiválasztás|
    |--|--|--|
    |SDK verziója|Bot Framework verziója|**SDK v4**|
    |SDK nyelve|Robot programozási nyelve|**Node.js**|
    |Bot|Robot típusa|**Alapszintű robot**|

1. Kattintson a **Létrehozás** gombra. Ezzel létrehozza a robotszolgáltatást, és üzembe helyezi azt az Azure-ban. A folyamat egyik része egy `luis-nodejs-bot-XXXX` nevű LUIS-appot hoz létre. Ez a név az /Azure Bot Service alkalmazás nevén alapul.

    > [!div class="mx-imgBorder"]
    > [![Webalkalmazás-robot létrehozása](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    A folytatás előtt várja meg, amíg létrejön a robotszolgáltatás.

## <a name="the-bot-has-a-language-understanding-model"></a>A robot nyelvtudási modellel rendelkezik

A bot szolgáltatás létrehozási folyamat is létrehoz egy új LUIS-alkalmazást szándékokkal és példa utterances. A robot szándékleképezéseket biztosít az új LUIS-apphoz a következő szándékok esetén:

|Alapszintű robot LUIS-szándékai|példa kimondott szöveg|
|--|--|
|Könyvjárat|`Travel to Paris`|
|Mégse|`bye`|
|GetWeather (Időjárás)|`what's the weather like?`|
|None|Az app tartományán kívül bármi.|

## <a name="test-the-bot-in-web-chat"></a>Tesztelje a bot web chat

1. Miközben az új robot Azure-portálján van, válassza a **Tesztelés a webes csevegésben**lehetőséget.
1. Az **Üzenet szövegének beírása** `Book a flight from Seattle to Berlin tomorrow`mezőbe írja be a szöveget. A robot annak ellenőrzésével válaszol, hogy szeretne-e repülőjegyet foglalni.

    ![Képernyőkép az Azure Portalról, írja be a "hello" szöveget.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    A tesztfunkció segítségével gyorsan tesztelheti a robotot. A teljes ebbel végzett teszteléshez, beleértve a hibakeresést is, töltse le a robotkódot, és használja a Visual Studio alkalmazást.

## <a name="download-the-web-app-bot-source-code"></a>A webalkalmazás robotforráskódjának letöltése
A webalkalmazás-robot kódjának fejlesztéséhez töltse le a kódot a helyi számítógépre.

1. Az Azure Portalon a **Robot felügyelete** szakaszban kattintson a **Build** elemre.

1. Válassza a **Robot forráskódjának letöltse** lehetőséget.

    [![Letöltés web app bot forráskód alapvető bot](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Amikor az előugró párbeszédpanel megkérdezi az **Alkalmazásbeállítások felvétele a letöltött zip-fájlba?** lehetőséget választja az **Igen**gombra. Ez biztosítja a LUIS-beállításokat.

1. Tömörített forráskód esetén az üzenet tartalmazza a kód letöltéséhez szükséges hivatkozást. Kattintson a hivatkozásra.

1. Mentse a .zip-fájlt a helyi számítógépére, és bontsa ki a fájlokat. Nyissa meg a projektet a Visual Studio segítségével.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Tekintse át a kódot, hogy elküldhesse az utterance (kifejezés) a LUIS-nak, és választ kapjon

1. Ha a felhasználói utterance (kifejezés) a LUIS előrejelzési végpontra szeretné küldeni, nyissa meg a **-> a flightBookingRecognizer.js** fájlt. A rendszer ezen a ponton küldi a robotban megadott felhasználói kimondott szöveget a LUIS-hoz. A LUIS válasza a **luisquery metódusból** származik.

    ````javascript
    class FlightBookingRecognizer {

        ...

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        ...

    }
    ````

1. A **párbeszédablakok -> mainDialog** rögzíti az utterance (kifejezés) és elküldi azt a executeLuisQuery az actStep metódusban.


    ````javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                this.recognizer = new LuisRecognizer(config, {}, true);
            }
        }

        get isConfigured() {
            return (this.recognizer !== undefined);
        }

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        getFromEntities(result) {
            let fromValue, fromAirportValue;
            if (result.entities.$instance.From) {
                fromValue = result.entities.$instance.From[0].text;
            }
            if (fromValue && result.entities.From[0].Airport) {
                fromAirportValue = result.entities.From[0].Airport[0][0];
            }

            return { from: fromValue, airport: fromAirportValue };
        }

        getToEntities(result) {
            let toValue, toAirportValue;
            if (result.entities.$instance.To) {
                toValue = result.entities.$instance.To[0].text;
            }
            if (toValue && result.entities.To[0].Airport) {
                toAirportValue = result.entities.To[0].Airport[0][0];
            }

            return { to: toValue, airport: toAirportValue };
        }

        /**
         * This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
         * TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
         */
        getTravelDate(result) {
            const datetimeEntity = result.entities.datetime;
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;

            const timex = datetimeEntity[0].timex;
            if (!timex || !timex[0]) return undefined;

            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }

    module.exports.FlightBookingRecognizer = FlightBookingRecognizer;
    ````
<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Használja a bot emulátor, hogy tesztelje a bot

Kérdezzen botegy kérdést a Book Flight szándékkal.

1. Kezdje el a Bot emulátort, és válassza **az Open Bot**lehetőséget.
1. A **Robot** előugró ablakának megnyitása párbeszédpanelen adja `http://localhost:3978/api/messages`meg a robot URL-címét, például . Az `/api/messages` útvonal a robot webcíme.
1. Adja meg a **Microsoft App ID** és a **Microsoft App jelszavát,** amely a letöltött robotkód gyökérében található **.env** fájlban található.

1. A bot emulátor, `Book a flight from Seattle to Berlin tomorrow` írja be, és kap ugyanazt a választ az alapvető bot, amit kapott a **Test in Web Chat**.

    [![Alapvető bot válasz emulátor](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Válassza az **Igen** lehetőséget. A robot a műveletek összegzésével válaszol.
1. A bot emulátor naplójából válassza ki `Luis Trace`a . Ez megjeleníti a JSON válasz a LUIS a szándék és az entitások az utterance (kifejezés).

    [![Alapvető bot válasz emulátor](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>További lépések

További [minták](https://github.com/microsoft/botframework-solutions) társalgási botokkal.

> [!div class="nextstepaction"]
> [Nyelvi tudásra vonatkozó alkalmazás létrehozása egyéni tárgytartománysal](luis-quickstart-intents-only.md)