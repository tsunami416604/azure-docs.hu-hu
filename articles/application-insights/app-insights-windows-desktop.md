---
title: Windowsos asztali alkalmazások használatának és teljesítményének figyelése
description: A windowsos asztali alkalmazások használatát és teljesítményét az Application Insights segítségével elemezheti.
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
ms.date: 05/15/2018
ms.author: mbullwin
ms.openlocfilehash: ada38fc26f2fce9251ae648302733b04fe4c82ec
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195828"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Klasszikus windowsos asztali alkalmazások használatának és teljesítményének figyelése

A helyszínen, az Azure-ban és más felhőben üzemeltetett alkalmazások is kihasználhatják az Application Insights előnyeit. Az egyetlen korlát, hogy [engedélyezni kell a kommunikációt](app-insights-ip-addresses.md) az Application Insights szolgáltatással. Az univerzális Windows-platformra (UWP) épülő alkalmazásokhoz a [Visual Studio App Center](app-insights-mobile-center-quickstart.md) használatát javasoljuk.

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Klasszikus Windows-alkalmazásból származó telemetriai adatok küldése az Application Insightsba
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

