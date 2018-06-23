---
title: LUIS adatok hozzáadása a Node.js segítségével az Application Insights |} Microsoft Docs
titleSuffix: Azure
description: Hozzon létre egy LUIS alkalmazás és a Node.js segítségével az Application Insights szolgáltatással integrált bot.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 01/18/2018
ms.author: v-geberr
ms.openlocfilehash: 929b6e1cc980d7215f91a616820e257aed26bab7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349595"
---
# <a name="add-luis-results-to-application-insights-from-a-web-app-bot"></a>Az Application Insights webes alkalmazás bot származó LUIS eredmények hozzáadása
Ez az oktatóanyag hozzáadja a kérelem-válasz adatokat LUIS [Application Insights](https://azure.microsoft.com/services/application-insights/) telemetriai adatok tárolására. Miután az adatokat, kérdezhetők le azt a Kusto nyelv vagy a Power bi elemzése, összesíteni, és a jelentéskészítés leképezések és a valós idejű utterance entitásokat. A elemzés segítségével meghatározhatja, hogy ha kell hozzáadásakor vagy szerkesztésekor a leképezések és entitások LUIS alkalmazása.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
* Az Application Insights könyvtár hozzáadása a webes alkalmazás bot
* Rögzítése és a lekérdezési eredmények LUIS küldése az Application Insights részére
* Az Application Insights felső leképezés, pontszám és utterance lekérdezése

## <a name="prerequisites"></a>Előfeltételek

* A LUIS web app botot a a **[az oktatóanyag előző](luis-nodejs-tutorial-build-bot-framework-sample.md)** az Application Insights-e kapcsolva. 

> [!Tip]
> Ha még nem rendelkezik előfizetéssel, akkor a regisztrálhatja a [ingyenes fiókot](https://azure.microsoft.com/free/).

A jelen oktatóanyagban található kód mindegyikét érhető el a [LUIS-minták github-tárházban](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/nodejs) és az ebben az oktatóanyagban társított soronként megjegyzésként `//APPINSIGHT:`. 

## <a name="web-app-bot-with-luis"></a>Webes alkalmazás botot rendelkező LUIS
Ez az oktatóanyag feltételezi, hogy rendelkezik-e a kódot, hogy a következőképpen néz vagy, hogy végrehajtotta a [más oktatóanyag](luis-nodejs-tutorial-build-bot-framework-sample.md): 

   [!code-javascript[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/nodejs/app.js "Web app bot with LUIS")]

## <a name="add-application-insights-library-to-web-app-bot"></a>Az Application Insights könyvtár hozzáadása a webes alkalmazás botot
Az Application Insights szolgáltatás, a webes alkalmazás botot használatban jelenleg a botot az általános állapot telemetriai gyűjti. Nem gyűjt LUIS kérelem-válasz szükséges információkat ellenőrizze és javítsa ki a leképezések és entitások. 

A webes alkalmazás botot kell rögzítése a LUIS kérelem-válasz, a **[Application Insights](https://www.npmjs.com/package/applicationinsights)** NPM csomag telepítve és konfigurálva a **app.js** fájlt. Ezután a leképezési párbeszédpanel kezelők kell küldje el a LUIS kérés- és az Application Insights. 

1. Válassza ki az Azure portálon, a web app botot szolgáltatás **Build** alatt a **Botot felügyeleti** szakasz. 

    ![Az alkalmazás elemzések keresése](./media/luis-tutorial-appinsights/build.png)

2. Egy új böngészőlapon nyílik meg az App Service szerkesztő. A felső parancssávon válassza ki az alkalmazás nevére, majd válasszon **nyissa meg a Kudu konzolt**. 

    ![Az alkalmazás elemzések keresése](./media/luis-tutorial-appinsights/kudu-console.png)

3. A konzolon adja meg az Application insights szolgáltatással és az aláhúzás csomagok telepítéséhez a következő parancsot:

    ```
    cd site\wwwroot && npm install applicationinsights && npm install underscore
    ```

    ![Az alkalmazás elemzések keresése](./media/luis-tutorial-appinsights/npm-install.png)

    Várja meg a telepítendő csomagokat:

    ```
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

    A kudu konzol böngészőlapon befejezése.

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Rögzítése és a lekérdezési eredmények LUIS küldése az Application Insights részére
1. Az App Service-szerkesztő böngészőlapon, nyissa meg a **app.js** fájlt.

2. Adja hozzá a következő NPM könyvtárak alatt a meglévő `requires` sorok:

   [!code-javascript[Add NPM packages to app.js](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=12-16 "Add NPM packages to app.js")]

3. Az Application Insights-objektum létrehozása és használata a webes alkalmazás botot Alkalmazásbeállítás **BotDevInsightsKey**: 

   [!code-javascript[Create the Application Insights object](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=68-80 "Create the Application Insights object")]

4. Adja hozzá a **appInsightsLog** függvény:

   [!code-javascript[Add the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=82-109 "Add the appInsightsLog function")]

    A függvény utolsó sora, ahol az adatok bekerülnek az Application Insights részére. Az esemény neve **LUIS-eredmények**, a webes alkalmazás botot által gyűjtött mellett egyéb telemetriai adatokat egy egyedi nevet. 

5. Használja a **appInsightsLog** függvény. Minden leképezési párbeszédpanelen hozzáadhat:

   [!code-javascript[Use the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=117-118 "Use the appInsightsLog function")]

6. A webes alkalmazás botot teszteléséhez használja a **webes Csevegés tesztelése** szolgáltatás. Meg kell jelennie nincs különbség, mert a munkát az Application Insightsban, nem a botot válaszokat.

## <a name="view-luis-entries-in-application-insights"></a>Nézet LUIS bejegyzések az Application Insightsban
Nyissa meg az Application Insights LUIS tételek megtekintéséhez. 

1. Válassza a portál **összes erőforrás** majd szűrés a webalkalmazás botot neve. Kattintson az erőforrás-típusú **Application Insights**. Az Application Insights ikonja villanykörte. 

    ![Az alkalmazás elemzések keresése](./media/luis-tutorial-appinsights/search-for-app-insights.png)



2. Amikor megnyílik az erőforrás, kattintson a a **keresési** ikon a Nagyítóüveg a jobb panelen. Egy új panelen a jobb oldali jelennek meg. Attól függően, hogy mennyi telemetriai adatot talál, a panel is igénybe vehet egy második megjelenítéséhez. Keresse meg `LUIS-results` , és nyomja le a billentyűzet adja meg. A lista csak LUIS lekérdezési eredmények hozzá, amelyeknél ez az oktatóanyag van szűkíthető.

    ![A függőségek szűrése](./media/luis-tutorial-appinsights/app-insights-filter.png)

3. Válassza ki a legfelső bejegyzésre. Egy új ablakban a LUIS lekérdezés jobb szélen egyéni adatokat is beleértve részletes adatait jeleníti meg. A felső célt, és a pontszám szerepel.

    ![Függőség részletei](./media/luis-tutorial-appinsights/app-insights-detail.png)

    Amikor elkészült, válassza ki a jobb szélső felső **X** való visszatéréshez függőségi elemek listáját. 


> [!Tip]
> Ha azt szeretné, mentse a függőségi listáját és a későbbiekben, kattintson a **... További** kattintson **mentés kedvenc**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Az Application Insights leképezés, pontszám és utterance lekérdezése
Az Application Insights a kínálja, az adatok lekérdezése a [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) nyelve, valamint exportálási úgy, hogy [Power bi](https://powerbi.microsoft.com). 

1. Kattintson a **Analytics** tetején található a függőségi listázása, fent a Szűrő mezőbe. 

    ![Elemzés gomb](./media/luis-tutorial-appinsights/analytics-button.png)

2. Egy új ablakban nyílik meg a lekérdezési ablakban a bal felső és egy tábla ablak, amely alatt. Adatbázisok előtt használta, ezzel az elrendezéssel akkor ismeri. A lekérdezés tartalmazza az elmúlt 24 órában verziótól kezdve a név összes elemének `LUIS-results`. A **CustomDimensions** oszlopnak van a név/érték párként LUIS lekérdezés eredményeit.

    ![Analytics lekérdezési ablakba](./media/luis-tutorial-appinsights/analytics-query-window.png)

3. A felső leképezés pontszám és utterance kiemeléséhez adja hozzá a következőket az utolsó sorban fölött a lekérdezési ablakban:

    ```SQL
    | extend topIntent = tostring(customDimensions.LUIS_intent_intent)
    | extend score = todouble(customDimensions.LUIS_intent_score)
    | extend utterance = tostring(customDimensions.LUIS_text)
    ```

4. Futtassa a lekérdezést. Görgessen a képernyő jobb szélén az adatok táblázatban. Az új oszlopok topIntent, pontszám és utterance érhetők el. Kattintson a topIntent oszlopra kattintva rendezheti.

    ![Elemzés felső leképezés](./media/luis-tutorial-appinsights/app-insights-top-intent.png)


További információ a [Kusto lekérdezési nyelv](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-queries) vagy [exportálja az adatokat a Power bi szolgáltatásba](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>További lépések

Egyéb információk, érdemes lehet az application insights adatainak hozzáadása Alkalmazásazonosító, Verzióazonosító, utolsó modell módosítás dátuma, utolsó vonat dátum, az legutóbbi közzétételének dátuma. Ezek az értékek vagy is beolvassa, végpont URL-CÍMÉT (Alkalmazásazonosító és Verzióazonosító), vagy egy [API szerzői](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) hívható meg, majd állítsa be a webes alkalmazás botot az és ott lekért.  

Ha egynél több LUIS alkalmazás használ végpont ugyanazt az előfizetést, is tartalmaznia kell az előfizetés-azonosító és arról, hogy a megosztott kulcs tulajdonságot. 

> [!div class="nextstepaction"]
> [További tudnivalók példa utterances](luis-how-to-add-example-utterances.md)
