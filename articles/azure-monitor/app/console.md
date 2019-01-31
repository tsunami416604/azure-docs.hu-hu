---
title: Az Azure Application Insights Konzolalkalmazással |} A Microsoft Docs
description: Figyeli a webalkalmazásokat a rendelkezésre állását, teljesítményét és használatát.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/30/2019
ms.reviewer: lmolkova
ms.author: mbullwin
ms.openlocfilehash: 602cd9696271931babad9aa962638c5b646c80ac
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296766"
---
# <a name="application-insights-for-net-console-applications"></a>.NET-hez készült Application Insights konzolon alkalmazások
[Az Application Insights](../../azure-monitor/app/app-insights-overview.md) figyelni, webalkalmazása rendelkezésre állását, teljesítményét és használatát.

Az előfizetés kell [Microsoft Azure](https://azure.com). Jelentkezzen be Microsoft-fiókkal, amely Windows, Xbox Live, vagy a Microsoft más felhőszolgáltatásaival előfordulhat, hogy rendelkezik. A csapata szervezeti előfizetéssel az Azure-bA lehet: kérje meg a tulajdonosát, hogy vegye fel a Microsoft-fiók használatával.

## <a name="getting-started"></a>Első lépések

* [Hozzon létre egy Application Insights-erőforrást](../../azure-monitor/app/create-new-resource.md ) az [Azure Portalon](https://portal.azure.com). Az alkalmazás típusánál válassza **általános**.
* Végezze el a kialakítási kulcs másolását. Keresse meg a kulcsot a **Essentials** legördülő az új erőforrás létrehozása. 
* Telepítse a legújabb [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) csomagot.
* Állítsa be a rendszerállapotkulcsot a kódban semmilyen telemetriai adatot követése előtt (vagy állítani az APPINSIGHTS_INSTRUMENTATIONKEY környezeti változó beállítása). Ezt követően meg kell tudni manuálisan nyomon követheti a telemetriai adatokat, és nézze meg az Azure Portalon

```csharp
TelemetryConfiguration.Active.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient();
telemetryClient.TrackTrace("Hello World!");
```

* Legújabb verziójának telepítése [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) package - automatikusan azt követi nyomon, HTTP-, SQL, vagy néhány egyéb külső függőségi hívások.

Előfordulhat, hogy inicializálni és az Application Insights beállítása a kódból vagy `ApplicationInsights.config` fájlt. Ellenőrizze, hogy az inicializálás történik, amilyen hamar csak lehet. 

> [!NOTE]
> Hivatkozó utasításokat **ApplicationInsights.config** csak a .NET-keretrendszer célozza, és nem vonatkoznak a .NET Core-alkalmazások alkalmazások vonatkoznak.

### <a name="using-config-file"></a>A konfigurációs fájl használatával

Alapértelmezés szerint az Application Insights SDK keres `ApplicationInsights.config` fájlt a munkakönyvtárban amikor `TelemetryConfiguration` létrehozása folyamatban van

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

A konfigurációs fájl elérési útját is megadható.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

További információkért lásd: [konfigurációs fájl ismertetése](configuration-with-applicationinsights-config.md).

Előfordulhat, hogy kap egy teljes példa a konfigurációs fájl legfrissebb verziójának telepítésével [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) csomagot. Íme a **minimális** függőségi gyűjteményt, amely megegyezik a példakód konfigurációját.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>A kód telemetriai adatok gyűjtésének konfigurálása

* Alkalmazás indítási során létrehozása és konfigurálása `DependencyTrackingTelemetryModule` példány -, egyedülálló kell lennie és meg kell őrizni az alkalmazás élettartamának.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Közös telemetriai inicializálók hozzáadása

```csharp
// stamps telemetry with correlation identifiers
TelemetryConfiguration.Active.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());

// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
TelemetryConfiguration.Active.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

* .NET-keretrendszer Windows-alkalmazások esetén is telepítheti és teljesítményszámláló gyűjtő modul inicializálása, amint [Itt](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)

#### <a name="full-example"></a>Teljes példa

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.Active;

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient();
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking so wait a bit
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>További lépések
* [Függőségek figyelése](../../azure-monitor/app/asp-net-dependencies.md) megtekintheti, ha a REST, SQL és más külső erőforrások okoznak lassulást.
* [Az API-val](../../azure-monitor/app/api-custom-events-metrics.md) küldése a saját események és mérőszámok az alkalmazás teljesítményének és használatának részletesebb megjelenítéséhez.
