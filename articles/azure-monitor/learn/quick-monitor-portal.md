---
title: Az ASP.NET-webalkalmazás monitorozása az Azure Application Insights segítségével | Microsoft Docs
description: Útmutatást nyújt a ASP.NET-webalkalmazások gyors beállításához Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: 1aab9009c2c33522abdeae6ca865a35b9fc104e4
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900488"
---
# <a name="start-monitoring-your-aspnet-web-application"></a>Az ASP.NET-alkalmazás monitorozásának indítása

Az Azure Application Insights segítségével egyszerűen monitorozhatja webalkalmazása rendelkezésre állását, teljesítményét és használatát.  Emellett egyszerűen azonosíthatja és diagnosztizálhatja az alkalmazás hibáit anélkül, hogy meg kellene várnia, amíg egy felhasználó jelenti azokat.  Az Application Insightsból az alkalmazás teljesítményéről és hatékonyságáról gyűjtött információkkal tájékozott döntéseket hozhat az alkalmazás karbantartásával és továbbfejlesztésével kapcsolatban.

Ez a rövid útmutató bemutatja, hogyan adhatja hozzá az Application Insightst a meglévő ASP.NET-webalkalmazáshoz, és hogyan indíthatja el az élő statisztika monitorozását, amely az alkalmazás elemzésére használható számos módszer egyike. Ha nem rendelkezik ASP.NET-webalkalmazással, a [ASP.net-Webalkalmazás létrehozása](../../app-service/app-service-web-get-started-dotnet-framework.md)című rövid útmutatóban hozhat létre egyet.

## <a name="prerequisites"></a>Előfeltételek
A gyorsútmutató elvégzéséhez:

- Telepítse a [Visual Studio 2019](https://www.visualstudio.com/downloads/) -et a következő munkaterhelésekkel:
    - ASP.NET és webfejlesztés
    - Azure-fejlesztés


Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

1. Nyissa meg a projektet a Visual Studio 2019-ben.
2. Válassza **Az Application Insights konfigurálása** elemet a Projekt menüben. A Visual Studio hozzáadja az Application Insights SDK-t az alkalmazásához.

    > [!IMPORTANT]
    > Az Application Insights hozzáadásának folyamata ASP.NET-sablontípusonként változik. Az **üres** vagy az **Azure Mobile App** sablon használata esetén válassza a **Projekt** > **Application Insights Telemetria hozzáadása** lehetőséget. Minden más ASP.NET-sablonnal kapcsolatban tekintse át a fenti lépésben található utasításokat. 

3. Válassza a **Get Started** (Első lépések) lehetőséget (a Visual Studio korábbi verzióiban ehelyett a **Start Free** (Kezdje meg ingyen) gomb található).

    ![Application Insights hozzáadása a Visual Studióhoz](./media/quick-monitor-portal/add-application-insights-b.png)

4. Válassza ki előfizetését, és kattintson a **Register** (Regisztráció) gombra.

5. Válassza a **projekt** > **NuGet-csomagok kezelése** > **csomag forrása: NUGET.org** > **frissítse** a Application Insights SDK-csomagokat a legújabb stabil kiadásra.

6. Futtassa az alkalmazást a **Hibakeresés** menü **Hibakeresés indítása** elemének kiválasztásával vagy az F5 billentyű lenyomásával.

## <a name="confirm-app-configuration"></a>Alkalmazáskonfiguráció jóváhagyása

Az Application Insights a futtatás helyétől függetlenül telemetriai adatokat gyűjt az alkalmazásról. Az adatok megjelenítéséhez hajtsa végre az alábbi lépéseket.

1. Az Application Insights megnyitásához kattintson a **View (Nézet)**  -> **Other Windows (Egyéb ablakok)**  -> **Application Insights Search (Application Insights-keresés)** elemre.  Megjelenik az aktuális munkamenetből származó telemetria.<BR><br>![Telemetria a Visual Studióban](./media/quick-monitor-portal/telemetry-in-vs.png)

2. A kérés részleteinek megtekintéséhez kattintson a lista első kérésére (ebben a példában: GET Home/Index). Figyelje meg, hogy az állapotkód és a válaszidő is megtalálható a részletek között, a kérésre vonatkozó más értékes információkkal együtt.<br><br>![Válasz részletei a Visual Studióban](media/quick-monitor-portal/request-details.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Monitorozás indítása az Azure Portalon

Most megnyithatja az Application Insightst az Azure Portalon a futó alkalmazás különböző részleteinek megtekintéséhez.

1. Bontsa ki a **csatlakoztatott szolgáltatások** mappát (felhő és beépülő ikon) a megoldáskezelő, majd kattintson a jobb gombbal a **Application Insights** mappára, és kattintson a **Megnyitás Application Insights portál**elemre.  Megjelenik számos lehetőség és néhány információ az alkalmazásról.

    ![Alkalmazástérkép](media/quick-monitor-portal/04-overview.png)

2. Kattintson az **Alkalmazástérkép** elemre az alkalmazás-összetevők függőségi viszonyait mutató vizuális elrendezés megjelenítéséhez.  Minden egyes összetevőnél megjelennek a KPI-k, például a terhelés, a teljesítmény, a hibák és a riasztások.

    ![Alkalmazástérkép](media/quick-monitor-portal/05-appmap.png)

3. Kattintson az **app Analytics** ikonra ![alkalmazás-hozzárendelési](media/quick-monitor-portal/app-viewinlogs-icon.png) a **naplók (Analitika)** elemre az egyik alkalmazás-összetevőn. Ekkor megnyílik a **naplók (Analitika)** , amely részletes lekérdezési nyelvet biztosít a Application Insights által összegyűjtött összes adatok elemzéséhez. Esetünkben most egy lekérdezés jön létre, amely a kérések számát egy diagramon jeleníti meg. A további adatok elemzéséhez írhat saját lekérdezéseket is.

    ![Elemzés](media/quick-monitor-portal/6viewanalytics.png)

4. Kattintson a **élő metrikastream** elemre a vizsgálat alatt a bal oldalon. Ezzel élő statisztikát jeleníthet meg az alkalmazásról futás közben. Ez a bejövő kérések számával, az adott kérések időtartamával, valamint az esetleges hibákkal kapcsolatos adatokat tartalmaz. Emellett a kritikus teljesítménymutatókat is figyelemmel kísérheti, például a processzor- és memóriahasználatot.

    ![Élő stream](media/quick-monitor-portal/7livemetrics.png)

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

* Külső lépésenkénti videó arról, hogyan [konfigurálható a Application Insights egy .net-alkalmazásból a semmiből](https://www.youtube.com/watch?v=blnGAVgMAfA).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha végzett a teszteléssel, törölheti az erőforráscsoportot és az összes kapcsolódó erőforrást. Ehhez kövesse az alábbi lépéseket.
1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a **myResourceGroup** elemre.
2. Az erőforráscsoport oldalán kattintson a **Törlés** elemre, írja be a **myResourceGroup** szöveget a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban engedélyezte az alkalmazás figyelését az Azure Application Insights.  Folytassa a további oktatóanyagokkal, amelyekből megtudhatja, hogyan használhatja a statisztika monitorozására és az alkalmazáshibák észlelésére is.

> [!div class="nextstepaction"]
> [Azure Application Insights-oktatóanyagok](tutorial-runtime-exceptions.md)
