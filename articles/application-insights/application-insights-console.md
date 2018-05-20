---
title: A konzol alkalmazások Azure Application Insights |} Microsoft Docs
description: Webalkalmazások rendelkezésre állását, teljesítményét és használatának a figyelheti.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: lmolkova; mbullwin
ms.openlocfilehash: 679a5d82fbede4d9c464e137d615fc1367522878
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="application-insights-for-net-console-applications"></a>.NET-keretrendszerhez készült Application Insights konzolon alkalmazások
[Az Application Insights](app-insights-overview.md) lehetővé teszi, hogy a webalkalmazás rendelkezésre állását, teljesítményét és használatát a figyelésére.

Az előfizetés kell [Microsoft Azure](http://azure.com). Jelentkezzen be Microsoft-fiókkal, amely lehetséges, hogy a Windows, az Xbox Live- vagy a Microsoft más felhőszolgáltatásaival. A csoport lehet egy szervezeti Azure-előfizetéssel: a nyilvános venni azt a Microsoft-fiókjával.

## <a name="getting-started"></a>Első lépések

* [Hozzon létre egy Application Insights-erőforrást](app-insights-create-new-resource.md) az [Azure Portalon](https://portal.azure.com). Alkalmazás típusának kiválasztása **általános**.
* Végezze el a kialakítási kulcs másolását. Keresse meg a kulcsot a **Essentials** legördülő lista az új erőforrás létrehozása. 
* Telepítse a legújabb [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) csomag.
* Állítsa be a instrumentation kulcs a kódban, mielőtt bármely telemetriai nyomon követése (vagy set APPINSIGHTS_INSTRUMENTATIONKEY környezeti változó). Ezt követően meg kell manuálisan nyomon követhető a telemetriai adatok és megtekintéséhez az Azure-portálon

```csharp
TelemetryConfiguration.Active.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient();
telemetryClient.TrackTrace("Hello World!");
```

* Telepítse a legújabb változatát [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) - csomag automatikusan azt követi nyomon, HTTP, SQL vagy valamilyen más külső függőségi hívások esetében.

Előfordulhat, hogy inicializálni és konfigurálhatja az Application Insights kód használata vagy `ApplicationInsights.config` fájlt. Győződjön meg arról, hogy a lehető leghamarabb történik, az inicializálás. 

> [!NOTE]
> Hivatkozó utasításokat **ApplicationInsights.config** alkalmazásokat .NET-szabvány céloz meg, és nem vonatkoznak a .NET Core alkalmazások csak vonatkoznak. 

### <a name="using-config-file"></a>A konfigurációs fájl használatával

Alapértelmezés szerint Application Insights SDK keresi `ApplicationInsights.config` fájlt a munkakönyvtárhoz. Ha `TelemetryConfiguration` létrehozása folyamatban van

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Is meg lehet, hogy a konfigurációs fájl elérési útját.

```csharp
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration("ApplicationInsights.config");
```

További információkért lásd: [hivatkozást a konfigurációs](app-insights-configuration-with-applicationinsights-config.md).

A konfigurációs fájl teljes példa kaphat legújabb verziójának telepítése [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) csomag. Itt a **minimális** függőségi gyűjteményt, amely megfelel a Kódpélda konfigurációját.

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

* Alkalmazás indítás folyamán létrehozása és konfigurálása `DependencyTrackingTelemetryModule` példány - az Egypéldányos kell lennie és az alkalmazás élettartamának meg kell őrizni.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/NuGet/ApplicationInsights.config.install.xdt#L20
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Adja hozzá a közös telemetriai inicializálók

```csharp
// stamps telemetry with correlation identifiers
TelemetryConfiguration.Active.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());

// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
TelemetryConfiguration.Active.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

* .NET-keretrendszer Windows-alkalmazások esetén is telepíthet, és teljesítményszámláló adatgyűjtő modul inicializálása leírtak [Itt](http://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)

#### <a name="full-example"></a>Teljes példa

```csharp
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
    // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/NuGet/ApplicationInsights.config.install.xdt#L20
    module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
    module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

    // initialize the module
    module.Initialize(configuration);

    return module;
}
```

## <a name="next-steps"></a>További lépések
* [Függőségek figyelése](app-insights-asp-net-dependencies.md) megjelenítéséhez, ha a többi, SQL vagy más külső erőforrások lassítja meg.
* [Az API-val](app-insights-api-custom-events-metrics.md) a saját eseményeket és metrikákat az alkalmazás teljesítményét és használatának részletes nézet küldését.
