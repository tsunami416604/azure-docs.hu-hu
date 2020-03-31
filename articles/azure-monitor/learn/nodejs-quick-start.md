---
title: 'Rövid útmutató: Node.js figyelése az Azure Monitor Application Insights szolgáltatással'
description: Utasításokat tartalmaz a Node.js webalkalmazás gyors beállításához az Azure Monitor Application Insights segítségével történő figyeléshez
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/12/2019
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 694d2ae529202223869fcbb2a084e32bccaedbf1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77660223"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Rövid útmutató: A Node.js webalkalmazás figyelésének megkezdése az Azure Application Insights segítségével

Ebben a rövid útmutatóban adja hozzá az Application Insights SDK 0.22-es verzióját a Node.js egy meglévő Node.js webalkalmazáshoz.

Az Azure Application Insights segítségével egyszerűen monitorozhatja webalkalmazása rendelkezésre állását, teljesítményét és használatát. Emellett egyszerűen azonosíthatja és diagnosztizálhatja az alkalmazás hibáit anélkül, hogy meg kellene várnia, amíg egy felhasználó jelenti azokat. Az SDK 0.20-as verziós kiadásától kezdve a gyakori külsős csomagok, például a MongoDB, a MySQL és a Redis monitorozása is lehetségessé vált.

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Egy működő Node.js alkalmazás.

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

Az Application Insights telemetriai adatokat gyűjthet bármely internetkapcsolattal rendelkező alkalmazásból, függetlenül attól, hogy a helyszíni vagy a felhőben fut-e. Az adatok megjelenítéséhez hajtsa végre az alábbi lépéseket.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Válassza az > **Erőforrás-fejlesztői eszközök** >  **létrehozása****Alkalmazáselemzési**lehetőséget.

   ![Azure Application Insights-erőforrás hozzáadása](./media/nodejs-quick-start/azure-app-insights-create-resource.png)

   > [!NOTE]
   >Ha ez az első alkalom, hogy egy Application Insights-erőforrást hoz létre, további információkat kaphat az [Application Insights-erőforrás létrehozása](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) doc ellátogatva.

   Megjelenik egy konfigurációs lap; a beviteli mezők kitöltéséhez használja az alábbi táblázatot. 

    | Beállítások        | Érték           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Név**      | Globálisan egyedi érték | A figyelt alkalmazást azonosító név |
   | **Erőforráscsoport**     | myResourceGroup      | Az AppInsights-adatok üzemeltetéséhez az új erőforráscsoport neve. Létrehozhat új erőforráscsoportot, vagy használhat egy meglévőt. |
   | **Helyen** | USA keleti régiója | Válasszon egy Önhöz vagy az alkalmazást futtató gazdagéphez közeli helyet. |

3. Kattintson a **Létrehozás** gombra.

## <a name="configure-appinsights-sdk"></a>Az AppInsights SDK konfigurálása

1. Válassza **az Áttekintés** lehetőséget, és másolja az alkalmazás **Instrumentation kulcsát.**

   ![Az Application Insights instrumentation kulcs megtekintése](./media/nodejs-quick-start/azure-app-insights-instrumentation-key.png)

2. Adja hozzá a Node.js-hez készült Application Insights SDK-t az alkalmazáshoz. Az alkalmazás gyökérmappájából futtassa a következőket:

   ```bash
   npm install applicationinsights --save
   ```

3. Eded az alkalmazás első *.js* fájlját, és add hozzá az alábbi két sort a parancsfájl legfelső részéhez. Ha a [Node.js gyorsútmutatóalkalmazást](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs)használja, módosítsa az *index.js* fájlt. Cserélje `<instrumentation_key>` le az alkalmazás műszerezési kulcsát. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Indítsa újra az alkalmazást.

> [!NOTE]
> 3–5 percet is igénybe vehet, mire az adatok elkezdenek megjelenni a portálon. Ha az alkalmazás egy alacsony forgalmú tesztalkalmazás, vegye figyelembe, hogy a legtöbb metrika rögzítése csak akkor történik, ha aktív kérések és műveletek vannak folyamatban.

## <a name="start-monitoring-in-the-azure-portal"></a>Monitorozás indítása az Azure Portalon

1. Most megnyithatja ismét az Application Insights **Áttekintés** lapját az Azure Portalon (ahol a kialakítási kulcsot is megjelenítette) a jelenleg futó alkalmazás részleteinek megtekintéséhez.

   ![Az Application Insights – áttekintés menü](./media/nodejs-quick-start/azure-app-insights-overview-menu.png)

2. **Válassza ki az Alkalmazásleképezést** az alkalmazás-összetevők közötti függőségi kapcsolatok vizuális elrendezéséhez. Minden egyes összetevőnél megjelennek a KPI-k, például a terhelés, a teljesítmény, a hibák és a riasztások.

   ![Application Insights-alkalmazástérkép](./media/nodejs-quick-start/azure-app-insights-application-map.png)

3. Válassza az **Alkalmazáselemzés** ikon ![](./media/nodejs-quick-start/azure-app-insights-analytics-icon.png) alkalmazástérképikont, az Analytics **nézetet.**  Ez a művelet megnyitja **az Application Insights Analytics**alkalmazást, amely gazdag lekérdezési nyelvet biztosít az Application Insights által gyűjtött összes adat elemzéséhez. Esetünkben most egy lekérdezés jön létre, amely a kérések számát egy diagramon jeleníti meg. A további adatok elemzéséhez írhat saját lekérdezéseket is.

   ![Az Application Insights Analytics grafikonjai](./media/nodejs-quick-start/azure-app-insights-analytics-queries.png)

4. Lépjen vissza az **Áttekintés** lapra, és vizsgálja meg a KPI-gráfokat.  Ezen az irányítópulton az alkalmazás állapotával kapcsolatos statisztikák jelennek meg, köztük a bejövő kérések száma, az egyes kérések időtartama, valamint az esetleges hibák.

   ![Az Application Insights állapotának áttekintése ütemtervgrafikonok](./media/nodejs-quick-start/azure-app-insights-health-overview.png)

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

5. A bal oldalon válassza az **Metrikák** lehetőséget. A metrikakezelő segítségével vizsgálja meg az erőforrás állapotát és kihasználtságát. Az **Új diagram hozzáadása** lehetőséget választva további egyéni nézeteket hozhat létre, vagy a **Szerkesztés** lehetőséget választva módosíthatja a meglévő diagramtípusokat, magasságot, színpalettát, csoportosításokat és mutatókat. Létrehozhat például egy diagramot, amely megjeleníti a böngésző oldalának átlagos betöltési idejét, ha a legördülő számokból a "Böngészőoldal betöltési idejét" és az összesítésből az "Avg" lehetőséget választja. Ha többet szeretne megtudni az Azure Metrics Explorerről, olvassa el [az Azure Metrics Explorer első lépéseit.](../../azure-monitor/platform/metrics-getting-started.md)

   ![Az Application Insights-kiszolgáló mérőszámai grafikon](./media/nodejs-quick-start/azure-app-insights-server-metrics.png)

Ha többet szeretne megtudni a Node.js figyeléséről, olvassa el az [AppInsights Node.js további dokumentációját.](../../azure-monitor/app/nodejs.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a teszteléssel, törölheti az erőforráscsoportot és az összes kapcsolódó erőforrást. Ehhez kövesse az alábbi lépéseket.

> [!NOTE]
> Ha egy meglévő erőforráscsoportot használt, az alábbi utasítások nem fognak működni, és csak törölnie kell az egyes Application Insights-erőforrást. Ne feledje, hogy bármikor töröl egy erőforráscsoportot, minden alászbontó erőforrás, amely a csoport tagja, törlődik.

1. Az Azure Portal bal oldali menüjében válassza az **Erőforráscsoportok** lehetőséget, majd válassza a **myResourceGroup**lehetőséget.
2. Az erőforráscsoport lapján válassza a **Törlés**lehetőséget, írja be a **myResourceGroup** értéket a szövegmezőbe, majd kattintson a **Törlés gombra.**

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Teljesítményproblémák észlelése és diagnosztizálása](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
