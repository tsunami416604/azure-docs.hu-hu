---
title: "Application Insights Windows-szolgáltatásokhoz és feldolgozói szerepkörökhöz | Microsoft Docs"
description: "Adja hozzá manuálisan az Application Insights SDK-t az ASP.NET-alkalmazáshoz a használat, a rendelkezésre állás és a teljesítmény elemzése érdekében."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 106ba99b-b57a-43b8-8866-e02f626c8190
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/01/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: aac35e524759d5f4356e9a9e386f658e9003d2ca


---
# <a name="manually-configure-application-insights-for-aspnet-4-applications"></a>Az Application Insights manuális beállítása az ASP.NET 4-alkalmazásokhoz
Az [Application Insights](app-insights-overview.md) egy bővíthető eszköz a webfejlesztők számára, amellyel megfigyelhető az élő alkalmazásának teljesítménye és használata. Manuálisan konfigurálható a Windows-szolgáltatások, feldolgozói szerepkörök és más ASP.NET-alkalmazások figyelésére. Webalkalmazások esetén a Visual Studio által kínált [automatikus beállítás](app-insights-asp-net.md) mellett manuális konfigurálást is választhat.

![Példa teljesítményfigyelő diagramokra](./media/app-insights-windows-services/10-perf.png)

#### <a name="before-you-start"></a>Előkészületek
A következők szükségesek:

* Egy [Microsoft Azure](http://azure.com)-előfizetés. Ha a csapata vagy a szervezete rendelkezik Azure-előfizetéssel, a tulajdonosa Önt is hozzáadhatja a [Microsoft-fiókja](http://live.com) segítségével.
* Visual Studio 2013 vagy újabb.

## <a name="a-nameadda1-create-an-application-insights-resource"></a><a name="add"></a>1. Application Insights-erőforrás létrehozása
Jelentkezzen be az [Azure portálra](https://portal.azure.com/), és hozzon létre egy új Application Insights-erőforrást. Az alkalmazás típusának válassza az ASP.NET lehetőséget.

![Kattintson az Új, majd az Application Insights lehetőségre](./media/app-insights-windows-services/01-new-asp.png)

Az Azure-ban az [erőforrás](app-insights-resources-roles-access-control.md) egy szolgáltatáspéldány. Az alkalmazás telemetriájának elemzése és bemutatása az erőforrásban történik.

A kiválasztott alkalmazástípus adja meg az erőforráspanelek alapértelmezett tartalmát, valamint a [Metrikaböngészőben](app-insights-metrics-explorer.md) látható tulajdonságokat.

#### <a name="copy-the-instrumentation-key"></a>A kialakítási kulcs másolása
A kulcs azonosítja az erőforrást, és hamarosan telepíteni fogja azt az SDK-ba, hogy az adatokat az erőforrásba irányíthassa.

![Kattintson a Tulajdonságok elemre, válassza ki a kulcsot, és nyomja le a ctrl+C billentyűkombinációt.](./media/app-insights-windows-services/02-props-asp.png)

Az új erőforrás létrehozásához az imént elvégzett lépések jól használhatók bármely alkalmazás figyelésének megkezdéséhez. Most már küldhet ide adatokat.

## <a name="a-namesdka2-install-the-sdk-in-your-application"></a><a name="sdk"></a>2. Az SDK telepítése az alkalmazásban
Az Application Insights SDK telepítése és konfigurálása a használt platformtól függően eltérő lehet. ASP.NET alkalmazásoknál ez nagyon egyszerű.

1. Visual Studióban szerkessze a webalkalmazási projekt NuGet-csomagjait.
   
    ![Kattintson a jobb gombbal a projektre, és válassza a Manage Nuget Packages (NuGet-csomagok kezelése) lehetőséget](./media/app-insights-windows-services/03-nuget.png)
2. Az Application Insights SDK telepítése a webalkalmazásokhoz
   
    ![Az „Application Insights” kifejezés keresése](./media/app-insights-windows-services/04-ai-nuget.png)
   
    *Használhatok más csomagokat is?*
   
    Igen. Válassza a fő API-t (Microsoft.ApplicationInsights), ha csak a saját telemetriája küldésére kívánja használni az API-t. A Windows Server-csomag automatikusan tartalmazza a fő API-t és más csomagokat, például a teljesítményszámlálót és a függőségfigyelést. 

#### <a name="to-upgrade-to-future-sdk-versions"></a>Frissítés a jövőbeli SDK-verziókra
Időről-időre kiadunk egy új SDK-verziót.

Ha frissíteni szeretne egy [új SDK-kiadásra](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases/), nyissa meg ismét a NuGet-csomagkezelőt, és szűréssel keresse meg a telepített csomagokat. Jelölje ki a **Microsoft.ApplicationInsights.Web** lehetőséget, és válassza a **Frissítés** elemet.

Ha az ApplicationInsights.config fájlt testreszabta, mentse el egy példányát a frissítés előtt, majd egyesítse a módosításait az új verzióval.

## <a name="3-send-telemetry"></a>3. Telemetria küldése
**Ha csak a fő API-csomagot telepítette:**

* Állítsa be a rendszerállapotkulcsot a kódban, például `main()`: 
  
    `TelemetryConfiguration.Active.InstrumentationKey = "` *az Ön kulcsa* `";` 
* [Írjon saját telemetriát az API-val](app-insights-api-custom-events-metrics.md#ikey).

**Ha más Application Insights-csomagokat is telepített,** akkor a .config fájl segítségével igény szerint beállíthatja a rendszerállapotkulcsot:

* Szerkessze az ApplicationInsights.config fájlt (ezt korábban a NuGet telepítése során adta hozzá). A címke zárása elé illessze be a következőt:
  
    `<InstrumentationKey>` *a kimásolt kialakítási kulcs* `</InstrumentationKey>`
* Győződjön meg arról, hogy az ApplicationInsights.config tulajdonságait a következőre állította a Megoldáskezelőben: **Build Action = Content, Copy to Output Directory = Copy**.

## <a name="a-nameruna-run-your-project"></a><a name="run"></a> A projekt futtatása
Futtassa az **F5** billentyűvel az alkalmazást, és próbálja ki: nyisson meg több oldalt, hogy létrejöjjön valamennyi telemetria.

A Visual Studióban láthatja az elküldött események számát.

![Események száma a Visual Studióban](./media/app-insights-windows-services/appinsights-09eventcount.png)

## <a name="a-namemonitora-view-your-telemetry"></a><a name="monitor"></a> A telemetriai adatok megtekintése
Térjen vissza az [Azure Portalra](https://portal.azure.com/), és keresse meg az Application Insights-erőforrást.

Az Áttekintés diagramokon keresse meg az adatot. Először csak egy vagy két pontot lát. Példa:

![Kattintson végig rajtuk a további adatokért](./media/app-insights-windows-services/12-first-perf.png)

Részletesebb mérőszámokért kattintson bármelyik diagramra. [További információk a metrikákról.](app-insights-web-monitor-performance.md)

#### <a name="no-data"></a>Nincs adat?
* Az alkalmazás segítségével nyisson meg különböző oldalakat, hogy létrejöjjön némi telemetria.
* Az egyes események megtekintéséhez nyissa meg a [Keresés](app-insights-diagnostic-search.md)csempét. Események esetében kicsit tovább is eltarthat a mérőszámok folyamatain való végighaladás.
* Várjon néhány másodpercet, és kattintson a **Frissítés** lehetőségre. A diagramok rendszeres időközönként frissülnek, de manuálisan is frissítheti őket, ha várja valamilyen adatok megjelenését.
* Lásd: [Hibaelhárítás](app-insights-troubleshoot-faq.md).

## <a name="publish-your-app"></a>Az alkalmazás közzététele
Most telepítse az alkalmazását a kiszolgálóra vagy az Azure-ba, és figyelje meg, hogyan gyűlnek az adatok.

![Az alkalmazás közzététele a Visual Studio segítségével](./media/app-insights-windows-services/15-publish.png)

Ha hibakeresési módban futtatja az alkalmazást, az egész folyamatban szolgáltat telemetriát a rendszer, így másodperceken belül meg kell jelenniük az adatoknak. Ha Kiadás konfigurációban telepíti az alkalmazását, az adatok lassabban gyűlnek.

#### <a name="no-data-after-you-publish-to-your-server"></a>Nem lát adatokat a kiszolgálón való közzététel után?
Nyissa meg ezeket a portokat a kimenő forgalom számára a kiszolgáló tűzfalán:

* `dc.services.visualstudio.com:443`
* `f5.services.visualstudio.com:443`

#### <a name="trouble-on-your-build-server"></a>Probléma adódott a lemezképfájl-kiszolgálóján?
Tekintse meg [ezt a Hibaelhárítási cikket](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

> [!NOTE]
> Ha az alkalmazása sok telemetriát hoz létre (és az ASP.NET SDK 2.0.0-beta3 vagy újabb verzióját használja), az adaptív mintavételezési modul automatikusan csökkenti a portálra küldött kötetet, csupán az eseményeket megjelenítő töredékeket küld. Az azonos kéréshez tartozó események azonban csoportosan lesznek kijelölve, illetve így lesz törölve a jelölésük, hogy lehessen mozogni a kapcsolódó események között. 
> [Ismerkedés a mintavételezéssel](app-insights-sampling.md).
> 
> 

## <a name="next-steps"></a>Következő lépések
* [További telemetriák hozzáadásával](app-insights-asp-net-more.md) az alkalmazást teljes körűen megfigyelheti.




<!--HONumber=Nov16_HO2-->


