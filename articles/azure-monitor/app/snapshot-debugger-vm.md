---
title: .NET-alkalmazások az Azure Service Fabric, a Felhőszolgáltatás és a virtuális gépek Snapshot Debugger engedélyezése |} A Microsoft Docs
description: .NET-alkalmazások az Azure Service Fabric, a Felhőszolgáltatás és a virtuális gépek Snapshot Debugger engedélyezése
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: brahmnes
ms.openlocfilehash: ac937ddb1bcaed6813a0de4d631f820eff01e26f
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877738"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>.NET-alkalmazások az Azure Service Fabric, a Felhőszolgáltatás és a virtuális gépek Snapshot Debugger engedélyezése

Ha az ASP.NET- vagy ASP.NET core alkalmazás futtatása az Azure App Service-ben, az alábbi utasításokat is használható. Kivéve, ha az alkalmazás egy pillanatkép-hibakereső konfigurációs van szüksége, erősen javasoljuk, hogy [Snapshot Debugger engedélyezése az Application Insights portál oldalán keresztül](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). Ha az alkalmazás Azure Service Fabric, a Cloud Service, a virtuális gép fut, vagy a helyszíni gépek, az alábbi utasítások alapján kell használni. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Az ASP.NET-alkalmazások pillanatkép gyűjtésének konfigurálása

1. [Az Application Insights engedélyezése a webalkalmazásokban](../../azure-monitor/app/asp-net.md), ha még nem tette azt.

2. Tartalmazza a [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomagot az alkalmazásban.

3. Ha szükséges, testre szabott a pillanatkép-hibakereső konfiguráció hozzáadott [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Az alapértelmezett Snapshot Debugger configuration leginkább üres, és minden beállítás nem kötelező. Íme egy példa konfiguráció egyenértékű, az alapértelmezett konfiguráció:

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. A pillanatképek összegyűjtése csak az Application insights jelentett kivételek. Bizonyos esetekben (például a .NET-platformról a régebbi verzióiban), szüksége lehet [kivételek gyűjtésének konfigurálása](../../azure-monitor/app/asp-net-exceptions.md#exceptions) kivételek a portál pillanatképek megtekintéséhez.


## <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Pillanatkép-gyűjtemény ASP.NET Core 2.0-alkalmazások konfigurálása

1. [Az Application Insights engedélyezése az ASP.NET Core webes alkalmazásban](../../azure-monitor/app/asp-net-core.md), ha még nem tette azt.

    > [!NOTE]
    > Arról, hogy az alkalmazás hivatkozik 2.1.1 verzió vagy újabb verziójú, a Microsoft.ApplicationInsights.AspNetCore csomag lehet.

2. Tartalmazza a [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomagot az alkalmazásban.

3. Módosítsa az alkalmazást, `Startup` osztály hozzáadása és konfigurálása a Snapshot Collector telemetriai processzor.

    Adja hozzá a következő using utasításokat a `Startup.cs`

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   using Microsoft.ApplicationInsights.AspNetCore;
   using Microsoft.ApplicationInsights.Extensibility;
   ```

   Adja hozzá a következő `SnapshotCollectorTelemetryProcessorFactory` osztályt az `Startup` osztály.

   ```csharp
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           private readonly IServiceProvider _serviceProvider;

           public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
               _serviceProvider = serviceProvider;

           public ITelemetryProcessor Create(ITelemetryProcessor next)
           {
               var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
               return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
           }
       }
       ...
    ```
    Adja hozzá a `SnapshotCollectorConfiguration` és `SnapshotCollectorTelemetryProcessorFactory` szolgáltatások a rendszerindítási folyamat számára:

    ```csharp
       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           // Configure SnapshotCollector from application settings
           services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

           // Add SnapshotCollector telemetry processor.
           services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

           // TODO: Add other services your application needs here.
       }
   }
   ```

4. Szükség esetén a Snapshot Debugger konfigurációt testre appsettings.json SnapshotCollectorConfiguration szakasz hozzáadása. A pillanatkép-hibakereső konfiguráció az összes beállítás megadása nem kötelező. Íme egy példa konfiguráció egyenértékű, az alapértelmezett konfiguráció:

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Pillanatkép-gyűjtemény más .NET-alkalmazások konfigurálása

1. Ha az alkalmazás már nincs kialakítva az Application Insights, első lépésként [az Application Insights engedélyezése és a kialakítási kulcs beállítása](../../azure-monitor/app/windows-desktop.md).

2. Adja hozzá a [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomagot az alkalmazásban.

3. A pillanatképek összegyűjtése csak az Application insights jelentett kivételek. Előfordulhat, hogy módosítania a kódokat a jelentés azokat. A kivételkezelő kód függ az alkalmazás felépítését, de például nem éri el:
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="next-steps"></a>További lépések

- Hozzon létre a forgalmat az alkalmazásához, elindíthat egy kivételt. Várjon 10 – 15 percet kell küldeni az Application Insights-példány pillanatképeket.
- Lásd: [pillanatképek](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json) az Azure Portalon.
- Segítség a Profiler kapcsolatos hibák elhárítása: [pillanatkép-hibakereső hibaelhárítási](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).
