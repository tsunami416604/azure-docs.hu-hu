---
title: 'Gyors útmutató: webhelyek figyelése Azure Monitor Application Insights'
description: A rövid útmutató útmutatást nyújt az ügyfél/böngésző oldali webhely figyeléséhez Azure Monitor Application Insights
ms.topic: quickstart
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: 495c40ca8e383dd5a3cf3ba9e5bd42e2936ea015
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80132364"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Rövid útmutató: a webhely figyelésének megkezdése Azure Monitor Application Insights

Ebből a rövid útmutatóból megtudhatja, hogyan adhatja hozzá a nyílt forráskódú Application Insights JavaScript SDK-t a webhelyéhez. Azt is megtudhatja, hogyan lehet jobban megismerni a webhelye látogatói számára az ügyfél/böngésző oldali élményt.

Az Azure Monitor Application Insights segítségével egyszerűen monitorozhatja webhelye rendelkezésre állását, teljesítményét és használatát. Emellett egyszerűen azonosíthatja és diagnosztizálhatja az alkalmazás hibáit anélkül, hogy meg kellene várnia, amíg egy felhasználó jelenti azokat. Application Insights a kiszolgálóoldali figyelést, valamint az ügyfél-és böngésző-oldali figyelési képességeket is biztosítja.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Egy webhely, amelyhez hozzá lehet adni a Application Insights JavaScript SDK-t.

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

Az Application Insights bármely, az internethez csatlakozó alkalmazásról képes telemetria-adatokat gyűjteni, akár a helyszínen, akár a felhőben fut. Az adatok megjelenítéséhez hajtsa végre az alábbi lépéseket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza **az erőforrás** > **létrehozása felügyeleti eszközök** > **Application Insights**elemet.

   > [!NOTE]
   >Ha első alkalommal hoz létre Application Insights-erőforrást, további információt az [Application Insights-erőforrás létrehozása](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) című cikkben talál.

   Megjelenik egy konfigurációs mező. Az adatbeviteli mezők kitöltéséhez használja az alábbi táblát.

    | Beállítások        | Érték           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Név**      | Globálisan egyedi érték | A figyelt alkalmazást azonosító név |
   | **Erőforráscsoport**     | myResourceGroup      | Az új erőforráscsoport neve, amely az Application Insights-adathalmazt tárolja. Létrehozhat egy új erőforráscsoportot, vagy használhat egy meglévőt is. |
   | **Hely** | USA keleti régiója | Válasszon egy Önhöz vagy az alkalmazást futtató gazdagéphez közeli helyet. |

3. Kattintson a **Létrehozás**gombra.

## <a name="create-an-html-file"></a>HTML-fájl létrehozása

1. A helyi számítógépen hozzon létre egy ``hello_world.html`` nevű fájlt. Ebben a példában a fájlt a C: meghajtó gyökerében helyezzük el: ``C:\hello_world.html``.
2. Másolja a szkriptet a ``hello_world.html`` fájlba:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>Application Insights SDK konfigurálása

1. Válassza az **Áttekintés** > **Essentials** > az alkalmazás kialakítási **kulcsának**másolása lehetőséget.

   ![Új Application Insights-erőforrás űrlap](media/website-monitoring/instrumentation-key-001.png)

2. Adja hozzá az alábbi szkriptet a ``hello_world.html`` fájlhoz a záró ``</head>`` címke előtt:

   ```javascript
   <script type="text/javascript">
      var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
      {
         instrumentationKey:"INSTRUMENTATION_KEY"
      }
      );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
   </script>
   ```

3. Szerkessze a ``hello_world.html`` fájlt, és adja hozzá az eszközkulcsát.

4. Nyissa meg a ``hello_world.html`` fájlt egy helyi böngésző-munkamenetben. Ez a művelet egyetlen oldalmegtekintést hoz létre. A böngésző frissítésével több teszt oldalmegtekintést is létrehozhat.

## <a name="start-monitoring-in-the-azure-portal"></a>Monitorozás indítása az Azure Portalon

1. Most újra megnyithatja a Azure Portal Application Insights **Áttekintés** lapját, ahol megtekintheti az aktuálisan futó alkalmazás részleteit. Az **Áttekintés** oldalon beolvasta a kialakítási kulcsot. Az áttekintő oldal négy alapértelmezett diagramjának hatóköre a kiszolgálóoldali alkalmazásadatokra van beállítva. Mivel az ügyfél/böngésző oldali interakciókat a JavaScript SDK-val alakítjuk ki, ez az adott nézet nem érvényes, kivéve, ha egy kiszolgálóoldali SDK is telepítve van.

2. Kattintson az ![Alkalmazástérkép ikon](media/website-monitoring/006.png) **Analytics** elemre.  Ez a művelet megnyitja az **elemzést**, amely részletes lekérdezési nyelvet biztosít a Application Insights által összegyűjtött összes adatok elemzéséhez. Az ügyféloldali böngészőkérésekkel kapcsolatos adatok megtekintéséhez futtassa az alábbi lekérdezést:

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

3. Térjen vissza az **Áttekintés** oldalra. Kattintson a **Böngészés** elemre a **Vizsgálat** fejléc alatt, majd válassza a **Teljesítmény** lehetőséget. Ezen a felületen találja a webhely teljesítményével kapcsolatos metrikákat. A hibák és kivételek elemzése a webhelyén is megfelelő nézet. A **Minták** elemre kattintva megtekintheti az egyes tranzakciók részleteit. Innen pedig elérheti a [tranzakciók részletes adatait tartalmazó](../../azure-monitor/app/transaction-diagnostics.md) felületet.

   ![Kiszolgálómetrikák diagram](./media/website-monitoring/browser-performance.png)

4. A [felhasználói viselkedést elemző eszközök](../../azure-monitor/app/usage-overview.md) megismeréséhez az Application Insights fő menüjében válassza a [**Felhasználók**](../../azure-monitor/app/usage-segmentation.md) elemet a **Használat** fejléc alatt. Mivel egyetlen gépről próbálunk tesztelni, csak egy felhasználó adatait fogjuk látni. Egy élő webhely esetében a felhasználók eloszlása az alábbihoz hasonlóan nézhet ki:

     ![Felhasználók diagram](./media/website-monitoring/usage-users.png)

5. Ha egy összetettebb webhelyet alakítottunk volna ki több oldallal, ehhez egy másik hasznos eszköz a [**Felhasználói folyamatok**](../../azure-monitor/app/usage-flows.md). A **Felhasználókövetéssel** nyomon követheti a látogatóknak a webhely különböző részeit érintő útját.

   ![Felhasználókövetés vizualizációja](./media/website-monitoring/user-flows.png)

A webhelyek monitorozásával kapcsolatos speciális konfigurációkról a [JavaScript SDK API-referenciáiban](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md) olvashat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy további rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ellenkező esetben, ha nem folytatja a műveletet, a következő lépésekkel törölheti a rövid útmutatóban létrehozott összes erőforrást a Azure Portal.

> [!NOTE]
> Ha meglévő erőforráscsoportot használt, az alábbi utasítások nem fognak működni, és csak törölni kell az egyéni Application Insights erőforrást. Ne feledje, hogy bármikor törli az erőforráscsoportot az összes olyan underyling-erőforrást, amely tagja a csoportnak.

1. A Azure Portal bal oldali menüjében kattintson az **erőforráscsoportok**elemre, majd kattintson a **myResourceGroup** vagy az ideiglenes erőforráscsoport nevére.
2. Az erőforráscsoport lapon kattintson a **Törlés**elemre, írja be a **myResourceGroup** szöveget a szövegmezőbe, majd kattintson a **Törlés**gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Teljesítményproblémák észlelése és diagnosztizálása](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
