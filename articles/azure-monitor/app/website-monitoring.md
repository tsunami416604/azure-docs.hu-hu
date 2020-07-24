---
title: 'Gyors útmutató: webhelyek figyelése Azure Monitor Application Insights'
description: Ebből a rövid útmutatóból megtudhatja, hogyan állíthatja be az ügyfél-és böngészőalapú webhelyek figyelését Azure Monitor Application Insights.
ms.topic: quickstart
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: fa2ecd5d953ec0411a122dc7107ce23de1ae5bc6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87014048"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Rövid útmutató: a webhely figyelésének megkezdése Azure Monitor Application Insights

Ebből a rövid útmutatóból megtudhatja, hogyan adhatja hozzá a nyílt forráskódú Application Insights JavaScript SDK-t a webhelyéhez. Azt is megtudhatja, hogyan lehet jobban megismerni a webhelye látogatói számára az ügyfél/böngésző oldali élményt.

Az Azure Monitor Application Insights segítségével egyszerűen monitorozhatja webhelye rendelkezésre állását, teljesítményét és használatát. Emellett egyszerűen azonosíthatja és diagnosztizálhatja az alkalmazás hibáit anélkül, hogy meg kellene várnia, amíg egy felhasználó jelenti azokat. Application Insights a kiszolgálóoldali figyelést, valamint az ügyfél-és böngésző-oldali figyelési képességeket is biztosítja.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Egy webhely, amelyhez hozzá lehet adni a Application Insights JavaScript SDK-t.

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

A Application Insights a helyszínen vagy a felhőben futó, internetkapcsolattal rendelkező alkalmazásokból is gyűjthet telemetria-adatokat. Az alábbi lépések végrehajtásával tekintheti meg ezeket az adatfájlokat:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza **az erőforrás létrehozása**  >  **felügyeleti eszközök**  >  **Application Insights**elemet.

   > [!NOTE]
   >Ha első alkalommal hoz létre Application Insights-erőforrást, tekintse meg [Application Insights erőforrás létrehozása](./create-new-resource.md)című témakört.
1. Amikor megjelenik a konfigurációs mező, a következő táblázat segítségével hajtsa végre a beviteli mezőket:

    | Beállítások        | Érték           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Név**      | Globálisan egyedi érték | A figyelt alkalmazást azonosító név. |
   | **Erőforráscsoport**     | myResourceGroup      | Az új erőforráscsoport neve Application Insights-adattároláshoz. Létrehozhat egy új erőforráscsoportot, vagy használhat egy meglévőt is. |
   | **Hely** | USA keleti régiója | Válasszon egy Önhöz közeli helyet, vagy a közelében, ahol az alkalmazás üzemeltetve van. |
1. Kattintson a **Létrehozás** gombra.

## <a name="create-an-html-file"></a>HTML-fájl létrehozása

1. A helyi számítógépen hozzon létre egy ``hello_world.html`` nevű fájlt. Ebben a példában hozza létre a fájlt a C meghajtó gyökerén úgy, hogy az a következőképpen néz ki: ``C:\hello_world.html`` .
1. Másolja és illessze be az alábbi szkriptet a következőbe ``hello_world.html`` :

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations, visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>Application Insights SDK konfigurálása

1. Válassza az **Áttekintés**  >  **Essentials**lehetőséget, majd másolja az alkalmazás kialakítási **kulcsát**.

   ![Új Application Insights-erőforrás űrlap](media/website-monitoring/instrumentation-key-001.png)

1. Adja hozzá a következő parancsfájlt a ``hello_world.html`` fájlhoz a záró ``</head>`` címke előtt:

   ```javascript
   <script type="text/javascript">
      var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
      {
         instrumentationKey:"INSTRUMENTATION_KEY"
      }
      );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
   </script>
   ```

1. Szerkessze a ``hello_world.html`` fájlt, és adja hozzá az eszközkulcsát.

1. Nyissa meg a ``hello_world.html`` fájlt egy helyi böngésző-munkamenetben. Ez a művelet létrehoz egy egyoldalas nézetet. A böngésző frissítésével több tesztoldalt is létrehozhat.

## <a name="monitor-your-website-in-the-azure-portal"></a>Webhelye figyelése a Azure Portal

1. A jelenleg futó alkalmazás részleteinek megtekintéséhez nyissa meg újra a Azure Portal Application Insights **Áttekintés** lapját. Az **Áttekintés** oldalon beolvasta a kialakítási kulcsot.

   Az áttekintő oldal négy alapértelmezett diagramjának hatóköre a kiszolgálóoldali alkalmazásadatokra van beállítva. Mivel az ügyfél/böngésző oldali interakciókat a JavaScript SDK-val alakítjuk ki, ez az adott nézet nem érvényes, kivéve, ha egy kiszolgálóoldali SDK is telepítve van.

1. Válassza az **elemzési** ![ alkalmazás Térkép ikont ](media/website-monitoring/006.png) .  Ez a művelet megnyitja az **elemzést**, amely részletes lekérdezési nyelvet biztosít a Application Insights által összegyűjtött összes adatok elemzéséhez. Az ügyféloldali böngésző kéréseivel kapcsolatos adatmegjelenítéshez futtassa a következő lekérdezést:

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   ![Az adott időtartamon belüli felhasználói kéréseket mutató elemzési diagram](./media/website-monitoring/analytics-query.png)

1. Térjen vissza az **Áttekintés** oldalra. A **vizsgálat** fejléc alatt válassza a **böngésző**, majd a **teljesítmény**lehetőséget.  A webhely teljesítményével kapcsolatos metrikák jelennek meg. A hibák és kivételek elemzéséhez egy megfelelő nézet tartozik a webhelyén. A [végpontok közötti tranzakció részleteinek](../../azure-monitor/app/transaction-diagnostics.md)eléréséhez választhat **mintákat** .

   ![Kiszolgálómetrikák diagram](./media/website-monitoring/browser-performance.png)

1. A fő Application Insights menüben a **használat** fejléc alatt válassza a [**felhasználók**](../../azure-monitor/app/usage-segmentation.md) lehetőséget a [felhasználói viselkedés elemzési eszközeinek](../../azure-monitor/app/usage-overview.md)megkezdéséhez. Mivel egyetlen gépről próbálunk tesztelni, csak egy felhasználó adatait fogjuk látni. Az élő webhelyek esetében a felhasználók eloszlása így néz ki:

     ![Felhasználók diagram](./media/website-monitoring/usage-users.png)

1. Több oldalt tartalmazó összetettebb webhelyhez a [**Felhasználókövetés**](../../azure-monitor/app/usage-flows.md) eszközzel nyomon követheti a látogatók által a webhely különböző részein áthaladó útvonalakat.

   ![Felhasználókövetés vizualizációja](./media/website-monitoring/user-flows.png)

A webhelyek figyelésére szolgáló speciális konfigurációk megismeréséhez tekintse meg a [JavaScript SDK API-referenciáját](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha azt tervezi, hogy további rövid útmutatókkal vagy oktatóanyagokkal dolgozik tovább, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ellenkező esetben a következő lépésekkel törölheti a rövid útmutatóban létrehozott összes erőforrást a Azure Portal.

> [!NOTE]
> Ha meglévő erőforráscsoportot használt, a következő utasítások nem fognak működni. Ehelyett egyszerűen törölheti az egyes Application Insights erőforrásait. Vegye figyelembe, hogy amikor töröl egy erőforráscsoportot, az adott csoport tagjaihoz tartozó összes underyling-erőforrás is törlődik.

1. A Azure Portal bal oldali menüjében válassza az **erőforráscsoportok**lehetőséget, majd válassza a **myResourceGroup** vagy az ideiglenes erőforráscsoport nevét.
1. Az erőforráscsoport lapon válassza a **Törlés**lehetőséget, írja be a **myResourceGroup** szöveget a szövegmezőbe, majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Teljesítményproblémák észlelése és diagnosztizálása](../log-query/log-query-overview.md)
