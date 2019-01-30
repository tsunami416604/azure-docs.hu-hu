---
title: Az Application Insights használatávalC#
titleSuffix: Azure Cognitive Services
description: A robot a LUIS alkalmazás és a C# használatával az Application Insights szolgáltatással integrált hozhat létre.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: dce0a34ea9d195748c47dd30cdd5f591ba597c2f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228151"
---
# <a name="add-luis-results-to-application-insights-with-a-bot-in-c"></a>A LUIS-eredményeket ad hozzá egy robottal az Application InsightsC#

Ez az oktatóanyag hozzáad a LUIS válasz információkat [Application Insights](https://azure.microsoft.com/services/application-insights/) telemetriai adatok tárolására. Miután az adatokat, kérdezhet le róla a Kusto-nyelv, vagy elemezheti, összesítése, a Power bi és a jelentés a szándékok és entitások, valós idejű utterance (kifejezés). Az elemzés segít annak meghatározásában, ha kell hozzáadása vagy szerkesztése a szándékok és entitások, a LUIS-alkalmazás.

A robot épül fel a Bot keretrendszer 3.x és az Azure Web app bot.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Application Insights web app-robot hozzáadása
> * Rögzíti és továbbítja a LUIS lekérdezés eredményeit az Application Insights
> * Az Application Insights lekérdezési felső szándékot, pontszám és utterance (kifejezés)

## <a name="prerequisites"></a>Előfeltételek

* A LUIS web app bot származó a **[előző oktatóanyagban](luis-csharp-tutorial-build-bot-framework-sample.md)** az Application Insights-e kapcsolva.
* [A Visual Studio 2017](https://www.visualstudio.com/downloads/) helyben telepítve a számítógépen.

> [!Tip]
> Ha Ön még nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).

Ebben az oktatóanyagban kódja érhető el a [Azure-minták GitHub-adattár](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/csharp) és ebben az oktatóanyagban társított minden egyes sor van ellátva a `//LUIS Tutorial:`.

## <a name="review-luis-web-app-bot"></a>Tekintse át a LUIS web app bot

Ez az oktatóanyag feltételezi, hogy a kódot, hogy a következőképpen néz ki: a következő vagy az, hogy végrehajtotta a [többi oktatóanyag](luis-csharp-tutorial-build-bot-framework-sample.md):

   [!code-csharp[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs "Web app bot with LUIS")]

## <a name="application-insights-in-web-app-bot"></a>Application Insights a web app bot

Jelenleg az Application Insights szolgáltatás, hozzáadni a web app bot service létrehozását, gyűjt a robot telemetriája általános állapota. A LUIS válasz adatokat nem gyűjt. Méréséhez és növeléséhez, LUIS, LUIS válasz információk kell.  

Annak érdekében, hogy rögzíti a LUIS-válasz, a web app bot kell **[Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** telepítette és konfigurálta a projekthez.

## <a name="download-web-app-bot"></a>Töltse le a web app bot

Használat [Visual Studio 2017](https://www.visualstudio.com/downloads/) hozzáadása és konfigurálása az Application Insights a web app-robot. A web app bot használatához a Visual Studióban, töltse le a web app bot kódot.

1. A web app-robot esetén az Azure Portalon válassza ki a **összeállítása**.

    ![Build kiválasztása a portálon](./media/luis-tutorial-bot-csharp-appinsights/download-build-menu.png)

2. Válassza ki **zip-fájl letöltési** és várjon, amíg a fájl elkészül.

    ![Zip-fájl letöltése](./media/luis-tutorial-bot-csharp-appinsights/download-link.png)

3. Válassza ki **zip-fájl letöltési** a felugró ablakban. Emlékszik a helyre a számítógépen, a következő szakaszban szükség lesz rá.

    ![Töltse le a zip-fájl menü](./media/luis-tutorial-bot-csharp-appinsights/download-popup.png)

## <a name="open-solution-in-visual-studio-2017"></a>Nyissa meg a megoldást a Visual Studio 2017

1. Bontsa ki a fájlt egy mappába.

2. Nyissa meg a Visual Studio 2017-et, és nyissa meg a megoldásfájlt `Microsoft.Bot.Sample.LuisBot.sln`. Ha a biztonsági figyelmeztetést kap, miszerint, válassza az "OK gombra".

    ![Nyissa meg a megoldást a Visual Studio 2017](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-security-warning.png)

3. A Visual Studio adja a függőségeket a megoldáshoz szükséges. Az a **Megoldáskezelőben**, kattintson a jobb gombbal a **hivatkozások**, és válassza ki **NuGet-csomagok kezelése...** .

    ![NuGet-csomagok kezelése](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-manage-nuget-packages.png)

4. A NuGet Package manager egy telepített csomagok listáját jeleníti meg. Válassza ki **visszaállítása** a sárga sáv. Várjon, amíg a visszaállítási folyamat befejezéséhez.

    ![NuGet-csomagok visszaállítása](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-restore-packages.png)

## <a name="add-application-insights-to-the-project"></a>Az Application Insights hozzáadása a projekthez

Telepítse és konfigurálja az Application Insights a Visual Studióban.

1. A Visual Studio 2017, a felső menüben válassza **projekt**, majd **Application Insights Telemetria hozzáadása...** .

2. Az a **az Application Insights konfigurációjának** ablakban válassza **ingyenes próba megkezdése**

    ![Indítsa el az Application Insights konfigurálása](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configure-app-insights.png)

3. Regisztrálja az alkalmazást az Application insights segítségével. Előfordulhat, hogy az Azure portal hitelesítő adatait.

4. A Visual Studio hozzáadja az Application Insights a projekthez, mint ez az állapot megjelenítése.

    ![Application Insights állapota](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-adding-application-insights-to-project.png)

    A folyamat befejeződése után a **az Application Insights konfigurációjának** végrehajtási állapotát jeleníti meg.

    ![Application Insights folyamat állapota](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configured-application-insights-to-project.png)

    A **gyűjthet nyomkövetési** piros, ami azt jelenti, nincs engedélyezve. Ebben az oktatóanyagban a funkció nem használható.

## <a name="build-and-resolve-errors"></a>Hozhat létre, és ki a hibákat

1. A megoldás felépítéséhez kiválasztásával a **hozhat létre** menüben, majd válassza ki **Rebuild Solution**. Várjon, amíg befejeződik a build.

2. Ha a létrehozás sikertelen, és `CS0104` hibák kijavításához szükséges őket. Az a `Controllers` mappába, a a `MessagesController.cs file`, javítsa ki a nem egyértelmű használatát `Activity` írja be a tevékenység típusát az összekötő típusú előtaggal. Ehhez módosítsa a nevet `Activity` sorok 22-es és a 36 `Activity` való `Connector.Activity`. Hozza létre újra a megoldást. Nincs több fordítási hibákat kell lennie.

    A teljes fájl forrása:

    [!code-csharp[MessagesController.cs file](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/MessagesController.cs "MessagesController.cs file")]

## <a name="publish-project-to-azure"></a>Projekt közzététele az Azure-bA

A **Application Insights** csomagot a projektben már van, és megfelelően konfigurálva a hitelesítő adatokat az Azure Portalon. A módosítások a projekt kell közzé tenni az Azure-bA.

1. Az a **Megoldáskezelőben**, kattintson a jobb gombbal a projekt nevére, majd válassza ki **közzététel**.

    ![Projekt közzététele a portálra](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish.png)

2. Az a **közzététel** ablakban válassza **új profil létrehozásához**.

    ![Közzététel részeként az új profil létrehozásához.](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-1.png)

3. Válassza ki **profil importálása**, és válassza ki **OK**.

    ![Közzététel részeként a profil importálása](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-2.png)

4. Az a **közzététele beállításfájl importálása** windows, lépjen a projektmappába, keresse meg a `PostDeployScripts` mappát, válassza ki a fájlt, amely `.PublishSettings`, és válassza ki `Open`. Most már konfigurálta a projekt közzététele.

5. A helyi forráskód közzétételére a Bot Service kiválasztásával a **közzététel** gombra. A **kimeneti** ablak állapotát jeleníti meg. Az oktatóanyag további részeinek befejezése az Azure Portalon. Zárja be a Visual Studio 2017-ben.

## <a name="open-three-browser-tabs"></a>Nyissa meg böngészőben három lap

Az Azure Portalon keresse meg a web app bot, és nyissa meg. Az alábbi lépéseket használja a web app bot három különböző nézetét. Elképzelhető, hogy könnyebben három külön lap van megnyitva a böngészőben:
  
>  * A webes csevegési tesztelése
>  * Nyissa meg a build/online Kódszerkesztő -> az App Service Editor
>  * App Service Editor nyissa meg/Kudu konzol -> diagnosztikai konzol

## <a name="modify-basicluisdialogcs-code"></a>BasicLuisDialog.cs kód módosítása

1. Az a **az App Service Editor** böngészőlapon, nyissa meg a `BasicLuisDialog.cs` fájlt.

2. Adja hozzá a következő NuGet-függőséget alatt a meglévő `using` sorok:

   [!code-csharp[Add using statement](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=11-12 "Add using statement")]

3. Adja hozzá a `LogToApplicationInsights` függvény:

   [!code-csharp[Add the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=61-92 "Add the LogToApplicationInsights function")]

    Az Application Insights-kialakítási kulcs már szerepel a web app bot alkalmazás nevű beállításhoz `BotDevInsightsKey`.

    A függvény utolsó sora az adatokat ad hozzá az Application Insights. A nyomkövetés neve `LUIS`, a web app bot által gyűjtött egyéb telemetriai adatokat szereplőkkel egy egyedi nevet. Az összes tulajdonság is van fűzve előtagként `LUIS_` így láthatja, hogy milyen adatokat ebben az oktatóanyagban hozzáadja képest a web app-robot által megadott adatok.

4. Hívja a `LogToApplicationInsights` függvény felső részén a `ShowLuisResult` függvény:

   [!code-csharp[Use the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=114-115 "Use the LogToApplicationInsights function")]

## <a name="build-web-app-bot"></a>Web app bot létrehozása

1. A web app bot a két módszer egyikével hozhat létre. Az első módszer, hogy a jobb gombbal a `build.cmd` a a **az App Service Editor**, majd **futtatása konzolról**. A konzol kimenetét jeleníti meg, és elvégzi az `Finished successfully.`

2. Ha ez nem fejeződik be sikeresen, nyissa meg a konzolt, keresse meg a parancsfájl, és futtassa az alábbi lépéseket követve szüksége. Az a **az App Service Editor**, válassza a felső kék sávban a robot neve, majd válassza ki **Kudu konzol megnyitása** a legördülő listában.

    ![Nyissa meg a Kudu konzol](./media/luis-tutorial-bot-csharp-appinsights/open-kudu-console.png)

3. A konzolablakban adja meg a következő parancsot:

    ```console
    cd site\wwwroot && build.cmd
    ```

    Várja meg, végezze el a build `Finished successfully.`

## <a name="test-the-web-app-bot"></a>A web app bot tesztelése

1. A web app bot teszteléséhez nyissa meg a **tesztelni, webes csevegési** funkció a portálon.

2. Adja meg a kifejezés `Coffee bar on please`.  

    ![Tesztelje a web app bot Csevegésben](./media/luis-tutorial-bot-csharp-appinsights/test-in-web-chat.png)

3. Nincs különbség a csevegőrobot választ kell megjelennie. A módosítás adatokat továbbít felé az Application Insights, nem a robot a válaszokat. Adja meg néhány további utterances, így egy kicsit több adat az Application insights szolgáltatásban:

|Beszédmódok|
|--|
|Küldje el a kétpizzás|
|Kapcsolja ki az összes lámpa|
|Kapcsolja be a hall világos|


## <a name="view-luis-entries-in-application-insights"></a>Nézet LUIS bejegyzések az Application insights szolgáltatásban

Nyissa meg az Application Insights a LUIS-bejegyzések megtekintéséhez.

1. Válassza a portál **összes erőforrás** majd szűrés a webalkalmazás robot neve. Kattintson az erőforráson típusú **Application Insights**. Az ikonra az Application Insights egy villanykörte.

    ![Keresés az app insights az Azure Portalon](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights.png)

2. Az erőforrás megnyitása után kattintson a a **keresési** ikonjára a Nagyító ikonra a jobb szélen panelen. Egy új panel a jobb oldali jeleníti meg. Telemetriai adatok mennyiségétől függően található, a panel megjelenítéséhez egy második is igénybe vehet. Keressen a `LUIS` kifejezésre. A listában csak a LUIS lekérdezési eredmények ebben az oktatóanyagban hozzáadja van szűkíthető.

    ![Nyomok keresése](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace.png)

3. Válassza ki a legfelső bejegyzésre. Egy új ablakban jeleníti meg a részletes – jobb szélen a LUIS lekérdezés egyéni adatokat is beleértve. A felső célt, és a pontszám szerepel.

    ![Tekintse át a nyomkövetési elem](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace-item.png)

    Amikor elkészült, válassza a jobb szélső felső **X** térjen vissza a függőségi elemek listáját.

> [!Tip]
> Ha azt szeretné, mentheti a függőségi listát, és később még visszatérünk rá, kattintson a **... További** kattintson **mentés kedvenc**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Lekérdezés az Application Insights szándékot, pontszám és utterance (kifejezés)

Az Application Insights lehetővé teszi az adatok lekérdezéséhez a [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) nyelven, valamint exportálási, hogy [Power bi](https://powerbi.microsoft.com).

1. Kattintson a **Analytics** felső részén a függőség, listázása, fent a Szűrő mezőbe.

    ![Elemzés gomb](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-analytics-button.png)

2. Egy lekérdezési ablak tetején, és a egy tábla ablak, amely alatt megjelenik egy új ablak. Ha a használt adatbázisokat korábban, ezzel az elrendezéssel fokozott tisztában van-e. A lekérdezés tartalmazza az elmúlt 24 órában nevét kezdetű elemeivel `LUIS`. A **CustomDimensions** oszlopnak a LUIS-lekérdezés eredménye név/érték párokként.

    ![Alapértelmezett analitikai jelentés](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-1.png)

3. Is, a felső szándékot, pontszám és utterance (kifejezés), adja hozzá a következő felett az utolsó sort a lekérdezési ablakban:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

4. Futtassa a lekérdezést. Görgessen az adatok tábla jobb szélén. Az új oszlopok topIntent, pontszám és utterance (kifejezés) érhetők el. Kattintson a topIntent oszlopra kattintva rendezheti a.

    ![Egyéni elemzési jelentés](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-2.png)

Tudjon meg többet a [Kusto-lekérdezés nyelvi](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) vagy [az adatok exportálása Power bi-bA](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi).

## <a name="learn-more-about-bot-framework"></a>További tudnivalók a Bot Frameworkről

Tudjon meg többet [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>További lépések

Egyéb információkat, érdemes hozzáadni az application insights-adatok tartalmazza Alkalmazásazonosító, a verzió azonosítója, a legutóbbi modell dátuma, a legutóbbi train dátuma, a legutóbbi közzététel dátuma. Ezek az értékek vagy olvashatók be a végpont URL-címe, (Alkalmazásazonosító és verzió azonosítója), vagy a egy [API szerzői](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) hívja, majd a web app bot beállítások, és onnan kéri le.  

Végpont ugyanahhoz az előfizetéshez egynél több LUIS alkalmazás használ, akkor is tartalmaznia kell az előfizetés-azonosító és a egy tulajdonság arról, hogy egy megosztott kulcsot.

> [!div class="nextstepaction"]
> [További tudnivalók a példa kimondott szöveg](luis-how-to-add-example-utterances.md)
