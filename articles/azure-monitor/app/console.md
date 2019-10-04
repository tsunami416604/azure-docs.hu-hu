---
title: Azure-Application Insights a konzol alkalmazásaihoz | Microsoft Docs
description: Webes alkalmazások figyelése a rendelkezésre állás, a teljesítmény és a használat érdekében.
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
ms.openlocfilehash: b6ecf1e9cece51635afc0bf0f8025b6e117438ee
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169451"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights a .NET-konzol alkalmazásaihoz

[Application Insights](../../azure-monitor/app/app-insights-overview.md) lehetővé teszi a webalkalmazások figyelését a rendelkezésre állás, a teljesítmény és a használat érdekében.

[Microsoft Azure](https://azure.com)-előfizetésre van szüksége. Jelentkezzen be egy Microsoft-fiókval, amely lehet Windows, Xbox Live vagy más Microsoft Cloud Services. Előfordulhat, hogy a csapata szervezeti előfizetéssel rendelkezik az Azure-hoz: kérje meg a tulajdonost, hogy vegye fel Önt a Microsoft-fiók használatával.

> [!NOTE]
> Létezik egy [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) nevű új Beta Application Insights SDK, amely lehetővé teszi a Application Insights használatát bármely konzolos alkalmazáshoz. Ezt a csomagot és a hozzá tartozó utasításokat ajánlott [használni.](../../azure-monitor/app/worker-service.md) Ez a csomag [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)célja, és így a .net Core 2,0-as vagy újabb verzióban, illetve a .NET-keretrendszer 4.7.2 vagy újabb verziókban használható.
Ez a dokumentum az új csomag stabil verziójának kiadása után elavult lesz.

## <a name="getting-started"></a>Első lépések

* [Hozzon létre egy Application Insights-erőforrást](../../azure-monitor/app/create-new-resource.md) az [Azure Portalon](https://portal.azure.com). Az alkalmazás típusa mezőben válassza az **általános**lehetőséget.
* Végezze el a kialakítási kulcs másolását. Keresse meg a kulcsot a létrehozott új erőforrás **Essentials** legördülő menüjében. 
* Telepítse a legújabb [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) csomagot.
* Adja meg a kialakítási kulcsot a kódban a telemetria követése előtt (vagy állítsa be a APPINSIGHTS_INSTRUMENTATIONKEY környezeti változót). Ezután manuálisan nyomon követheti a telemetria, és megtekintheti a Azure Portal

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

* A [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) Package legújabb verziójának telepítése – automatikusan nyomon követi a http, az SQL vagy más külső függőségi hívásokat.

A kód vagy a fájl használatával `ApplicationInsights.config` Application Insights inicializálható és konfigurálható. Győződjön meg arról, hogy az inicializálás a lehető leghamarabb megtörténik. 

> [!NOTE]
> A **ApplicationInsights. config fájlra** hivatkozó utasítások csak a .NET-keretrendszert célzó alkalmazásokra vonatkoznak, és a .net Core-alkalmazásokra nem érvényesek.

### <a name="using-config-file"></a>Konfigurációs fájl használata
Alapértelmezés szerint a Application Insights SDK a `ApplicationInsights.config` `TelemetryConfiguration` munkakönyvtárban keresi a fájlt a létrehozáskor

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

A konfigurációs fájl elérési útját is megadhatja.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

További információ: [konfigurációs fájl leírása](configuration-with-applicationinsights-config.md).

A konfigurációs fájlhoz a [Microsoft. ApplicationInsights. windowsserver](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) csomag legújabb verziójának telepítésével kaphat teljes példát. Itt látható a függőségi gyűjtemény **minimális** konfigurációja, amely egyenértékű a kóddal.

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

### <a name="configuring-telemetry-collection-from-code"></a>Telemetria-gyűjtemény konfigurálása a kódból
> [!NOTE]
> A konfigurációs fájl olvasása nem támogatott a .NET Core-ban. Érdemes lehet Application Insights SDK-t használni [a ASP.net Core](../../azure-monitor/app/asp-net-core.md)

* Az alkalmazás indítási létrehozása és konfigurálása `DependencyTrackingTelemetryModule` során a példánynak egyedinek kell lennie, és meg kell őrizni az alkalmazás élettartamát.

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

* Általános telemetria-inicializálók hozzáadása

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Ha egyszerű `TelemetryConfiguration()` konstruktorral hozta létre a konfigurációt, engedélyeznie kell a korrelációs támogatást is. **Nem szükséges** , ha a konfigurációt beolvassa a fájlból `TelemetryConfiguration.Active`, a használt `TelemetryConfiguration.CreateDefault()` vagy a elemet.

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* Az [itt](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/) leírtak szerint érdemes lehet a teljesítményszámláló-gyűjtő modult is telepíteni és inicializálni.


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
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
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
* A [függőségek figyelésével](../../azure-monitor/app/asp-net-dependencies.md) ellenőrizheti, hogy a REST, az SQL vagy más külső erőforrások lassulnak-e.
* [Az API használatával](../../azure-monitor/app/api-custom-events-metrics.md) saját eseményeket és mérőszámokat küldhet az alkalmazás teljesítményének és használatának részletesebb áttekintéséhez.
