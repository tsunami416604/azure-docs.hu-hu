---
title: Felhasználói környezetben való használat engedélyezése azonosítók tapasztalat az Azure Application Insights küldése |} A Microsoft Docs
description: Nyomon követheti, hogyan felhasználók át a szolgáltatáson keresztül teheti azok Application Insights egy egyedi, állandó azonosító karakterláncot.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: csharp
ms.topic: conceptual
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 29e76338b8d19ce70dedea971d26a49544e9f152
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54018143"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Az Azure Application Insights használati bővítsék azonosítók felhasználói környezet küldése

## <a name="tracking-users"></a>Felhasználók nyomon követése

Az Application Insights lehetővé teszi, hogy figyelését és nyomon követheti a felhasználók a termék használati eszközöket keresztül:

- [Felhasználók, munkamenetek, események](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
- [Tölcsérek](https://docs.microsoft.com/azure/application-insights/usage-funnels)
- [Adatmegőrzési](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention) Kohorszok
- [Munkafüzetek](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Nyomon követheti, mi a felhasználó időbeli végrehajtja, az Application Insights-azonosító az egyes felhasználók vagy a munkamenet van szüksége. Minden egyéni esemény vagy oldal nézet vegye fel a következő azonosítóval.

- Felhasználók, a tölcsérek, a megőrzési és a Kohorszok: Tartalmazza a felhasználói azonosítóját.
- Munkamenetek: Tartalmazza a munkamenet-azonosítót.

> [!NOTE]
> Ez a speciális cikk sbalování a manuális lépéseket az Application insights segítségével a felhasználói tevékenység nyomon követése. Számos webes alkalmazások **ezeket a lépéseket nem feltétlenül szükséges**, mint az alapértelmezett kiszolgálóoldali SDK-k együtt a [/böngésző ügyféloldali JavaScript SDK](app-insights-website-monitoring.md), általában automatikusan követésére felhasználói tevékenység. Ha még nem konfigurálta [az ügyféloldali figyelés](app-insights-website-monitoring.md) mellett a kiszolgálóoldali SDK-t, először hajtsa végre, és ellenőrizze, hogy ha a felhasználói viselkedés elemzőeszközök elvárt.

## <a name="choosing-user-ids"></a>Felhasználók kiválasztása

Felhasználói azonosítók a kell nyomon követheti, hogyan viselkednek a felhasználók idővel a felhasználói munkamenetek közötti megtartása. Nincsenek a különböző megközelítéseket megőrzése a azonosítója.

- A felhasználó a szolgáltatásban már meglévő definíciója.
- Szolgáltatásnak van egy böngészőben a hozzáférést, ha, továbbíthatja a böngészőben a cookie-k azonosítójú abban. Az azonosító az addig megmarad, mindaddig, amíg a cookie-t a felhasználó böngészőjében.
- Szükség esetén új Azonosítót minden egyes munkamenetnél is használhat, de a felhasználókra vonatkozó eredmények korlátozott lesz. Ha például nem tudja meg, hogyan változik a felhasználói viselkedés idővel.

Az azonosító egy GUID azonosítót vagy egy másik karakterláncra elég bonyolult minden felhasználóhoz egyedi azonosításához kell lennie. Például lehet a hosszú véletlen szám.

Ha az azonosító a felhasználó személyes azonosító információkat tartalmaz, azt nem megfelelő érték küldése az Application Insightsba, mint a felhasználói azonosítóját. Elküldheti az azonosítója, mint egy [hitelesített felhasználó azonosítója](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users), de nem felel meg a felhasználói azonosító követelménye használati forgatókönyvek.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET-alkalmazások: A felhasználói környezet egy ITelemetryInitializer beállítása

Hozzon létre egy telemetriainicializáló részletesen ismertetett módon [Itt](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer). A munkamenet-azonosító, a kérelmek telemetriai adatai keresztül adja át, és állítsa be a Context.User.Id és a Context.Session.Id.

Ebben a példában a felhasználói Azonosítót állít be egy azonosítóval, amely a munkamenet végeztével lejár. Ha lehetséges használjon egy felhasználói Azonosítót, amely továbbra is fennáll, munkamenetek között.

### <a name="telemetry-initializer"></a>Telemetriainicializáló

```csharp
using System;
using System.Web;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that sets the user ID.
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var ctx = HttpContext.Current;

        // If telemetry initializer is called as part of request execution and not from some async thread
        if (ctx != null)
        {
            var requestTelemetry = ctx.GetRequestTelemetry();
 
            // Set the user and session ids from requestTelemetry.Context.User.Id, which is populated in Application_PostAcquireRequestState in Global.asax.cs.
            if (requestTelemetry != null && !string.IsNullOrEmpty(requestTelemetry.Context.User.Id) &&
                (string.IsNullOrEmpty(telemetry.Context.User.Id) || string.IsNullOrEmpty(telemetry.Context.Session.Id)))
            {
                // Set the user id on the Application Insights telemetry item.
                telemetry.Context.User.Id = requestTelemetry.Context.User.Id;
 
                // Set the session id on the Application Insights telemetry item.
                telemetry.Context.Session.Id = requestTelemetry.Context.User.Id;
            }
        }
    }
  }
}
```

### <a name="globalasaxcs"></a>Global.asax.cs

```csharp
using System.Web;
using System.Web.Http;
using System.Web.Mvc;
using System.Web.Optimization;
using System.Web.Routing;

namespace MvcWebRole.Telemetry
{
    public class MvcApplication : HttpApplication
    {
        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            GlobalConfiguration.Configure(WebApiConfig.Register);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }
 
        protected void Application_PostAcquireRequestState()
        {
            var requestTelemetry = Context.GetRequestTelemetry();
 
            if (HttpContext.Current.Session != null && requestTelemetry != null && string.IsNullOrEmpty(requestTelemetry.Context.User.Id))
            {
                requestTelemetry.Context.User.Id = Session.SessionID;
            }
        }
    }
}
```

## <a name="next-steps"></a>További lépések

- Használati bővítsék küldésének megkezdése [egyéni események](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [Lapmegtekintések](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Ha már küldhet egyéni eseményeket vagy lapmegtekintéseket, Fedezze fel az megtudhatja, hogy a felhasználók használni a szolgáltatást a Használatelemzési eszközben.
    - [Használat – áttekintés](app-insights-usage-overview.md)
    - [Felhasználók, munkamenetek és események](app-insights-usage-segmentation.md)
    - [Tölcsérek](usage-funnels.md)
    - [Megőrzés](app-insights-usage-retention.md)
    - [Munkafüzetek](app-insights-usage-workbooks.md)
