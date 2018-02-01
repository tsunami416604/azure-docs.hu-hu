---
title: "Windowsos asztali alkalmazások használatának és teljesítményének figyelése"
description: "Windowsos asztali alkalmazások használatát és teljesítményét elemezheti a HockeyApp és az Application Insights segítségével."
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2016
ms.author: mbullwin
ms.openlocfilehash: 3b91996c12a27847befcacf6daf14457a0e066aa
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>Windowsos asztali alkalmazások használatának és teljesítményének figyelése


Az [Azure Application Insights](app-insights-overview.md) és a [HockeyApp](https://hockeyapp.net) a telepített alkalmazások használatának és teljesítményének figyelését teszi lehetővé.

> [!IMPORTANT]
> Az asztali és eszközalkalmazások terjesztésére és figyelésére a [HockeyApp](https://hockeyapp.net) szolgáltatást ajánljuk. A HockeyApp segítségével a terjesztést, a működés közbeni tesztelést és a felhasználói visszajelzéseket felügyelheti, valamint a használatot és az összeomlási jelentéseket figyelheti. Ezen túlmenően [telemetriai adatokat exportálhat és kérdezhet le az Analytics használatával](app-insights-hockeyapp-bridge-app.md).
> 
> Bár küldhetők asztali alkalmazásból származó telemetriai adatok az Application Insightsba, ez elsősorban hibakeresésre és kísérletezésre szolgál.
> 
> 

## <a name="to-send-telemetry-to-application-insights-from-a-windows-application"></a>Windows-alkalmazásból származó telemetriai adatok küldése az Application Insightsba
1. [Hozzon létre egy Application Insights-erőforrást](app-insights-create-new-resource.md) az [Azure Portalon](https://portal.azure.com). Az alkalmazás típusánál válassza az ASP.NET-alkalmazás lehetőséget.
2. Végezze el a kialakítási kulcs másolását. A kulcs az imént létrehozott új erőforrás Alapvető szolgáltatások legördülő menüjében található. 
3. A Visual Studióban szerkessze az alkalmazási projekt NuGet-csomagjait, és vegye fel a Microsoft.ApplicationInsights.WindowsServer csomagot. (Vagy válassza a Microsoft.ApplicationInsights csomagot, ha csak az API-ra van szüksége a normál telemetriai adatgyűjtő modulok nélkül.)
4. Állítsa be a kialakítási kulcsot a kódban:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "`*az Ön kulcsa*`";` 
   
    vagy az ApplicationInsights.config fájlban (ha telepítette valamelyik normál telemetriai csomagot):
   
    `<InstrumentationKey>`*az Ön kulcsa*`</InstrumentationKey>` 
   
    Az ApplicationInsights.config használatakor győződjön meg arról, hogy annak tulajdonságait a következőre állította a Megoldáskezelőben: **Build Action = Content, Copy to Output Directory = Copy**.
5. [Az API-val](app-insights-api-custom-events-metrics.md) telemetriai adatokat küldhet.
6. Az alkalmazás futtatása után az Azure Portalon tekintheti meg a létrehozott erőforrás telemetriai adatait.

## <a name="telemetry"></a>Mintakód
```csharp

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>További lépések
* [Irányítópult létrehozása](app-insights-dashboards.md)
* [Diagnosztikai keresés](app-insights-diagnostic-search.md)
* [Metrikák böngészése](app-insights-metrics-explorer.md)
* [Analytics-lekérdezések](app-insights-analytics.md)

