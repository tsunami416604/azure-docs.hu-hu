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
ms.date: 09/06/2019
ms.author: diberry
ms.openlocfilehash: 8f0438ab015f9d16fd3776421b8d0032fc0a0639
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772903"
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
|GetWeather|`what's the weather like?`|
|Nincsenek|Az app tartományán kívül bármi.|

## <a name="test-the-bot-in-web-chat"></a>A robot webes csevegési tesztelése

1. Miközben az új robot Azure Portal továbbra is elérhető, válassza a **tesztelés webes csevegésben**lehetőséget. 
1. Az írja be az **üzenet** szövegmezőbe írja be a `Book a flight from Seattle to Berlin tomorrow`szöveget. A robot ellenőrzi, hogy szeretne-e repülőjáratot foglalni. 

    ![A Azure Portal képernyőképe a "Hello" szöveg beírásával.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    A teszt funkció használatával gyorsan tesztelheti a robotot. A teljes teszteléshez, beleértve a hibakeresést, töltse le a robot kódját, és használja a Visual studiót. 

## <a name="download-the-web-app-bot-source-code"></a>A webalkalmazás robot-forráskódjának letöltése
A webalkalmazás-robot kódjának fejlesztéséhez töltse le a kódot a helyi számítógépre. 

1. Az Azure Portalon a **Robot felügyelete** szakaszban kattintson a **Build** elemre. 

1. Válassza a **Robot forráskódjának letöltse** lehetőséget. 

    [![Webalkalmazás-robot forráskódjának letöltése alapszintű robothoz](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Ha az előugró ablak **a letöltött zip-fájlban is**megkéri az Alkalmazásbeállítások megadását, válassza az **Igen**lehetőséget. Ez biztosítja a LUIS beállításait. 

1. Tömörített forráskód esetén az üzenet tartalmazza a kód letöltéséhez szükséges hivatkozást. Kattintson a hivatkozásra. 

1. Mentse a .zip-fájlt a helyi számítógépére, és bontsa ki a fájlokat. Nyissa meg a projektet a Visual Studióval. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Tekintse át a kódot, hogy kinyerje a LUIS-t és a választ

1. A felhasználó teljes körű elküldéséhez a LUIS előrejelzési végponthoz nyissa meg a **párbeszédpanelek-> flightBookingRecognizer. js** fájlt. A rendszer ezen a ponton küldi a robotban megadott felhasználói kimondott szöveget a LUIS-hoz. A LUIS válaszát a **executeLuisQuery** metódus adja vissza.  

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

1. A **párbeszédpanelek – > mainDialog** rögzítik a kiválasztást, és a actStep metódusban küldi el a executeLuisQuery.


    ````javascript
    class MainDialog extends ComponentDialog {

        constructor(luisRecognizer, bookingDialog) {
            ...
            this.luisRecognizer = luisRecognizer;
            ...
        }


        ...

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {

            ...

            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);

            switch (LuisRecognizer.topIntent(luisResult)) {
                    case 'BookFlight':
                        // Extract the values for the composite entities from the LUIS result.
                        const fromEntities = this.luisRecognizer.getFromEntities(luisResult);
                        const toEntities = this.luisRecognizer.getToEntities(luisResult);
            
                        // Show a warning for Origin and Destination if we can't resolve them.
                        await this.showWarningForUnsupportedCities(stepContext.context, fromEntities, toEntities);
            
                        // Initialize BookingDetails with any entities we may have found in the response.
                        bookingDetails.destination = toEntities.airport;
                        bookingDetails.origin = fromEntities.airport;
                        bookingDetails.travelDate = this.luisRecognizer.getTravelDate(luisResult);
                        console.log('LUIS extracted these booking details:', JSON.stringify(bookingDetails));
            
                        // Run the BookingDialog passing in whatever details we have from the LUIS call, it will fill out the remainder.
                        return await stepContext.beginDialog('bookingDialog', bookingDetails);
            
                    case 'GetWeather':
                        // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                        const getWeatherMessageText = 'TODO: get weather flow here';
                        await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                        break;
            
                    default:
                        // Catch all for unhandled intents
                        const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                        await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    }
            
                    return await stepContext.next();

        }

        ...

    }
    ````
<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>A bot-emulátor használata a robot teszteléséhez

Kérdezze meg a robotot a könyv repülési szándékáról.

1. Indítsa el a robot-emulátort, és válassza a **robot megnyitása**lehetőséget.
1. A **bot megnyitása** előugró ablakban adja meg a robot URL-címét, például `http://localhost:3978/api/messages`:. Az `/api/messages` útvonal a robot webes címe.
1. Adja meg a **Microsoft-alkalmazás azonosítóját** és a **Microsoft-alkalmazás jelszavát**, amely a letöltött robot gyökerében található **. env** fájlban található.

1. A bot-emulátorban adja `Book a flight from Seattle to Berlin tomorrow` meg és kérje le ugyanazt a választ az alapszintű robotra, mint amit a **teszt webes csevegésben**kapott.

    [![Alapszintű bot-válasz az emulátorban](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Válassza az **Igen**lehetőséget. A robot a műveleteinek összegzésével válaszol. 
1. A robot-emulátor naplójából válassza ki a sort, amely tartalmazza `Luis Trace`a elemet. Ez megjeleníti a LUIS által a cél és a Kimondás entitásai számára küldött JSON-választ.

    [![Alapszintű bot-válasz az emulátorban](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>További lépések

További [mintákat](https://github.com/microsoft/botframework-solutions) tekinthet meg a társalgási robotokkal. 

> [!div class="nextstepaction"]
> [Language Understanding-alkalmazás létrehozása egyéni tárgyú tartománnyal](luis-quickstart-intents-only.md)