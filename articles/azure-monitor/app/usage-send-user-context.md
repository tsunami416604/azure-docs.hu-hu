---
title: Felhasználói környezetazonosítók a tevékenységek nyomon követéséhez - Azure Application Insights
description: Nyomon követheti, hogyan mozoghatnak a felhasználók a szolgáltatásban azáltal, hogy mindegyikhez egyedi, állandó azonosító karakterláncot rendelnek az Application Insightsban.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.openlocfilehash: beb5a0f7ad3733aaf12b0880af4fba23a705a7e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670933"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Felhasználói környezetazonosítók küldése az Azure Application Insights használati élményének engedélyezéséhez

## <a name="tracking-users"></a>Felhasználók nyomon követése

Az Application Insights lehetővé teszi a felhasználók figyelését és nyomon követését termékhasználati eszközök segítségével:

- [Felhasználók, munkamenetek, események](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
- [Tölcsérek](https://docs.microsoft.com/azure/application-insights/usage-funnels)
- [Megőrzés](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention) Kohorszok
- [Munkafüzetek](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Annak érdekében, hogy nyomon követhesse, hogy a felhasználó mit csinál az idő múlásával, az Application Insights-nak minden felhasználóhoz vagy munkamenethez azonosítóra van szüksége. Minden egyéni eseményben vagy oldalnézetben a következő azonosítókat kell megadnia.

- Felhasználók, csatornák, adatmegőrzés és kohorszok: Adja meg a felhasználói azonosítót.
- Munkamenetek: Munkamenet-azonosító val együtt.

> [!NOTE]
> Ez egy speciális cikk, amely felvázolja a felhasználói tevékenység nyomon követésének manuális lépéseit az Application Insights segítségével. Sok webalkalmazás esetében ezekre a **lépésekre nincs szükség,** mivel az [ügyfél- és böngészőoldali JavaScript SDK-kkal](../../azure-monitor/app/website-monitoring.md )együtt az alapértelmezett kiszolgálóoldali SDK-k gyakran elegendőek a felhasználói tevékenység automatikus nyomon követéséhez. Ha a kiszolgálóoldali SDK mellett még nem konfigurálta az [ügyféloldali figyelést,](../../azure-monitor/app/website-monitoring.md ) először ezt tegye meg, és ellenőrizze, hogy a felhasználói viselkedéselemző eszközök a várt módon teljesítenek-e.

## <a name="choosing-user-ids"></a>Felhasználói azonosítók kiválasztása

A felhasználói azonosítóknak meg kell maradniuk a felhasználói munkamenetek között, hogy nyomon követhessék, hogyan viselkednek a felhasználók az idő múlásával. Az azonosító megőrzéséhez különböző megközelítések léteznek.

- A szolgáltatásban már meglévő felhasználó definíciója.
- Ha a szolgáltatás hozzáfér egy böngészőhöz, átadhat a böngészőnek egy cookie-t, amelyben azonosító van. Az azonosító mindaddig megmarad, amíg a cookie a felhasználó böngészőjében marad.
- Ha szükséges, minden munkamenetben használhat új azonosítót, de a felhasználókra vonatkozó eredmények korlátozottak lesznek. Például nem fogja látni, hogyan változik a felhasználó viselkedése az idő múlásával.

Az azonosítónak guid vagy más karakterlánc-komplexumnak kell lennie ahhoz, hogy minden felhasználóegyedileg azonosíthassa. Például lehet, hogy egy hosszú véletlen szám.

Ha az azonosító személyes azonosítási adatokat tartalmaz a felhasználóról, nem megfelelő érték az Application Insights felhasználói azonosítóként történő elküldéséhez. Az ilyen azonosítót [elküldheti hitelesített felhasználói azonosítóként,](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users)de nem felel meg a használati forgatókönyvek felhasználói azonosítókövetelményének.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>alkalmazások ASP.NET: A felhasználói környezet beállítása egy ITelemetryInitializer

Hozzon létre egy telemetriai inicializálót, ahogy [az itt](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#addmodify-properties-itelemetryinitializer)részletesen ismertetjük. Adja át a munkamenet-azonosítót a kérelem telemetrián keresztül, és állítsa be a Context.User.Id és a Context.Session.Id.

Ez a példa a felhasználói azonosítót egy olyan azonosítóra állítja be, amely a munkamenet után jár le. Ha lehetséges, használjon olyan felhasználói azonosítót, amely a munkamenetek között megmarad.

### <a name="telemetry-initializer"></a>Telemetriai inicializáló

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

- A használati élmény engedélyezéséhez kezdje el elküldeni [az egyéni eseményeket](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [oldalnézeteket.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)
- Ha már küld egyéni eseményeket vagy oldalnézeteket, fedezze fel a Használati eszközöket, hogy megtudja, hogyan használják a felhasználók a szolgáltatást.
    - [Használat – áttekintés](usage-overview.md)
    - [Felhasználók, munkamenetek és események](usage-segmentation.md)
    - [Tölcsérek](usage-funnels.md)
    - [Megőrzés](usage-retention.md)
    - [Munkafüzetek](../../azure-monitor/app/usage-workbooks.md)
