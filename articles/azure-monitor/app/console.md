---
title: Azure Application Insights for Console Applications | Microsoft dokumentumok
description: Figyelje a webes alkalmazások elérhetőségét, teljesítményét és használatát.
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: lmolkova
ms.openlocfilehash: baaea0f8055eeff0314fcf5fde00729ea8091d12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655429"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights .NET konzolalkalmazásokhoz

[Az Application Insights](../../azure-monitor/app/app-insights-overview.md) segítségével figyelheti a webes alkalmazást a rendelkezésre állás, a teljesítmény és a használat.

Előfizetésre van szüksége a [Microsoft Azure-ral.](https://azure.com) Jelentkezzen be egy Microsoft-fiókkal, amellyel Windows, Xbox Live vagy más Microsoft felhőszolgáltatásokhoz rendelkezhet. Előfordulhat, hogy a csapata rendelkezik egy szervezeti előfizetéssel az Azure-ra: kérje meg a tulajdonost, hogy adja hozzá a Microsoft-fiókjához.

> [!NOTE]
> Van egy új Application Insights SDK nevű [Microsoft.ApplicationInsights.WorkerService,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) amely segítségével az Application Insights bármely konzolalkalmazások. Javasoljuk, hogy használja ezt a csomagot, és a kapcsolódó utasításokat [innen](../../azure-monitor/app/worker-service.md). Ez a [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)csomag a .

## <a name="getting-started"></a>Első lépések

* [Hozzon létre egy Application Insights-erőforrást](../../azure-monitor/app/create-new-resource.md) az [Azure Portalon](https://portal.azure.com). Az alkalmazástípushoz válassza az **Általános**lehetőséget.
* Végezze el a kialakítási kulcs másolását. Keresse meg a kulcsot az új onnan létrehozott új erőforrás **Essentials** legördülő menüjében.
* Telepítse a legújabb [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) csomagot.
* Állítsa be a műszerezési kulcsot a kódban, mielőtt bármilyen telemetriai (vagy APPINSIGHTS_INSTRUMENTATIONKEY környezeti változó beállítása) követése előtt. Ezt követően manuálisan nyomon követheti a telemetriai adatokat, és megtekintheti azt az Azure Portalon

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> Telemetriai adatok nem küldi azonnal. Telemetriai elemek kötegelt és küldött az ApplicationInsights SDK. A konzolos alkalmazásokban, amelyek `Track()` közvetlenül a hívási módszerek `Flush()` `Sleep` után lépnek ki, a telemetriai adatok csak akkor küldhetők el, ha az alkalmazás kilépése előtt történik, ahogy az a cikk későbbi részében bemutatott [teljes példában](#full-example) látható.


* Telepítse a [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) csomag legújabb verzióját – automatikusan nyomon követi a HTTP- vagy SQL-hívásokat vagy más külső függőségi hívásokat.

Inicializálhatja és konfigurálhatja az `ApplicationInsights.config` Application Insightsot a kódból vagy a fájl használatával. Győződjön meg róla, hogy az inicializálás a lehető leghamarabb megtörténik. 

> [!NOTE]
> Az **ApplicationInsights.config** fájlra vonatkozó utasítások csak a .

### <a name="using-config-file"></a>Konfigurációs fájl használata

Alapértelmezés szerint az Application Insights `ApplicationInsights.config` SDK a `TelemetryConfiguration` munkakönyvtárban lévő fájlt keresi a létrehozás kor

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Megadhatja a konfigurációs fájl elérési útját is.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

További információt a [konfigurációs fájl hivatkozása című témakörben talál.](configuration-with-applicationinsights-config.md)

A [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) csomag legújabb verziójának telepítésével teljes példát kaphat a konfigurációs fájlra. Itt van a **minimális** konfiguráció függőségi gyűjtemény, amely egyenértékű a kód példa.

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

### <a name="configuring-telemetry-collection-from-code"></a>Telemetriai gyűjtemény konfigurálása kódból
> [!NOTE]
> A .NET Core nem támogatja a konfigurációs fájl olvasását. Érdemes lehet [az Application Insights SDK](../../azure-monitor/app/asp-net-core.md) használatát ASP.NET Core

* Az alkalmazás indításakor `DependencyTrackingTelemetryModule` hozzon létre és konfiguráljon példányt – egypéldánynak kell lennie, és meg kell őrizni az alkalmazás élettartamára.

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

* Gyakori telemetriai inicializálók hozzáadása

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Ha egyszerű `TelemetryConfiguration()` konstruktorral hozott létre konfigurációt, akkor a korrelációs támogatást is engedélyeznie kell. **Nem szükséges,** ha fájlból, használt `TelemetryConfiguration.CreateDefault()` `TelemetryConfiguration.Active`vagy .

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* Előfordulhat, hogy a Performance Counter kollektor modult is telepíteni és inicializálni szeretné [az itt](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/) leírtak szerint


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

            var telemetryClient = new TelemetryClient(configuration);
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
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

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
* [Figyelheti a függőségeket,](../../azure-monitor/app/asp-net-dependencies.md) hogy a REST, az SQL vagy más külső erőforrások lelassítják-e.
* [Az API-val](../../azure-monitor/app/api-custom-events-metrics.md) saját eseményeket és mérőszámokat küldhet az alkalmazás teljesítményének és használatának részletesebb megtekintéséhez.
