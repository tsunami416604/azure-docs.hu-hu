---
title: "Küldése a felhasználói környezet azonosítók használatának engedélyezése az Azure Application Insights észlel |} Microsoft Docs"
description: "Nyomon követheti, hogyan felhasználók halad át a szolgáltatás egy egyedi, állandó azonosító karakterláncot az Application Insightsban rendel hozzájuk."
services: application-insights
documentationcenter: 
author: abgreg
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: csharp
ms.topic: article
ms.date: 08/02/2017
ms.author: mbullwin
ms.openlocfilehash: fe4481cf851fc021b3073a6d9d6254f546218785
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
#  <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Felhasználói környezet használata felhasználói élményt Azure Application Insights az azonosítók küldése

## <a name="tracking-users"></a>Felhasználók nyomon követése

Az Application Insights figyeléséhez és nyomon követéséhez a felhasználók termékkel használati eszközöket keresztül teszi lehetővé: 
* [Felhasználók, munkamenetek, események](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
* [Tölcsérek](https://docs.microsoft.com/azure/application-insights/usage-funnels)
* [Megőrzés](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention)
* Cohorts
* [Munkafüzetek](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Nyomon követheti a felhasználók funkciója adott idő alatt, az Application Insights Azonosítót kell minden egyes felhasználó vagy a munkamenet. Tartalmazza a következő azonosítót minden egyéni esemény vagy a lap nézetben.
- Felhasználók, tölcsérek, megőrzés és Cohorts: közé tartozik a felhasználói azonosítóját.
- Munkamenetek: Tartalmazza a munkamenet-azonosítót.

Ha az alkalmazás integrálva van a [JavaScript SDK](https://docs.microsoft.com/azure/application-insights/app-insights-javascript#set-up-application-insights-for-your-web-page), a felhasználói azonosító automatikusan rögzíti.

## <a name="choosing-user-ids"></a>Felhasználói azonosítók kiválasztása

Felhasználói azonosítók kell követni a felhasználók hogyan szeretnék kezelni idővel a felhasználói munkamenetek után is. Nincsenek a különböző módszerek megőrzése a azonosítóját.
- Egy olyan felhasználó, a szolgáltatás már rendelkezik definíciója.
- Ha hozzáfér a szolgáltatás egy böngészőt, azt is adja át a böngésző Azonosítóval rendelkező cookie azt. Az azonosító mindaddig, amíg a cookie-k a felhasználó böngészőben a megmaradnak.
- Szükség esetén új Azonosítót minden munkamenet is használhat, de a felhasználókra vonatkozó eredmények korlátozott lesz. Például nem fogja tudni idővel hogyan változik a felhasználói viselkedés talál.

Az azonosító egy GUID azonosítót vagy egy másik karakterlánc elég bonyolult minden felhasználóhoz egyedi módon azonosítani kell lennie. Például annak oka lehet egy hosszú véletlenszerű számot.

Az azonosító a felhasználó személyes azonosításra alkalmas információkat tartalmaz, ha nincs a megfelelő érték küldhet az Application Insights részére, a felhasználói azonosítóját. Küldhet az azonosítója, mint egy [hitelesített felhasználói azonosító](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users), de nem felel meg a felhasználói azonosító követelménye használati forgatókönyvek.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET-alkalmazások: a felhasználói környezet egy ITelemetryInitializer beállítása

Hozzon létre egy telemetriai inicializáló, ennek részletes ismertetését lásd [Itt](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer), és állítsa be a Context.User.Id és a Context.Session.Id.

Ebben a példában a felhasználói Azonosítóját, amely a munkamenet végeztével lejár azonosítót állítja be. Ha lehetséges használjon egy felhasználói Azonosítót, amely továbbra is fennáll-munkamenetek között.

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
            // For a full experience, track each user across sessions. For an incomplete view of user 
            // behavior within a session, store user ID on the HttpContext Session.
            // Set the user ID if we haven't done so yet.
            if (HttpContext.Current.Session["UserId"] == null)
            {
                HttpContext.Current.Session["UserId"] = Guid.NewGuid();
            }

            // Set the user id on the Application Insights telemetry item.
            telemetry.Context.User.Id = (string)HttpContext.Current.Session["UserId"];

            // Set the session id on the Application Insights telemetry item.
            telemetry.Context.Session.Id = HttpContext.Current.Session.SessionID;
        }
      }
    }
```

## <a name="next-steps"></a>További lépések
- Ahhoz, hogy a használati tapasztalatok, küldésének megkezdése [egyéni események](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [lapmegtekintés](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Ha egyéni események vagy Lapmegtekintések már küld, megismerkedhet a használati eszközök további, a szolgáltatás használatát a felhasználók.
    * [Használat – áttekintés](app-insights-usage-overview.md)
    * [Felhasználók, a munkamenetek és az események](app-insights-usage-segmentation.md)
    * [Tölcsérek](usage-funnels.md)
    * [Megőrzés](app-insights-usage-retention.md)
    * [Munkafüzetek](app-insights-usage-workbooks.md)
