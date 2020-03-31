---
title: Pillanatkép-hibakereső engedélyezése .NET-alkalmazásokhoz az Azure Service Fabricben, a Felhőszolgáltatásban és a virtuális gépekben | Microsoft dokumentumok
description: Pillanatkép-hibakereső engedélyezése .NET-alkalmazásokhoz az Azure Service Fabricben, a Felhőszolgáltatásban és a virtuális gépekben
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 194a2da23c8fb405c492df8f6ee173cc97fde4ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671341"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Pillanatkép-hibakereső engedélyezése .NET-alkalmazásokhoz az Azure Service Fabricben, a Felhőszolgáltatásban és a virtuális gépekben

Ha a ASP.NET vagy ASP.NET alapvető alkalmazás fut az Azure App Service-ben, erősen ajánlott [a Snapshot Debugger engedélyezése az Application Insights portálon keresztül.](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) Ha azonban az alkalmazás testre szabott Snapshot Debugger-konfigurációt vagy a .NET core előzetes verzióját igényli, akkor ezt az utasítást az [Application Insights portállapon keresztül történő engedélyezésre](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)vonatkozó utasítások ***mellett*** kell követnie.

Ha az alkalmazás fut az Azure Service Fabric, felhőszolgáltatás, virtuális gépek, vagy a helyszíni gépek, a következő utasításokat kell használni. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Pillanatképgyűjtemény konfigurálása ASP.NET alkalmazásokhoz

1. [Engedélyezze az Application Insights ot a webalkalmazásban,](../../azure-monitor/app/asp-net.md)ha még nem tette meg.

2. A [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet csomag felvétele az alkalmazásba.

3. Szükség esetén testreszabta az [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)fájlhoz hozzáadott Pillanatkép-hibakereső konfigurációt. Az alapértelmezett Snapshot Debugger konfiguráció többnyire üres, és minden beállítás nem kötelező. Íme egy példa, amely az alapértelmezett konfigurációval egyenértékű konfigurációt mutat be:

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

4. Pillanatképek csak az Application Insights nak jelentett kivételek alapján gyűjtik a pillanatképeket. Bizonyos esetekben (például a .NET platform régebbi verzióiban) előfordulhat, hogy a kivételgyűjteményt úgy kell [konfigurálnia,](../../azure-monitor/app/asp-net-exceptions.md#exceptions) hogy a portálon a pillanatképeket is megtekintő kivételeket láthassa.


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>Pillanatképgyűjtemény konfigurálása ASP.NET Core 2.0 vagy újabb rendszert használó alkalmazásokhoz

1. [Engedélyezze az Application Insights ot a ASP.NET Core webalkalmazásban,](../../azure-monitor/app/asp-net-core.md)ha még nem tette meg.

    > [!NOTE]
    > Győződjön meg arról, hogy az alkalmazás a Microsoft.ApplicationInsights.AspNetCore csomag 2.1.1-es vagy újabb verziójára hivatkozik.

2. A [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet csomag felvétele az alkalmazásba.

3. Módosítsa az alkalmazás `Startup` osztályát a Snapshot Collector telemetriai processzorának hozzáadásához és konfigurálásához.
    1. Ha [a Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet csomag 1.3.5-ös vagy újabb verzióját `Startup.cs`használja, akkor adja hozzá a következő t a hoz.

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       Adja hozzá a következőket a ConfigureServices `Startup` metódus `Startup.cs`végén a osztályban.

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. Ha [a Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet csomagot használja az 1.3.4-es vagy `Startup.cs`újabb verzióban, akkor adja hozzá a következő t a hozzá a alkalmazáshoz.

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```
    
       Adja hozzá `SnapshotCollectorTelemetryProcessorFactory` a `Startup` következő osztályt az osztályhoz.
    
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
        Adja `SnapshotCollectorConfiguration` hozzá `SnapshotCollectorTelemetryProcessorFactory` a és a szolgáltatásokat az indítási folyamathoz:
    
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

4. Szükség esetén testreszabta a Snapshot Debugger konfigurációját egy SnapshotCollectorConfiguration szakasz hozzáadásával az appsettings.json hoz. A Snapshot Debugger konfigurációban minden beállítás megadása nem kötelező. Íme egy példa, amely az alapértelmezett konfigurációval egyenértékű konfigurációt mutat be:

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

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Pillanatképgyűjtemény konfigurálása más .NET-alkalmazásokhoz

1. Ha az alkalmazás még nincs instrumentálva az Application Insights, első lépések [az Application Insights engedélyezésével és a műszerezési kulcs beállításával.](../../azure-monitor/app/windows-desktop.md)

2. Adja hozzá a [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet csomagot az alkalmazáshoz.

3. Pillanatképek csak az Application Insights nak jelentett kivételek alapján gyűjtik a pillanatképeket. Előfordulhat, hogy módosítania kell a kódot, hogy jelentse őket. A kivételkezelési kód az alkalmazás szerkezetétől függ, de egy példa az alábbi:
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

- Hozzon létre forgalmat az alkalmazás, amely kivételt válthat ki. Ezután várjon 10–15 percet, amíg a pillanatképeket el kell küldeni az Application Insights-példány.
- Tekintse meg a [pillanatképek az](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) Azure Portalon.
- A Pillanatkép-hibakeresővel kapcsolatos problémák elhárításával kapcsolatos segítségért olvassa el a [Pillanatképhiba-elhárító hibaelhárításcímű témakört.](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)
