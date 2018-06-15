---
title: LUIS adatok hozzáadása a Application Insights segítségével C# |} Microsoft Docs
titleSuffix: Azure
description: Integrálva van egy LUIS alkalmazás és az Application Insights segítségével C# bot felépítéséhez.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/07/2018
ms.author: v-geberr
ms.openlocfilehash: 52b6ae224b0e8da12eb4903f5100a6e5cc39704d
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "35349952"
---
# <a name="add-luis-results-to-application-insights-from-a-web-app-bot"></a>Az Application Insights webes alkalmazás bot származó LUIS eredmények hozzáadása
Ez az oktatóanyag LUIS válasz információkat ad [Application Insights](https://azure.microsoft.com/services/application-insights/) telemetriai adatok tárolására. Miután az adatokat, kérdezhetők le azt a Kusto nyelv vagy a Power bi elemzése, összesíteni, és a jelentéskészítés leképezések és a valós idejű utterance entitásokat. A elemzés segítségével meghatározhatja, hogy ha kell hozzáadásakor vagy szerkesztésekor a leképezések és entitások LUIS alkalmazása.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
* Az Application Insights hozzáadása a webes alkalmazás bot
* Rögzítése és a lekérdezési eredmények LUIS küldése az Application Insights részére
* Az Application Insights felső leképezés, pontszám és utterance lekérdezése

## <a name="prerequisites"></a>Előfeltételek

* A LUIS web app botot a a **[az oktatóanyag előző](luis-csharp-tutorial-build-bot-framework-sample.md)** az Application Insights-e kapcsolva. 
* [A Visual Studio 2017](https://www.visualstudio.com/downloads/) helyben telepítve a számítógépre.

> [!Tip]
> Ha még nem rendelkezik előfizetéssel, akkor a regisztrálhatja a [ingyenes fiókot](https://azure.microsoft.com/free/).

A jelen oktatóanyagban található kód mindegyikét érhető el a [LUIS-minták github-tárházban](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/csharp) és az ebben az oktatóanyagban társított soronként megjegyzésként `//LUIS Tutorial:`. 

## <a name="review-luis-web-app-bot"></a>Tekintse át a LUIS web app botot
Ez az oktatóanyag feltételezi, hogy rendelkezik-e a kódot, hogy a következőképpen néz vagy, hogy végrehajtotta a [más oktatóanyag](luis-csharp-tutorial-build-bot-framework-sample.md): 

   [!code-csharp[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs "Web app bot with LUIS")]

## <a name="application-insights-in-web-app-bot"></a>Az Application Insights a webes alkalmazás botot
Az Application Insights szolgáltatás hozzáadni a webalkalmazás botot szolgáltatás létrehozása, a jelenleg a botot az általános állapot telemetriai gyűjti. LUIS válasz adatokat nem gyűjt. Ahhoz, hogy elemzése és LUIS javítása, információra van szüksége a LUIS válasz.  

A webes alkalmazás botot kell rögzítheti a LUIS választ, **[Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** telepítette és konfigurálta a projekthez. 

## <a name="download-web-app-bot"></a>Töltse le a webes alkalmazás botot
Használjon [Visual Studio 2017](https://www.visualstudio.com/downloads/) hozzáadása és a webes alkalmazás botot az Application Insights beállítása. Ahhoz, hogy a webes alkalmazás botot a Visual Studio, a webes alkalmazás botot kód letöltése.

1. Válassza ki az Azure-portálon, a webes alkalmazás botot **Build**.

    ![Build kiválasztása a portálon](./media/luis-tutorial-bot-csharp-appinsights/download-build-menu.png)

2. Válassza ki **zip-fájl letöltési** és várjon, amíg készen áll a testreszabásra a fájlt.

    ![Zip-fájl letöltése](./media/luis-tutorial-bot-csharp-appinsights/download-link.png)

3. Válassza ki **zip-fájl letöltési** az előugró ablakban. Emlékszik a helyre, a számítógépen, a következő szakaszban szüksége lesz rá.

    ![Töltse le a zip-fájl menü](./media/luis-tutorial-bot-csharp-appinsights/download-popup.png)

## <a name="open-solution-in-visual-studio-2017"></a>Nyissa meg a megoldást a Visual Studio 2017

1. Bontsa ki a fájlt egy mappába. 

2. Nyissa meg a Visual Studio 2017, és nyissa meg a megoldásfájlt `Microsoft.Bot.Sample.LuisBot.sln`. Ha a biztonsági figyelmeztetés jelenik meg, válassza az "OK gombra".

    ![Nyissa meg a megoldást a Visual Studio 2017](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-security-warning.png)

3. A Visual Studio kell adja a függőségeket a megoldáshoz. Az a **Megoldáskezelőben**, kattintson a jobb gombbal a **hivatkozások**, és válassza ki **NuGet-csomagok kezelése...** . 

    ![NuGet-csomagok kezelése](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-manage-nuget-packages.png)

4. A NuGet Package manager telepített csomagok listáját tartalmazza. Válassza ki **visszaállítása** a sárga sávon. Várjon, amíg a visszaállítási folyamat befejezéséhez.

    ![NuGet-csomagok visszaállítása](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-restore-packages.png)

## <a name="add-application-insights-to-the-project"></a>Az Application Insights hozzáadása a projekthez
Telepítse és konfigurálja az Application Insights a Visual Studióban. 

1. A Visual Studio 2017, a felső menüben válassza **projekt**, majd jelölje be **Application Insights Telemetria...** .

2. Az a **Application Insights beállításait** ablakban válassza ki **szabad indítása**

    ![Indítsa el az Application Insights konfigurálása](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configure-app-insights.png)

3. Regisztrálja az alkalmazást az Application insights szolgáltatással. Előfordulhat, hogy az Azure portál hitelesítő adatait. 

4. A Visual Studio hozzáadja az Application Insights a projekt állapot megjelenítése, ez azonban nem. 

    ![Application Insights állapota](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-adding-application-insights-to-project.png)

    A folyamat befejezése után a **Application Insights beállításait** végrehajtási állapotát jeleníti meg. 

    ![Application Insights folyamat állapota](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configured-application-insights-to-project.png)

    A **nyomkövetési gyűjtésének engedélyezése** piros, ami azt jelenti, engedélyezve van. Ez az oktatóanyag nem használja a szolgáltatást. 

## <a name="build-and-resolve-errors"></a>Hozza létre, és hárítsa el a hibákat

1. A megoldás felépítéséhez kiválasztásával a **Build** menüben, majd válassza ki **Rebuild Solution**. Várjon, amíg befejeződik a build. 

2. Ha a létrehozás sikertelen, és `CS0104` hibák, javítani kell őket. Az a `Controllers` mappa, a a `MessagesController.cs file`, hárítsa el a nem egyértelmű használatát `Activity` az összekötő típusú tevékenységtípus illesztésével típusa. Ehhez az szükséges, módosítsa a nevét `Activity` sorok 22-es és a 36 `Activity` való `Connector.Activity`. A megoldás felépítéséhez újra. Kell több összeállítási hiba.

    A fájl teljes forrása:

    [!code-csharp[MessagesController.cs file](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/MessagesController.cs "MessagesController.cs file")]

## <a name="publish-project-to-azure"></a>Projekt közzététele az Azure-bA
A **Application Insights** csomag most már a projektben, és megfelelően konfigurálva a hitelesítő adatokat az Azure portálon. A módosítások a projekt kell közzétenni vissza az Azure-bA.

1. Az a **Megoldáskezelőben**, kattintson a jobb gombbal a projekt nevére, majd válassza ki **közzététel**.

    ![Projekt közzététele a portálra](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish.png)

2. Az a **közzététel** ablakban válassza ki **hozzon létre új profilt**.

    ![Projekt közzététele a portálra](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-1.png)

3. Válassza ki **profil importálása**, és válassza ki **OK**.

    ![Projekt közzététele a portálra](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-2.png)

4. Az a **közzététele beállításfájl importálása** windows, keresse meg a projekt mappát, keresse meg a `PostDeployScripts` mappát, válassza ki a fájlt, amely `.PublishSettings`, és válassza ki `Open`. Ezzel beállította a közzététel ebben a projektben. 

5. A helyi forráskód közzétételére Botot szolgáltatás kiválasztásával a **közzététel** gombra. A **kimeneti** ablak állapotát jeleníti meg. Az oktatóanyag a többi Azure-portálon befejeződött. Zárja be a Visual Studio 2017. 

## <a name="open-three-browser-tabs"></a>Nyissa meg a három böngészőlapokon
Az Azure portálon keresse meg a webes alkalmazás botot, és nyissa meg. Az alábbi lépéseket a webes alkalmazás botot három különböző nézeteit használja. Elképzelhető, hogy könnyebben három különálló lap megnyitása a böngészőben van: 
  
>  * A webes Csevegés tesztelése
>  * Build/megnyitása online kód szerkesztése -> az App Service-szerkesztő
>  * App Service szerkesztő/nyissa meg a Kudu konzol -> diagnosztikai konzol

## <a name="modify-basicluisdialogcs-code"></a>BasicLuisDialog.cs kód módosítása

1. Az a **App Service-szerkesztő** böngészőlapon, nyissa meg a `BasicLuisDialog.cs` fájlt.

2. Adja hozzá a következő NuGet-függőség alatt a meglévő `using` sorok:

   [!code-csharp[Add using statement](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=11-12 "Add using statement")]

3. Adja hozzá a `LogToApplicationInsights` függvény:

   [!code-csharp[Add the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=61-92 "Add the LogToApplicationInsights function")]

    Az Application Insights instrumentation kulcs már szerepel a webes alkalmazás botot alkalmazás nevű beállításával `BotDevInsightsKey`. 

    A függvény utolsó sora hozzáadása az Application Insights az adatokat. A nyomkövetés neve `LUIS`, a webes alkalmazás botot által gyűjtött mellett egyéb telemetriai adatokat egy egyedi nevet. A tulajdonságok is előtagként `LUIS_` hívjuk fel adatokkal az oktatóanyag ad hozzá képest a webes alkalmazás botot által megadott adatok.

4. Hívja a `LogToApplicationInsights` függvény tetején a `ShowLuisResult` függvény:

   [!code-csharp[Use the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=114-115 "Use the LogToApplicationInsights function")]

## <a name="build-web-app-bot"></a>Webes alkalmazás botot összeállítása
1. A webes alkalmazás botot az alábbi két módszer egyikével felépítéséhez. Az első módszer a következő: kattintson a jobb gombbal a `build.cmd` a a **App Service-szerkesztő**, majd jelölje be **futtatása konzolról**. A konzol kimeneti jeleníti meg, és a befejezése `Finished successfully.`

2. Ha ez nem fejeződik be sikeresen, nyissa meg a konzolt, keresse meg a parancsfájl, és futtassa az alábbi lépéseket követve szüksége. Az a **App Service-szerkesztő**, a felső kék sávon, válassza ki a botot nevét, majd válassza ki **nyissa meg a Kudu konzolt** a legördülő listában.

    ![Nyissa meg a Kudu konzol](./media/luis-tutorial-bot-csharp-appinsights/open-kudu-console.png)

3. A konzolablakban adja meg a következő parancsot:

    ```
    cd site\wwwroot && build.cmd
    ```

    Várjon, amíg befejeződik a build `Finished successfully.`

## <a name="test-the-web-app-bot"></a>A webes alkalmazás botot tesztelése

1. A webes alkalmazás botot ellenőrzéséhez nyissa meg a **webes Csevegés tesztelése** szolgáltatás a portálon. 

2. Adja meg a kifejezés `Coffee bar on please`.  

    ![A csevegési web app botot tesztelése](./media/luis-tutorial-bot-csharp-appinsights/test-in-web-chat.png)

3. Meg kell jelennie a chatbot válasz nincs különbség. A módosítás küld adatokat az Application Insights részére, nem található a botot válaszokat. Adjon meg néhány további utterances, így az Application Insights egy kis több adatot:

```
Please deliver a pizza
Turn off all the lights
Turn on the hall light
```

## <a name="view-luis-entries-in-application-insights"></a>Nézet LUIS bejegyzések az Application Insightsban
Nyissa meg az Application Insights LUIS tételek megtekintéséhez. 

1. Válassza a portál **összes erőforrás** majd szűrés a webalkalmazás botot neve. Kattintson az erőforrás-típusú **Application Insights**. Az Application Insights ikonja villanykörte. 

    ![Az alkalmazás elemzések keresése](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights.png)

2. Amikor megnyílik az erőforrás, kattintson a a **keresési** ikon a Nagyítóüveg a jobb panelen. Egy új panelen a jobb oldali jelennek meg. Attól függően, hogy mennyi telemetriai adatot talál, a panel is igénybe vehet egy második megjelenítéséhez. Keresse meg `LUIS`. A lista csak LUIS lekérdezési eredmények hozzá, amelyeknél ez az oktatóanyag van szűkíthető.

    ![Keresse meg a nyomkövetési adatokat](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace.png)

3. Válassza ki a legfelső bejegyzésre. Egy új ablakban a LUIS lekérdezés jobb szélen egyéni adatokat is beleértve részletes adatait jeleníti meg. A felső célt, és a pontszám szerepel.

    ![Tekintse át a nyomkövetési elem](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace-item.png)

    Amikor elkészült, válassza ki a jobb szélső felső **X** való visszatéréshez függőségi elemek listáját. 


> [!Tip]
> Ha azt szeretné, mentse a függőségi listáját és a későbbiekben, kattintson a **... További** kattintson **mentés kedvenc**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Az Application Insights leképezés, pontszám és utterance lekérdezése
Az Application Insights a kínálja, az adatok lekérdezése a [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) nyelve, valamint exportálási úgy, hogy [Power bi](https://powerbi.microsoft.com). 

1. Kattintson a **Analytics** tetején található a függőségi listázása, fent a Szűrő mezőbe. 

    ![Elemzés gomb](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-analytics-button.png)

2. Egy új ablakban nyílik meg a lekérdezési ablakban a bal felső és egy tábla ablak, amely alatt. Adatbázisok előtt használta, ezzel az elrendezéssel akkor ismeri. A lekérdezés tartalmazza az elmúlt 24 órában verziótól kezdve a név összes elemének `LUIS`. A **CustomDimensions** oszlopnak van a név/érték párként LUIS lekérdezés eredményeit.

    ![Alapértelmezett analytics jelentés](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-1.png)

3. A felső leképezés pontszám és utterance kiemeléséhez adja hozzá a következőket az utolsó sorban fölött a lekérdezési ablakban:

    ```SQL
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

4. Futtassa a lekérdezést. Görgessen a képernyő jobb szélén az adatok táblázatban. Az új oszlopok topIntent, pontszám és utterance érhetők el. Kattintson a topIntent oszlopra kattintva rendezheti.

    ![Egyéni analytics jelentés](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-2.png)


További információ a [Kusto lekérdezési nyelv](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-queries) vagy [exportálja az adatokat a Power bi szolgáltatásba](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>További tudnivalók Botot keretrendszer
További információ [Botot keretrendszer](https://dev.botframework.com/).

## <a name="next-steps"></a>További lépések

Egyéb információk, érdemes lehet az application insights adatainak hozzáadása Alkalmazásazonosító, Verzióazonosító, utolsó modell módosítás dátuma, utolsó vonat dátum, az legutóbbi közzétételének dátuma. Ezek az értékek vagy is beolvassa, végpont URL-CÍMÉT (Alkalmazásazonosító és Verzióazonosító), vagy egy [API szerzői](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) hívható meg, majd állítsa be a webes alkalmazás botot az és ott lekért.  

Ha egynél több LUIS alkalmazás használ végpont ugyanazt az előfizetést, is tartalmaznia kell az előfizetés-azonosító és arról, hogy a megosztott kulcs tulajdonságot. 

> [!div class="nextstepaction"]
> [További tudnivalók példa utterances](luis-how-to-add-example-utterances.md)
