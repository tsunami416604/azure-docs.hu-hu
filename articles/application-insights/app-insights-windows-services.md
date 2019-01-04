---
title: Azure Application Insights Windows Serverhez és feldolgozói szerepkörökhöz | Microsoft Docs
description: Adja hozzá manuálisan az Application Insights SDK-t az ASP.NET-alkalmazáshoz a használat, a rendelkezésre állás és a teljesítmény elemzése érdekében.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 106ba99b-b57a-43b8-8866-e02f626c8190
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: mbullwin
ms.openlocfilehash: b760780912642dac664f68c1f2b9d8d4ebf8cbde
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2018
ms.locfileid: "53809896"
---
# <a name="manually-configure-application-insights-for-net-applications"></a>Az Application Insights manuális beállítása a .NET-alkalmazásokhoz

Konfigurálhatja az [Application Insightsot](app-insights-overview.md) számos különféle alkalmazás vagy alkalmazás-szerepkör, -összetevő vagy mikroszolgáltatás monitorozására. A webalkalmazásokhoz és -szolgáltatásokhoz a Visual Studio [egylépéses konfigurációs lehetőséget ](../azure-monitor/app/asp-net.md) biztosít. Más típusú .NET-alkalmazásokhoz, például a háttérkiszolgálói szerepkörökhöz vagy az asztali alkalmazásokhoz beállíthatja manuálisan az Application Insightsot.

![Példa teljesítményfigyelő diagramokra](./media/app-insights-windows-services/10-perf.png)

#### <a name="before-you-start"></a>Előkészületek

A következők szükségesek:

* Egy [Microsoft Azure](https://azure.com)-előfizetés. Ha a csapata vagy a szervezete rendelkezik Azure-előfizetéssel, a tulajdonosa Önt is hozzáadhatja a [Microsoft-fiókja](https://live.com) segítségével.
* Visual Studio 2013 vagy újabb.

## <a name="add"></a>1. Application Insights-erőforrások választása

Az „erőforrás” az adatok összegyűjtésének és megjelenítésének helye az Azure Portalon. Választhat, hogy újat hoz-e létre, vagy megoszt egy már meglévőt.

### <a name="part-of-a-larger-app-use-existing-resource"></a>Egy nagyobb alkalmazás része: Létező erőforrás használata

Ha a webalkalmazás több részből áll – például egy előtéri webalkalmazásból és egy vagy több háttérszolgáltatásból –, akkor minden helyről ugyanarra az erőforrásra kell telemetriát küldenie. Ez lehetővé teszi, hogy egyetlen alkalmazástérképen megjeleníthetőek legyenek, valamint nyomon követhetőek legyenek a kérések az egyes összetevők közt.

Így ha már monitorozza az alkalmazás más összetevőit is, akkor egyszerűen használhatja ugyanazt az erőforrást.

Nyissa meg az erőforrást az [Azure Portalon](https://portal.azure.com/). 

### <a name="self-contained-app-create-a-new-resource"></a>Önálló alkalmazás: Új erőforrás létrehozása

Ha az új alkalmazás nem kapcsolódik más alkalmazásokhoz, akkor saját erőforrása kell, hogy legyen.

Jelentkezzen be az [Azure portálra](https://portal.azure.com/), és hozzon létre egy új Application Insights-erőforrást. Az alkalmazás típusának válassza az ASP.NET lehetőséget.

![Kattintson az Új, majd az Application Insights lehetőségre](./media/app-insights-windows-services/01-new-asp.png)

A kiválasztott alkalmazástípus adja meg az erőforráspanelek alapértelmezett tartalmát.

## <a name="2-copy-the-instrumentation-key"></a>2. A kialakítási kulcs másolása
A kulcs azonosítja az erőforrást. Hamarosan telepíteni fogja azt az SDK-ba, hogy az adatokat az erőforrásba irányíthassa.

![Kattintson a Tulajdonságok elemre, válassza ki a kulcsot, és nyomja le a ctrl+C billentyűkombinációt.](./media/app-insights-windows-services/02-props-asp.png)

## <a name="sdk"></a>3. Az Application Insights-csomag telepítése az alkalmazásban
Az Application Insights-csomag telepítése és konfigurálása a használt platformtól függően eltérő lehet. 

1. A Visual Studióban kattintson a jobb gombbal a projektjére, és válassza a **Manage NuGet Packages (NuGet-csomagok kezelése)** lehetőséget.
   
    ![Kattintson a jobb gombbal a projektre, és válassza a Manage Nuget Packages (NuGet-csomagok kezelése) lehetőséget](./media/app-insights-windows-services/03-nuget.png)
2. Telepítse a Windows Server-alkalmazásokhoz tartozó „Microsoft.ApplicationInsights.WindowsServer” Application Insights-csomagot.
   
    ![Az „Application Insights” kifejezés keresése](./media/app-insights-windows-services/04-ai-nuget.png)
   
    *Melyik verzió?*

    Jelölje be az **Include prerelease** (Előzetes verzió belefoglalása) jelölőnégyzetet, ha ki szeretné próbálni a legújabb funkciókat. A megfelelő dokumentumok és blogok jelzik, hogy van-e szüksége előzetes verzióra.
    
    *Használhatok más csomagokat is?*
   
    Igen. Válassza a „Microsoft.ApplicationInsights” lehetőséget, ha csak a saját telemetriája küldésére kívánja használni az API-t. A Windows Server-csomag tartalmazza az API-t és más csomagokat, például a teljesítményszámlálót és a függőségmonitorozást. 

### <a name="to-upgrade-to-future-package-versions"></a>Frissítés a jövőbeli csomagverziókra
Időről-időre kiadunk egy új SDK-verziót.

Ha frissíteni szeretne egy [új csomagkiadásra](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases/), nyissa meg ismét a NuGet-csomagkezelőt, és szűréssel keresse meg a telepített csomagokat. Jelölje ki a **Microsoft.ApplicationInsights.WindowsServer** lehetőséget, és válassza az **Upgrade** (Frissítés) lehetőséget.

Ha az ApplicationInsights.config fájlt testreszabta, mentse el egy példányát a frissítés előtt, majd egyesítse a módosításait az új verzióval.

## <a name="4-send-telemetry"></a>4. Telemetria küldése
**Ha csak az API-csomagot telepítette:**

* Állítsa be a rendszerállapotkulcsot a kódban, például `main()`: 
  
    `TelemetryConfiguration.Active.InstrumentationKey = "`*az Ön kulcsa*`";` 
* [Írjon saját telemetriát az API-val](../azure-monitor/app/api-custom-events-metrics.md#ikey).

**Ha más Application Insights-csomagokat is telepített,** akkor a .config fájl segítségével igény szerint beállíthatja a rendszerállapotkulcsot:

* Szerkessze az ApplicationInsights.config fájlt (ezt korábban a NuGet telepítése során adta hozzá). A címke zárása elé illessze be a következőt:
  
    `<InstrumentationKey>`*a kimásolt kialakítási kulcs*`</InstrumentationKey>`
* Győződjön meg arról, hogy az ApplicationInsights.config tulajdonságait a következőre állította a Megoldáskezelőben: **Build Action = Content, Copy to Output Directory = Copy**.

Hasznos lehet a kialakítási kulcsot kódból megadni, ha [a különböző felépítéskonfigurációkban váltogatni szeretné a kulcsot](app-insights-separate-resources.md). Ha a kulcsot kódból adja meg, nem kell azt beállítania a `.config` fájlban.

## <a name="run"></a> A projekt futtatása
Futtassa az **F5** billentyűvel az alkalmazást, és próbálja ki: nyisson meg több oldalt, hogy létrejöjjön valamennyi telemetria.

A Visual Studióban láthatja az elküldött események számát.

![Események száma a Visual Studióban](./media/app-insights-windows-services/appinsights-09eventcount.png)

## <a name="monitor"></a> A telemetriai adatok megtekintése
Térjen vissza az [Azure Portalra](https://portal.azure.com/), és keresse meg az Application Insights-erőforrást.

Az Áttekintés diagramokon keresse meg az adatot. Először csak egy vagy két pontot lát. Példa:

![Kattintson végig rajtuk a további adatokért](./media/app-insights-windows-services/12-first-perf.png)

Részletesebb mérőszámokért kattintson bármelyik diagramra. [További információk a metrikákról.](app-insights-web-monitor-performance.md)

### <a name="no-data"></a>Nincs adat?
* Az alkalmazás segítségével nyisson meg különböző oldalakat, hogy létrejöjjön némi telemetria.
* Az egyes események megtekintéséhez nyissa meg a [Keresés](../azure-monitor/app/diagnostic-search.md)csempét. Események esetében kicsit tovább is eltarthat a mérőszámok folyamatain való végighaladás.
* Várjon néhány másodpercet, és kattintson a **Frissítés** lehetőségre. A diagramok rendszeres időközönként frissülnek, de manuálisan is frissítheti őket, ha várja valamilyen adatok megjelenését.
* Lásd: [Hibaelhárítás](app-insights-troubleshoot-faq.md).

## <a name="publish-your-app"></a>Az alkalmazás közzététele
Most telepítse az alkalmazását a kiszolgálóra vagy az Azure-ba, és figyelje meg, hogyan gyűlnek az adatok.

![Az alkalmazás közzététele a Visual Studio segítségével](./media/app-insights-windows-services/15-publish.png)

Ha hibakeresési módban futtatja az alkalmazást, az egész folyamatban szolgáltat telemetriát a rendszer, így másodperceken belül meg kell jelenniük az adatoknak. Ha Kiadás konfigurációban telepíti az alkalmazását, az adatok lassabban gyűlnek.

### <a name="no-data-after-you-publish-to-your-server"></a>Nem lát adatokat a kiszolgálón való közzététel után?
Nyissa meg a portokat a kimenő forgalom számára a kiszolgáló tűzfalán. A szükséges címek listája [ezen az oldalon](https://docs.microsoft.com/azure/application-insights/app-insights-ip-addresses) tekinthető meg 

### <a name="trouble-on-your-build-server"></a>Probléma adódott a lemezképfájl-kiszolgálóján?
Tekintse meg [ezt a Hibaelhárítási cikket](../azure-monitor/app/asp-net-troubleshoot-no-data.md#NuGetBuild).

> [!NOTE]
> Ha az alkalmazása sok telemetriát hoz létre, az adaptív mintavételezési modul automatikusan csökkenti a portálra küldött mennyiséget, és csupán az eseményeket megjelenítő töredékeket küld. Az azonos kéréshez tartozó események azonban csoportosan lesznek kijelölve, illetve így lesz törölve a jelölésük, hogy lehessen mozogni a kapcsolódó események között. 
> [Ismerkedés a mintavételezéssel](app-insights-sampling.md).
> 
> 

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>További lépések
* [További telemetriák hozzáadásával](../azure-monitor/app/asp-net-more.md) az alkalmazást teljes körűen megfigyelheti.

