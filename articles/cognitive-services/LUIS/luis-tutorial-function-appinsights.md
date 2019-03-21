---
title: Az Application Insights Node.js
titleSuffix: Azure Cognitive Services
description: A robot a LUIS-alkalmazás és az Application Insights Node.js használatával integrálva hozhat létre.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 1c44d2e41d37a9236ee6d6936c349acf5ca5e44c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098764"
---
# <a name="add-luis-results-to-application-insights-and-azure-functions"></a>A LUIS-eredményeket ad hozzá az Application Insights és az Azure functions
Ebben az oktatóanyagban hozzáadja a kérések és válaszok adatokat LUIS [Application Insights](https://azure.microsoft.com/services/application-insights/) telemetriai adatok tárolására. Miután az adatokat, kérdezhet le róla a Kusto-nyelv, vagy elemezheti, összesítése, a Power bi és a jelentés a szándékok és entitások, valós idejű utterance (kifejezés). Az elemzés segít annak meghatározásában, ha kell hozzáadása vagy szerkesztése a szándékok és entitások, a LUIS-alkalmazás.

A robot épül fel a Bot keretrendszer 3.x és az Azure Web app bot.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Web app-robot Application Insights-könyvtár hozzáadása
> * Rögzíti és továbbítja a LUIS lekérdezés eredményeit az Application Insights
> * Az Application Insights lekérdezési felső szándékot, pontszám és utterance (kifejezés)

## <a name="prerequisites"></a>Előfeltételek

* A LUIS web app bot származó a **[előző oktatóanyagban](luis-nodejs-tutorial-build-bot-framework-sample.md)** az Application Insights-e kapcsolva. 

> [!Tip]
> Ha Ön még nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).

Ebben az oktatóanyagban kódja érhető el a [Azure-minták GitHub-adattár](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/nodejs) és ebben az oktatóanyagban társított minden egyes sor van ellátva a `//APPINSIGHT:`. 

## <a name="web-app-bot-with-luis"></a>Web app bot az intelligens hangfelismerési szolgáltatással
Ez az oktatóanyag feltételezi, hogy a kódot, hogy a következőképpen néz ki: a következő vagy az, hogy végrehajtotta a [többi oktatóanyag](luis-nodejs-tutorial-build-bot-framework-sample.md): 

   [!code-javascript[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/nodejs/app.js "Web app bot with LUIS")]

## <a name="add-application-insights-library-to-web-app-bot"></a>Web app bot Application Insights-könyvtár hozzáadása
Jelenleg az Application Insights szolgáltatás a web app bot használt gyűjt a robot telemetriája általános állapotát. A LUIS kérések és válaszok a keresett információt, ellenőrizze és javítsa ki a szándékok és entitások nem gyűjt. 

Annak érdekében, hogy a LUIS-kérések és válaszok rögzítése, a web app bot van szüksége a **[Application Insights](https://www.npmjs.com/package/applicationinsights)** NPM-csomag telepítve és konfigurálva a **app.js** fájlt. Ezután a szándék párbeszédpanel kezelők kell elküldeni a LUIS kérés- és az információkat az Application Insights. 

1. Web app bot service-ben az Azure Portalon válassza ki a **hozhat létre** alatt a **Bot felügyeleti** szakaszban. 

    ![Web app bot service-ben az Azure Portalon válassza a "Build" a "Bot kezelése" szakaszban.](./media/luis-tutorial-appinsights/build.png)

2. Egy új böngészőlapon nyílik meg, hogy az App Service Editor. Válassza ki az alkalmazás neve a felső sávon, majd válassza ki **Kudu konzol megnyitása**. 

    ![Válassza ki a felső sávon az alkalmazás nevét, majd válassza a "Megnyitás Kudu konzol".](./media/luis-tutorial-appinsights/kudu-console.png)

3. A konzolon adja meg az Application Insights és az aláhúzás csomagok telepítéséhez a következő parancsot:

    ```console
    cd site\wwwroot && npm install applicationinsights && npm install underscore
    ```

    ![Az Application Insights és az aláhúzás csomagok telepítése az npm parancsok használatával](./media/luis-tutorial-appinsights/npm-install.png)

    Várja meg a csomagok telepítése:

    ```console
    luisbot@1.0.0 D:\home\site\wwwroot
    `-- applicationinsights@1.0.1 
      +-- diagnostic-channel@0.2.0 
      +-- diagnostic-channel-publishers@0.2.1 
      `-- zone.js@0.7.6 
    
    npm WARN luisbot@1.0.0 No repository field.
    luisbot@1.0.0 D:\home\site\wwwroot
    +-- botbuilder-azure@3.0.4
    | `-- azure-storage@1.4.0
    |   `-- underscore@1.4.4 
    `-- underscore@1.8.3 
    ```

    A kudu konzol böngészőlapon végzett.

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Rögzíti és továbbítja a LUIS lekérdezés eredményeit az Application Insights
1. Az App Service Editor böngészőlapon nyissa meg a **app.js** fájlt.

2. Adja hozzá a következő NPM-kódtárak a meglévő alatt `requires` sorok:

   [!code-javascript[Add NPM packages to app.js](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=12-16 "Add NPM packages to app.js")]

3. Az Application Insights-objektum létrehozása és használata a web app bot Alkalmazásbeállítás **BotDevInsightsKey**: 

   [!code-javascript[Create the Application Insights object](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=68-80 "Create the Application Insights object")]

4. Adja hozzá a **appInsightsLog** függvény:

   [!code-javascript[Add the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=82-109 "Add the appInsightsLog function")]

    A függvény utolsó sora, ahol az adatok bekerülnek az Application Insightsba. Az esemény nevét kötelező **LUIS-eredmények**, a web app bot által gyűjtött egyéb telemetriai adatokat szereplőkkel egy egyedi nevet. 

5. Használja a **appInsightsLog** függvény. Minden szándék párbeszédpanel, adja hozzá:

   [!code-javascript[Use the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=117-118 "Use the appInsightsLog function")]

6. A web app bot teszteléséhez használja a **tesztelni, webes csevegési** funkció. Nincs különbség, mivel a munka az Application insights szolgáltatásban, nem szerepel a robot válaszokat kell megjelennie.

## <a name="view-luis-entries-in-application-insights"></a>Nézet LUIS bejegyzések az Application insights szolgáltatásban
Nyissa meg az Application Insights a LUIS-bejegyzések megtekintéséhez. 

1. Válassza a portál **összes erőforrás** majd szűrés a webalkalmazás robot neve. Kattintson az erőforráson típusú **Application Insights**. Az ikonra az Application Insights egy villanykörte. 

    ! [[Keresse meg az app insights az Azure Portalon](./media/luis-tutorial-appinsights/search-for-app-insights.png)

2. Az erőforrás megnyitása után kattintson a a **keresési** ikonjára a Nagyító ikonra a jobb szélen panelen. Egy új panel a jobb oldali jeleníti meg. Telemetriai adatok mennyiségétől függően található, a panel megjelenítéséhez egy második is igénybe vehet. Keresse meg `LUIS-results` , majd nyomja le a billentyűzet adja meg. A listában csak a LUIS lekérdezési eredmények ebben az oktatóanyagban hozzáadja van szűkíthető.

    ![Függőségek szűrése](./media/luis-tutorial-appinsights/app-insights-filter.png)

3. Válassza ki a legfelső bejegyzésre. Egy új ablakban jeleníti meg a részletes – jobb szélen a LUIS lekérdezés egyéni adatokat is beleértve. A felső célt, és a pontszám szerepel.

    ![Függőség részletei](./media/luis-tutorial-appinsights/app-insights-detail.png)

    Amikor elkészült, válassza a jobb szélső felső **X** térjen vissza a függőségi elemek listáját. 


> [!Tip]
> Ha azt szeretné, mentheti a függőségi listát, és később még visszatérünk rá, kattintson a **... További** kattintson **mentés kedvenc**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Lekérdezés az Application Insights szándékot, pontszám és utterance (kifejezés)
Az Application Insights lehetővé teszi az adatok lekérdezéséhez a [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) nyelven, valamint exportálási, hogy [Power bi](https://powerbi.microsoft.com). 

1. Kattintson a **Analytics** felső részén a függőség, listázása, fent a Szűrő mezőbe. 

    ![Elemzés gomb](./media/luis-tutorial-appinsights/analytics-button.png)

2. Egy lekérdezési ablak tetején, és a egy tábla ablak, amely alatt megjelenik egy új ablak. Ha a használt adatbázisokat korábban, ezzel az elrendezéssel fokozott tisztában van-e. A lekérdezés tartalmazza az elmúlt 24 órában nevét kezdetű elemeivel `LUIS-results`. A **CustomDimensions** oszlopnak a LUIS-lekérdezés eredménye név/érték párokként.

    ![Elemzés lekérdezési ablak](./media/luis-tutorial-appinsights/analytics-query-window.png)

3. Is, a felső szándékot, pontszám és utterance (kifejezés), adja hozzá a következő felett az utolsó sort a lekérdezési ablakban:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_intent_intent)
    | extend score = todouble(customDimensions.LUIS_intent_score)
    | extend utterance = tostring(customDimensions.LUIS_text)
    ```

4. Futtassa a lekérdezést. Görgessen az adatok tábla jobb szélén. Az új oszlopok topIntent, pontszám és utterance (kifejezés) érhetők el. Kattintson a topIntent oszlopra kattintva rendezheti a.

    ![Analytics felső leképezés](./media/luis-tutorial-appinsights/app-insights-top-intent.png)


Tudjon meg többet a [Kusto-lekérdezés nyelvi](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) vagy [az adatok exportálása Power bi-bA](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>További lépések

Egyéb információkat, érdemes hozzáadni az application insights-adatok tartalmazza Alkalmazásazonosító, a verzió azonosítója, a legutóbbi modell dátuma, a legutóbbi train dátuma, a legutóbbi közzététel dátuma. Ezek az értékek vagy olvashatók be a végpont URL-címe, (Alkalmazásazonosító és verzió azonosítója), vagy a egy [API szerzői](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) hívja, majd a web app bot beállítások, és onnan kéri le.  

Végpont ugyanahhoz az előfizetéshez egynél több LUIS alkalmazás használ, akkor is tartalmaznia kell az előfizetés-azonosító és a egy tulajdonság arról, hogy egy megosztott kulcsot. 

> [!div class="nextstepaction"]
> [További tudnivalók a példa kimondott szöveg](luis-how-to-add-example-utterances.md)
