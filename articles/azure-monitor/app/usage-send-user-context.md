---
title: A tevékenység nyomon követésére szolgáló felhasználói környezeti azonosítók – Azure Application Insights
description: Nyomon követheti, hogy a felhasználók hogyan haladnak át a szolgáltatáson. ehhez Rendeljen hozzájuk egy egyedi, állandó azonosító karakterláncot a Application Insights.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.openlocfilehash: ecdcc8a84cdccb05ec514003d63f808583d719c9
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797677"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Felhasználói környezeti azonosítók küldése a használati élmények engedélyezéséhez az Azure-ban Application Insights

## <a name="tracking-users"></a>Felhasználók nyomon követése

Application Insights lehetővé teszi a felhasználók figyelését és nyomon követését a termék használati eszközeinek használatával:

- [Felhasználók, munkamenetek, események](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
- [Tölcsérek](https://docs.microsoft.com/azure/application-insights/usage-funnels)
- [Adatmegőrzés](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention) Kohorszok
- [Munkafüzetek](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview)

A felhasználó által az idő múlásával nyomon követhető, Application Insights minden felhasználóhoz vagy munkamenethez szüksége van egy AZONOSÍTÓra. A következő azonosítókat adja meg minden egyéni esemény vagy lap nézetben.

- Felhasználók, tölcsérek, megőrzés és kohorszok: felhasználói azonosító belefoglalása.
- Munkamenetek: munkamenet-azonosító belefoglalása.

> [!NOTE]
> Ez egy speciális cikk, amely ismerteti a felhasználói tevékenység nyomon követésének manuális lépéseit Application Insights. Számos webalkalmazással **ezek a lépések nem szükségesek**, mivel az alapértelmezett kiszolgálóoldali SDK-k az [ügyfél/böngésző-oldal JavaScript SDK](../../azure-monitor/app/website-monitoring.md )-val együtt általában elegendőek a felhasználói tevékenységek automatikus követéséhez. Ha még nem konfigurálta az [ügyféloldali figyelést](../../azure-monitor/app/website-monitoring.md ) a kiszolgálóoldali SDK mellett, először is ellenőrizze, hogy a felhasználói viselkedés elemzési eszközei a várt módon működnek-e.

## <a name="choosing-user-ids"></a>Felhasználói azonosítók kiválasztása

A felhasználói azonosítóknak meg kell őrizniük a felhasználói munkamenetek között, hogy nyomon kövessék, hogyan viselkedjenek a felhasználók a Az azonosító megtartásának számos módja van.

- A szolgáltatásban már meglévő felhasználó definíciója.
- Ha a szolgáltatásnak van hozzáférése egy böngészőhöz, akkor a böngészőben egy olyan cookie-t adhat át, amely egy AZONOSÍTÓval rendelkezik. Az azonosító addig marad, amíg a cookie a felhasználó böngészőjében marad.
- Ha szükséges, használhatja az egyes munkamenetek új AZONOSÍTÓját, de a felhasználókra vonatkozó eredmények korlátozottak lesznek. Például nem fogja tudni megtekinteni, hogyan változnak a felhasználók viselkedése az idő múlásával.

Az AZONOSÍTÓnak GUID-ként vagy más karakterlánc-komplexnek kell lennie ahhoz, hogy az egyes felhasználók egyedileg azonosíthatók legyenek. Ez lehet például egy hosszú véletlenszerű szám.

Ha az azonosító a felhasználóval kapcsolatos személyazonosításra alkalmas adatokat tartalmaz, nem megfelelő érték, amelyet felhasználói AZONOSÍTÓként Application Insights küldeni. Ezt az azonosítót [hitelesített felhasználói azonosítóként](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users)is elküldheti, de nem teljesíti a használati forgatókönyvek felhasználói azonosítóra vonatkozó követelményeit.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET alkalmazások: a felhasználói környezet beállítása egy ITelemetryInitializer

Hozzon létre egy telemetria inicializáló a részletek [itt](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#addmodify-properties-itelemetryinitializer)leírtak szerint. Adja át a munkamenet-azonosítót a kérelem telemetria, és állítsa be a Context.User.Id és a Context.Session.Id.

Ez a példa a felhasználói azonosítót egy olyan azonosítóra állítja be, amely a munkamenet után lejár. Ha lehetséges, használjon olyan felhasználói azonosítót, amely a munkamenetek között megmarad.

### <a name="telemetry-initializer"></a>Telemetria inicializáló

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

## <a name="next-steps"></a>Következő lépések

- A használati tapasztalatok engedélyezéséhez kezdjen el [Egyéni eseményeket](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [oldalletöltések](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)küldését.
- Ha már elküldte az egyéni eseményeket vagy a lapok nézeteit, tekintse meg a használati eszközöket, amelyekkel megismerheti, hogy a felhasználók miként használják a szolgáltatást.
    - [A használat áttekintése](usage-overview.md)
    - [Felhasználók, munkamenetek és események](usage-segmentation.md)
    - [Tölcsérek](usage-funnels.md)
    - [Megőrzés](usage-retention.md)
    - [Munkafüzetek](../../azure-monitor/platform/workbooks-overview.md)
