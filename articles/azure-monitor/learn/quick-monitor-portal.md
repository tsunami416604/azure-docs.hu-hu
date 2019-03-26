---
title: Az ASP.NET-webalkalmazás monitorozása az Azure Application Insights segítségével | Microsoft Docs
description: Ez a gyors beállítása ASP.NET-WebApp figyelése az Application insights segítségével
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/11/2018
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 37fad0769753f462859809ff8600f5b15c7eaa1f
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437509"
---
# <a name="start-monitoring-your-aspnet-web-application"></a>Az ASP.NET-alkalmazás monitorozásának indítása

Az Azure Application Insights segítségével egyszerűen monitorozhatja webalkalmazása rendelkezésre állását, teljesítményét és használatát.  Emellett egyszerűen azonosíthatja és diagnosztizálhatja az alkalmazás hibáit anélkül, hogy meg kellene várnia, amíg egy felhasználó jelenti azokat.  Az Application Insightsból az alkalmazás teljesítményéről és hatékonyságáról gyűjtött információkkal tájékozott döntéseket hozhat az alkalmazás karbantartásával és továbbfejlesztésével kapcsolatban.

Ez a rövid útmutató bemutatja, hogyan adhatja hozzá az Application Insightst a meglévő ASP.NET-webalkalmazáshoz, és hogyan indíthatja el az élő statisztika monitorozását, amely az alkalmazás elemzésére használható számos módszer egyike. Ha nem rendelkezik ASP.NET-webalkalmazás, egy következő hozhat létre a [hozzon létre egy ASP.NET webes alkalmazás – rövid útmutató](../../app-service/app-service-web-get-started-dotnet-framework.md).

## <a name="prerequisites"></a>Előfeltételek
A gyorsútmutató elvégzéséhez:

- Telepítse a [Visual Studio 2017](https://www.visualstudio.com/downloads/) szoftvert a következő számítási feladatokkal:
    - ASP.NET és webfejlesztés
    - Azure-fejlesztés


Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

1. Nyissa meg a projektjét a Visual Studio 2017-ben.
2. Válassza **Az Application Insights konfigurálása** elemet a Projekt menüben. A Visual Studio hozzáadja az Application Insights SDK-t az alkalmazásához.

    > [!IMPORTANT]
    > Az Application Insights hozzáadásának folyamata ASP.NET-sablontípusonként változik. Az **üres** vagy az **Azure Mobile App** sablon használata esetén válassza a **Projekt** > **Application Insights Telemetria hozzáadása** lehetőséget. Minden más ASP.NET-sablonnal kapcsolatban tekintse át a fenti lépésben található utasításokat. 

3. Válassza a **Get Started** (Első lépések) lehetőséget (a Visual Studio korábbi verzióiban ehelyett a **Start Free** (Kezdje meg ingyen) gomb található).

    ![Application Insights hozzáadása a Visual Studióhoz](./media/quick-monitor-portal/add-application-insights-b.png)

4. Válassza ki előfizetését, és kattintson a **Register** (Regisztráció) gombra.

5. Futtassa az alkalmazást a **Hibakeresés** menü **Hibakeresés indítása** elemének kiválasztásával vagy az F5 billentyű lenyomásával.

## <a name="confirm-app-configuration"></a>Alkalmazáskonfiguráció jóváhagyása

Az Application Insights a futtatás helyétől függetlenül telemetriai adatokat gyűjt az alkalmazásról. Az adatok megjelenítéséhez hajtsa végre az alábbi lépéseket.

1. Az Application Insights megnyitásához kattintson a **View (Nézet)** -> **Other Windows (Egyéb ablakok)** -> **Application Insights Search (Application Insights-keresés)** elemre.  Megjelenik az aktuális munkamenetből származó telemetria.<BR><br>![Telemetria a Visual Studióban](./media/quick-monitor-portal/telemetry-in-vs.png)

2. A kérés részleteinek megtekintéséhez kattintson a lista első kérésére (ebben a példában: GET Home/Index). Figyelje meg, hogy az állapotkód és a válaszidő is megtalálható a részletek között, a kérésre vonatkozó más értékes információkkal együtt.<br><br>![Válasz részletei a Visual Studióban](media/quick-monitor-portal/request-details.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Monitorozás indítása az Azure Portalon

Most megnyithatja az Application Insightst az Azure Portalon a futó alkalmazás különböző részleteinek megtekintéséhez.

1. A Megoldáskezelőben kattintson a jobb gombbal **Az Application Insights kapcsolódó szolgáltatásai** mappára, majd kattintson az **Application Insights portál megnyitása** parancsra.  Megjelenik számos lehetőség és néhány információ az alkalmazásról.

    ![Alkalmazástérkép](media/quick-monitor-portal/overview-001.png)

2. Kattintson az **Alkalmazástérkép** elemre az alkalmazás-összetevők függőségi viszonyait mutató vizuális elrendezés megjelenítéséhez.  Minden egyes összetevőnél megjelennek a KPI-k, például a terhelés, a teljesítmény, a hibák és a riasztások.

    ![Alkalmazástérkép](media/quick-monitor-portal/application-map-001.png)

3. Kattintson az **Alkalmazáselemzés** ikonra, majd az ![Alkalmazástérképre](media/quick-monitor-portal/app-analytics-icon.png) az alkalmazás egyik összetevőjén.  Megnyílik az **Application Insights Analytics**, amely egy részletes lekérdezési nyelvet biztosít az Application Insights által gyűjtött adatok elemzéséhez.  Esetünkben most egy lekérdezés jön létre, amely a kérések számát egy diagramon jeleníti meg.  A további adatok elemzéséhez írhat saját lekérdezéseket is.

    ![Elemzés](media/quick-monitor-portal/analytics.png)

4. Lépjen vissza az **Áttekintés** lapra, és kattintson az **Élő stream** elemre.  Ezzel élő statisztikát jeleníthet meg az alkalmazásról futás közben.  Ez a bejövő kérések számával, az adott kérések időtartamával, valamint az esetleges hibákkal kapcsolatos adatokat tartalmaz.  Emellett a kritikus teljesítménymutatókat is figyelemmel kísérheti, például a processzor- és memóriahasználatot.

    ![Élő stream](media/quick-monitor-portal/live-stream.png)

    Ha készen áll az alkalmazás Azure-on történő üzemeltetésére, most már közzéteheti. Kövesse az [ASP.NET-webalkalmazás létrehozását ismertető rövid útmutató](../../app-service/app-service-web-get-started-dotnet.md#update-the-app-and-redeploy) lépéseit.

5. Ha a Visual Studiót használja az Application Insights-monitorozáshoz, automatikusan hozzáadhatja az ügyféloldali monitorozást. Ha egy alkalmazáshoz manuálisan szeretné hozzáadni az ügyféloldali monitorozást, adja hozzá a következő JavaScript-kódrészletet:

```html
<!-- 
To collect user behavior analytics about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var appInsights=window.appInsights||function(a){
  function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
  }({
      instrumentationKey:"<your instrumentation key>"
  });

window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
</script>
```

További információt a [nyílt forráskódú JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)-ra vonatkozó GitHub-adattárban talál.

## <a name="video"></a>Videó

* Külső részletes videó [konfigurálása az Application Insights .NET-alkalmazás Előzmények](https://www.youtube.com/watch?v=blnGAVgMAfA).

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban engedélyezte, hogy az Azure Application Insights monitorozza az alkalmazását.  Folytassa a további oktatóanyagokkal, amelyekből megtudhatja, hogyan használhatja a statisztika monitorozására és az alkalmazáshibák észlelésére is.

> [!div class="nextstepaction"]
> [Azure Application Insights-oktatóanyagok](tutorial-runtime-exceptions.md)
