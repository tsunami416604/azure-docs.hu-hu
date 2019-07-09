---
title: Az Application Insights Node.js
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban hozzáadja az Application Insights telemetriai adatokat tároló robot és a Language Understanding információkat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/16/2019
ms.author: diberry
ms.openlocfilehash: cfed5477df75350f24e77786117e85b9c728c49a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657762"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>LUIS eredményeket adhat az Application Insights Node.js-ben a robot a
Ez az oktatóanyag hozzáad robot és a Language Understanding információkat [Application Insights](https://azure.microsoft.com/services/application-insights/) telemetriai adatok tárolására. Miután az adatokat, kérdezhet le róla a Kusto-nyelv vagy a Power BI segítségével elemezheti, összesítése, és a jelentés a szándékok és entitások, valós idejű utterance (kifejezés). Az elemzés segít annak meghatározásában, ha kell hozzáadása vagy szerkesztése a szándékok és entitások, a LUIS-alkalmazás.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A robot és a Language understanding adatok az Application Insights rögzítése
> * Az Application Insights lekérdezni a Language Understanding data

## <a name="prerequisites"></a>Előfeltételek

* Az Azure bot service robot, engedélyezve van az Application Insights használatával létrehozott.
* Bot kód az előző robot-től letöltött  **[oktatóanyag](luis-nodejs-tutorial-bf-v4.md)** . 
* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Ebben az oktatóanyagban kódja megtalálható a [Azure-minták Language Understanding GitHub-adattár](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Az Application Insights hozzáadása a web app bot projekthez
Jelenleg az Application Insights szolgáltatás a web app bot használt gyűjt a robot telemetriája általános állapotát. A LUIS-adatokat nem gyűjt. 

Annak érdekében, hogy rögzítheti a LUIS-információkat, a web app bot van szüksége a **[Application Insights](https://www.npmjs.com/package/applicationinsights)** NPM-csomag telepítését és konfigurálását.  

1. A VSCode integrált terminálon a bot projekt gyökerében adja hozzá a következő NPM csomagok, a parancs látható: 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    A **aláhúzás** csomag simítja egybe a LUIS JSON-struktúrát, így könnyebben megtekintéséhez és használatához az Application Insights segítségével.
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Rögzíti és továbbítja a LUIS lekérdezés eredményeit az Application Insights

1. A vscode-ban, hozzon létre egy új fájlt **appInsightsLog.js** , és adja hozzá a következő kódot:

    ```javascript
    const appInsights = require('applicationinsights');
    const _ = require("underscore");
    
    // Log LUIS results to Application Insights
    // must flatten as name/value pairs
    var appInsightsLog = (botContext,luisResponse) => {

        appInsights.setup(process.env.MicrosoftApplicationInsightsInstrumentationKey).start();
        const appInsightsClient = appInsights.defaultClient;

        // put bot context and LUIS results into single object
        var data = Object.assign({}, {'botContext': botContext._activity}, {'luisResponse': luisResponse});
    
        // Flatten data into name/value pairs
        flatten = (x, result, prefix) => {
            if(_.isObject(x)) {
                _.each(x, (v, k) => {
                    flatten(v, result, prefix ? prefix + '_' + k : k)
                })
            } else {
                result["LUIS_" + prefix] = x
            }
            return result;
        }
    
        // call fn to flatten data
        var flattenedData = flatten(data, {});
    
        // ApplicationInsights Trace 
        console.log(JSON.stringify(flattenedData));
    
        // send data to Application Insights
        appInsightsClient.trackTrace({message: "LUIS", severity: appInsights.Contracts.SeverityLevel.Information, properties: flattenedData});
    }
    
    module.exports.appInsightsLog = appInsightsLog;
    ```

    Ez a fájl vesz igénybe a robot, környezeti és a luis-válasz, simítja egybe mindkét objektum és beilleszti őket egy **nyomkövetési** eseményt az application insightsban. Az esemény nevét kötelező **LUIS**. 

1. Nyissa meg a **párbeszédpanelek** mappát, majd a **luisHelper.js** fájlt. Tartalmazza az új **appInsightsLog.js** egy szükséges fájl, és rögzítheti a robot környezet és a LUIS választ. Ez a fájl teljes kódja a következő: 

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    const { appInsightsLog } = require('../appInsightsLog');
    
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
    
                // APPINSIGHT: Log results to Application Insights
                appInsightsLog(context,recognizerResult);
    
    
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

## <a name="add-application-insights-instrumentation-key"></a>Az Application Insights-eszközkulcs felvétele 

Adatok hozzáadása az application insights, a kialakítási kulcsot kell.

1. Egy böngészőben a a [az Azure portal](https://portal.azure.com), keresse meg a robot **Application Insights** erőforrás. A név lesz a legtöbb a robot neve, majd a véletlenszerű karaktert a név végén `luis-nodejs-bot-johnsmithxqowom`. 
1. Az Application Insights-erőforrás a a **áttekintése** lapon, másolja a **kialakítási kulcs**.
1. A vscode-ban, nyissa meg a **.env** fájlt robot projekt gyökérmappájában. Ebben a fájlban található összes környezeti változót.  
1. Adjon hozzá egy új változó `MicrosoftApplicationInsightsInstrumentationKey` a kialakítási kulcs értékét. Ne nincs put idézőjelek között szereplő érték. 

## <a name="start-the-bot"></a>A robot indítása

1. Az integrált terminálon VSCode indítsa el a robotot:
    
    ```console
    npm start
    ```

1. A robot-emulátor elindítása, és nyissa meg a robot. Ez [lépés](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) az előző oktatóanyagban található.

1. A robot tegyen fel kérdést. Ez [lépés](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) az előző oktatóanyagban található.

## <a name="view-luis-entries-in-application-insights"></a>Nézet LUIS bejegyzések az Application insights szolgáltatásban

Nyissa meg az Application Insights a LUIS-bejegyzések megtekintéséhez. Az adatai megjelennek az Application Insights pár percet is igénybe vehet.

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a robot Application Insights-erőforrást. 
1. Az erőforrás megnyitása után válassza ki a **keresési** , és keresse meg az összes adat az utolsó **30 perc** az esemény típusú **nyomkövetési**. Válassza ki a nyomkövetés nevű **LUIS**. 
1. A robot és a LUIS információ érhető el **egyéni tulajdonságok**. 

    ![Tekintse át az Application insights szolgáltatásban tárolt LUIS egyéni tulajdonságok](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Lekérdezés az Application Insights szándékot, pontszám és utterance (kifejezés)
Az Application Insights lehetővé teszi az adatok lekérdezéséhez a [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) nyelven, valamint exportálási, hogy [Power BI](https://powerbi.microsoft.com). 

1. Válassza ki **Log (Analytics)** . Egy lekérdezési ablak tetején, és a egy tábla ablak, amely alatt megjelenik egy új ablak. Ha a használt adatbázisokat korábban, ezzel az elrendezéssel fokozott tisztában van-e. A lekérdezés az előző szűrt adatokat jelöli. A **CustomDimensions** oszlopnak a robot és a LUIS-információkat.
1. Is, a felső szándékot, pontszám és utterance (kifejezés), adja hozzá a következő csak az utolsó sort fent (a `|top...` sor) a lekérdezési ablakban:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. Futtassa a lekérdezést. Az új oszlopok topIntent, pontszám és utterance (kifejezés) érhetők el. Válassza ki a topIntent oszlopra kattintva rendezheti.

Tudjon meg többet a [Kusto-lekérdezés nyelvi](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) vagy [exportálja az adatokat a Power bi-bA](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>További lépések

Egyéb információkat, érdemes hozzáadni az application insights-adatok tartalmazza Alkalmazásazonosító, a verzió azonosítója, a legutóbbi modell dátuma, a legutóbbi train dátuma, a legutóbbi közzététel dátuma. Ezeket az értékeket is vagy lekérje a végponti URL-cím (az alkalmazás és verzió-azonosító), vagy egy jelentéskészítő API-hívás, majd a web app bot beállítások és onnan kéri le.  

Végpont ugyanahhoz az előfizetéshez egynél több LUIS alkalmazás használ, akkor is tartalmaznia kell az előfizetés-azonosító és a egy tulajdonság arról, hogy egy megosztott kulcsot. 

> [!div class="nextstepaction"]
> [További tudnivalók a példa kimondott szöveg](luis-how-to-add-example-utterances.md)
