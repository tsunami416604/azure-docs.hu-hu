---
title: 'Gyors útmutató: Figyelés az Azure Application Insights szolgáltatással'
description: A Node.js-webalkalmazások gyors üzembe helyezésével kapcsolatos utasítások az Application Insights-monitorozáshoz
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/12/2019
ms.service: application-insights
ms.custom: mvc, seo-javascript-september2019
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 1db26002167f4b7c5b4fc19699ddb021de8ac23d
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703014"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Gyors útmutató: A Node. js-alapú webalkalmazás figyelésének megkezdése az Azure Application Insights

Az Azure Application Insights segítségével egyszerűen monitorozhatja webalkalmazása rendelkezésre állását, teljesítményét és használatát. Emellett egyszerűen azonosíthatja és diagnosztizálhatja az alkalmazás hibáit anélkül, hogy meg kellene várnia, amíg egy felhasználó jelenti azokat. Az SDK 0.20-as verziós kiadásától kezdve a gyakori külsős csomagok, például a MongoDB, a MySQL és a Redis monitorozása is lehetségessé vált.

Ez a rövid útmutató végigvezeti a Node.js-hez készült Application Insights SDK 0.22-es verziója egy meglévő Node.js-webalkalmazáshoz történő hozzáadásának folyamatán.

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

- Szüksége lesz egy Azure-előfizetésre és egy meglévő Node.js-webalkalmazásra.

Ha nem rendelkezik Node.js-webalkalmazással, a létrehozásához követheti a [Node.js-webalkalmazások létrehozásához készült útmutató](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs) utasításait.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

Az Application Insights bármely, az internethez csatlakozó alkalmazásról képes telemetriaadatokat gyűjteni, függetlenül attól, hogy az a helyszínen vagy a felhőben fut-e. Az adatok megjelenítéséhez hajtsa végre az alábbi lépéseket.

1. Válassza az **Erőforrás létrehozása** > **Fejlesztői eszközök** > **Application Insights** elemet.

   ![Application Insights-erőforrások hozzáadása](./media/nodejs-quick-start/1createresourseappinsights.png)

   > [!NOTE]
   >Ha első alkalommal hoz létre egy Application Insights-erőforrást, további információt az [Application Insights erőforrás létrehozása](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) doc webhelyén olvashat.

   Megjelenik egy konfigurációs oldal; a beviteli mezők kitöltéséhez használja az alábbi táblázatot. 

    | Beállítások        | Value           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Name**      | Globálisan egyedi érték | A figyelt alkalmazást azonosító név |
   | **Alkalmazás típusa** | Node.js-alkalmazás | A figyelt alkalmazás típusa |
   | **Location** | East US | Válasszon egy Önhöz vagy az alkalmazást futtató gazdagéphez közeli helyet. |

2. Kattintson a **Létrehozás** gombra.

## <a name="configure-app-insights-sdk"></a>Az App Insights SDK konfigurálása

1. Válassza az **Áttekintés** lehetőséget, és másolja az alkalmazás kialakítási **kulcsát**.

   ![Új App Insights-erőforrás űrlap](./media/nodejs-quick-start/3key.png)

2. Adja hozzá a Node.js-hez készült Application Insights SDK-t az alkalmazáshoz. Az alkalmazás gyökérmappájából futtassa a következőket:

   ```bash
   npm install applicationinsights --save
   ```

3. Módosítsa az alkalmazás első .js-fájlját, és adja hozzá az alábbi két sort a szkript legtetejéhez. A [Node.js rövid útmutatójában található alkalmazás](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs) használata esetén az index.js fájlt kell módosítania. Az &lt;instrumentation_key&gt; helyére írja be az alkalmazása eszközkulcsát. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Indítsa újra az alkalmazást.

> [!NOTE]
> 3–5 percet is igénybe vehet, mire az adatok elkezdenek megjelenni a portálon. Ha az alkalmazás egy alacsony forgalmú tesztalkalmazás, vegye figyelembe, hogy a legtöbb metrika rögzítése csak akkor történik, ha aktív kérések és műveletek vannak folyamatban.

## <a name="start-monitoring-in-the-azure-portal"></a>Monitorozás indítása az Azure Portalon

1. Most megnyithatja ismét az Application Insights **Áttekintés** lapját az Azure Portalon (ahol a kialakítási kulcsot is megjelenítette) a jelenleg futó alkalmazás részleteinek megtekintéséhez.

   ![Az Application Insights áttekintése menü](./media/nodejs-quick-start/4overview.png)

2. Válassza ki az alkalmazás-összetevők közötti függőségi kapcsolatok vizualizációs elrendezéséhez tartozó **Application Map** elemet. Minden egyes összetevőnél megjelennek a KPI-k, például a terhelés, a teljesítmény, a hibák és a riasztások.

   ![Alkalmazástérkép](./media/nodejs-quick-start/5appmap.png)

3. Válassza az **app Analytics** ikon ![alkalmazás-hozzárendelés](./media/nodejs-quick-start/006.png) ikon **nézetét az elemzés elemnél**.  Megnyílik az **Application Insights Analytics**, amely egy részletes lekérdezési nyelvet biztosít az Application Insights által gyűjtött adatok elemzéséhez. Esetünkben most egy lekérdezés jön létre, amely a kérések számát egy diagramon jeleníti meg. A további adatok elemzéséhez írhat saját lekérdezéseket is.

   ![Az adott időtartamon belüli felhasználói kéréseket mutató elemzési diagram](./media/nodejs-quick-start/6analytics.png)

4. Lépjen vissza az **Áttekintés** lapra, és vizsgálja meg a KPI-gráfokat.  Ezen az irányítópulton az alkalmazás állapotával kapcsolatos statisztikák jelennek meg, köztük a bejövő kérések száma, az egyes kérések időtartama, valamint az esetleges hibák.

   ![Az Állapotáttekintési időegyenes diagramjai](./media/nodejs-quick-start/7kpidashboards.png)

   Ahhoz, hogy a **Lapmegtekintés betöltési ideje** diagramon megjelenjenek az **ügyféloldali telemetria** adatai, adja az alábbi szkriptet minden olyan oldalhoz, amelyet nyomon kíván követni:

   ```HTML
   <!-- 
   To collect user behavior analytics tools about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
       function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
       }({
           instrumentationKey:"<insert instrumentation key>"
       });
       
       window.appInsights=appInsights;
       appInsights.trackPageView();
   </script>
   ```

5. A bal oldalon válassza a **metrikák**lehetőséget. Az erőforrás állapotának és kihasználtságának vizsgálatához használja a metrikák Explorert. Az **új diagram hozzáadása** lehetőség kiválasztásával további egyéni nézeteket hozhat létre, vagy a **Szerkesztés** gombra kattintva módosíthatja a meglévő diagramtípust, a magasságot, a színpalettát, a csoportosításokat és a metrikákat. Létrehozhat például egy olyan diagramot, amely megjeleníti a böngésző átlagos betöltési idejét, ha a mérőszámok legördülő menüből kiválasztja a "böngésző oldal betöltési ideje" lehetőséget, és az összesítést az "AVG" értékre. Ha többet szeretne megtudni az Azure Metrikaböngésző-ról, látogasson el [az azure Metrikaböngésző](../../azure-monitor/platform/metrics-getting-started.md)használatába.

   ![Kiszolgálómetrikák diagram](./media/nodejs-quick-start/8metrics.png)

A Node.js monitorozásával kapcsolatos további információkért tekintse át [az App Insights további Node.js-dokumentációját](../../azure-monitor/app/nodejs.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a teszteléssel, törölheti az erőforráscsoportot és az összes kapcsolódó erőforrást. Ehhez kövesse az alábbi lépéseket.

1. A Azure Portal bal oldali menüjében válassza az **erőforráscsoportok** lehetőséget, majd válassza a **myResourceGroup**lehetőséget.
2. Az erőforráscsoport lapon válassza a **Törlés**lehetőséget, írja be a **myResourceGroup** szöveget a szövegmezőbe, majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Teljesítményproblémák észlelése és diagnosztizálása](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
