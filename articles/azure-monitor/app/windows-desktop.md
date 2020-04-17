---
title: Windowsos asztali alkalmazások használatának és teljesítményének figyelése
description: A windowsos asztali alkalmazások használatát és teljesítményét az Application Insights segítségével elemezheti.
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: eb9e0fc480098478a3a68265ac85e0d5450e27fe
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537389"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Klasszikus windowsos asztali alkalmazások használatának és teljesítményének figyelése

A helyszínen, az Azure-ban és más felhőben üzemeltetett alkalmazások is kihasználhatják az Application Insights előnyeit. Az egyetlen korlát, hogy [engedélyezni kell a kommunikációt](../../azure-monitor/app/ip-addresses.md) az Application Insights szolgáltatással. Az univerzális Windows-platformra (UWP) épülő alkalmazásokhoz a [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md) használatát javasoljuk.

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Klasszikus Windows-alkalmazásból származó telemetriai adatok küldése az Application Insightsba
1. [Hozzon létre egy Application Insights-erőforrást](../../azure-monitor/app/create-new-resource.md ) az [Azure Portalon](https://portal.azure.com). Az alkalmazás típusánál válassza az ASP.NET-alkalmazás lehetőséget.
2. Végezze el a kialakítási kulcs másolását. A kulcs az imént létrehozott új erőforrás Alapvető szolgáltatások legördülő menüjében található. 
3. A Visual Studióban szerkessze az alkalmazási projekt NuGet-csomagjait, és vegye fel a Microsoft.ApplicationInsights.WindowsServer csomagot. (Vagy válassza a Microsoft.ApplicationInsights csomagot, ha csak az API-ra van szüksége a normál telemetriai adatgyűjtő modulok nélkül.)
4. Állítsa be a kialakítási kulcsot a kódban:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *az Ön kulcsa* `";`
   
    vagy az ApplicationInsights.config fájlban (ha telepítette valamelyik normál telemetriai csomagot):
   
    `<InstrumentationKey>`*a kulcs*`</InstrumentationKey>` 
   
    Az ApplicationInsights.config használatakor győződjön meg arról, hogy annak tulajdonságait a következőre állította a Megoldáskezelőben: **Build Action = Content, Copy to Output Directory = Copy**.
5. [Az API-val](../../azure-monitor/app/api-custom-events-metrics.md) telemetriai adatokat küldhet.
6. Futtassa az alkalmazást, és tekintse meg a telemetriai adatokat az Azure Portalon létrehozott erőforrásban.

## <a name="example-code"></a><a name="telemetry"></a>Mintakód
```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
        {
            e.Cancel = true;

            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="override-storage-of-computer-name"></a>A számítógépnév tárolásának felülbírálása

Alapértelmezés szerint ez az SDK összegyűjti és tárolja a rendszer telemetriáját kibocsátó rendszer nevét. A gyűjtemény felülbírálásához telemetriai inicializálót kell használnia:

**Írjon egyéni TelemettryInitializer az alábbiak szerint.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here, you can pass an empty string if needed.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```
Az inicializáló t `Program.cs` `Main()` az alábbi módszerrel hozza létre a műszerezési kulcsot:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
        }
```

## <a name="next-steps"></a>További lépések
* [Irányítópult létrehozása](../../azure-monitor/app/overview-dashboard.md)
* [Diagnosztikai keresés](../../azure-monitor/app/diagnostic-search.md)
* [Metrikák böngészése](../../azure-monitor/platform/metrics-charts.md)
* [Analytics-lekérdezések](../../azure-monitor/app/analytics.md)

