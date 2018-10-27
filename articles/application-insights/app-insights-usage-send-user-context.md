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
ms.date: 08/02/2017
ms.reviewer: abgreg;mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: bfb04f596a61ff79c75cd38473c9480a29b0e6c4
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139890"
---
#  <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Az Azure Application Insights használati bővítsék azonosítók felhasználói környezet küldése

## <a name="tracking-users"></a>Felhasználók nyomon követése

Az Application Insights lehetővé teszi, hogy figyelését és nyomon követheti a felhasználók a termék használati eszközöket keresztül: 
* [Felhasználók, munkamenetek, események](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
* [Tölcsérek](https://docs.microsoft.com/azure/application-insights/usage-funnels)
* [Megőrzés](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention)
* Kohorszok
* [Munkafüzetek](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Nyomon követheti, mi a felhasználó időbeli végrehajtja, az Application Insights-azonosító az egyes felhasználók vagy a munkamenet van szüksége. Minden egyéni esemény vagy oldal nézet vegye fel a következő azonosítóval.
- Felhasználók, a tölcsérek, a megőrzési és a Kohorszok: közé tartozik a felhasználói azonosítóját.
- Munkamenetek: Tartalmazza a munkamenet-azonosítót.

Ha az alkalmazás integrálva van a [JavaScript SDK](https://docs.microsoft.com/azure/application-insights/app-insights-javascript#set-up-application-insights-for-your-web-page), felhasználói azonosító automatikusan követi nyomon.

## <a name="choosing-user-ids"></a>Felhasználók kiválasztása

Felhasználói azonosítók a kell nyomon követheti, hogyan viselkednek a felhasználók idővel a felhasználói munkamenetek közötti megtartása. Nincsenek a különböző megközelítéseket megőrzése a azonosítója.
- A felhasználó a szolgáltatásban már meglévő definíciója.
- Szolgáltatásnak van egy böngészőben a hozzáférést, ha, továbbíthatja a böngészőben a cookie-k azonosítójú abban. Az azonosító az addig megmarad, mindaddig, amíg a cookie-t a felhasználó böngészőjében.
- Szükség esetén új Azonosítót minden egyes munkamenetnél is használhat, de a felhasználókra vonatkozó eredmények korlátozott lesz. Ha például nem tudja meg, hogyan változik a felhasználói viselkedés idővel.

Az azonosító egy GUID azonosítót vagy egy másik karakterláncra elég bonyolult minden felhasználóhoz egyedi azonosításához kell lennie. Például lehet a hosszú véletlen szám.

Ha az azonosító a felhasználó személyes azonosító információkat tartalmaz, azt nem megfelelő érték küldése az Application Insightsba, mint a felhasználói azonosítóját. Elküldheti az azonosítója, mint egy [hitelesített felhasználó azonosítója](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users), de nem felel meg a felhasználói azonosító követelménye használati forgatókönyvek.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET-alkalmazások: a felhasználói környezet egy ITelemetryInitializer beállítása

Hozzon létre egy telemetriainicializáló részletesen ismertetett módon [Itt](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer), és állítsa be a Context.User.Id és a Context.Session.Id.

Ebben a példában a felhasználói Azonosítót állít be egy azonosítóval, amely a munkamenet végeztével lejár. Ha lehetséges használjon egy felhasználói Azonosítót, amely továbbra is fennáll, munkamenetek között.

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
- Használati bővítsék küldésének megkezdése [egyéni események](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [Lapmegtekintések](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Ha már küldhet egyéni eseményeket vagy lapmegtekintéseket, Fedezze fel az megtudhatja, hogy a felhasználók használni a szolgáltatást a Használatelemzési eszközben.
    * [Használat – áttekintés](app-insights-usage-overview.md)
    * [Felhasználók, munkamenetek és események](app-insights-usage-segmentation.md)
    * [Tölcsérek](usage-funnels.md)
    * [Megőrzés](app-insights-usage-retention.md)
    * [Munkafüzetek](app-insights-usage-workbooks.md)
