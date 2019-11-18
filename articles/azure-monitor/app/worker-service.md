---
title: A Worker Service-alkalmazások Application Insights (nem HTTP-alkalmazások) | Microsoft Docs
description: A .NET Core/. NET Framework nem HTTP-alkalmazások figyelése Application Insights használatával.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cijothomas
ms.author: cithomas
ms.date: 09/15/2019
ms.openlocfilehash: 5f812d5fe1b25358a0bf09ebf879569ae29b33f3
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131882"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Application Insights Worker Service-alkalmazásokhoz (nem HTTP-alkalmazások)

Application Insights egy `Microsoft.ApplicationInsights.WorkerService`nevű új SDK kiadása, amely a legmegfelelőbb a nem HTTP-alapú számítási feladatokhoz, például az üzenetküldéshez, a háttérbeli feladatokhoz, a konzol alkalmazásaihoz stb. Az ilyen típusú alkalmazások nem rendelkeznek olyan bejövő HTTP-kérések fogalmával, mint a hagyományos ASP.NET/ASP.NET Core-webalkalmazások, és így Application Insights csomagok használata [ASP.net](asp-net.md) vagy [ASP.net Core](asp-net-core.md) alkalmazásokhoz nem támogatott.

Az új SDK önmagában nem végez telemetria-gyűjteményt. Ehelyett más jól ismert Application Insights automatikus gyűjtők, például a [DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), a [PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), a [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) stb. Ez az SDK a telemetria-gyűjtés engedélyezéséhez és konfigurálásához `IServiceCollection` bővítményi metódusokat tesz elérhetővé.

## <a name="supported-scenarios"></a>Támogatott esetek

Az [Application INSIGHTS SDK for Worker szolgáltatás](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) ideális a nem HTTP-alapú alkalmazásokhoz, függetlenül attól, hogy hol vagy hogyan futnak. Ha az alkalmazás fut, és hálózati kapcsolattal rendelkezik az Azure-hoz, a telemetria gyűjthet. A Application Insights figyelése mindenhol támogatott a .NET Core-ban. Ez a csomag az újonnan bevezetett [.net core 3,0 Worker Service](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances)-ben, a [ASP.net Core 2.1/2.2-ben](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2), a konzolon futó alkalmazások (.net Core/.NET Framework) háttérbeli feladatai között használható.

## <a name="prerequisites"></a>Előfeltételek

Érvényes Application Insights kialakítási kulcs. Ez a kulcs szükséges ahhoz, hogy bármilyen telemetria küldjön a Application Insightsnak. Ha létre kell hoznia egy új Application Insights-erőforrást a kialakítási kulcs beszerzéséhez, tekintse meg a [Application Insights erőforrás létrehozása](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)című témakört.

## <a name="using-application-insights-sdk-for-worker-services"></a>Application Insights SDK használata a Worker-szolgáltatásokhoz

1. Telepítse a [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) csomagot az alkalmazásba.
   A következő kódrészlet azokat a módosításokat mutatja be, amelyeket fel kell venni a projekt `.csproj` fájljába.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.8.0" />
    </ItemGroup>
```

1. Hívja meg `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` Extension metódust `IServiceCollection`n, amely megadja a kialakítási kulcsot. Ezt a metódust az alkalmazás elején kell meghívni. A pontos hely az alkalmazás típusától függ.

1. `ILogger` példány vagy `TelemetryClient` példány beolvasása a függőségi injektálási (DI) tárolóból a `serviceProvider.GetRequiredService<TelemetryClient>();` meghívásával vagy a konstruktor-injektálás használatával. Ez a lépés elindítja `TelemetryConfiguration` és az automatikus gyűjtési modulok beállítását.

Az egyes alkalmazási típusok részletes ismertetését a következő szakaszokban találja.

## <a name="net-core-30-worker-service-application"></a>.NET Core 3,0 Worker szolgáltatásalkalmazás

A teljes példa [itt](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) van megosztva

1. A [.net Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0) letöltése és telepítése
2. Hozzon létre egy új feldolgozói szolgáltatást a Visual Studio új projekt sablonjának vagy parancssori felületének használatával `dotnet new worker`
3. Telepítse a [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) csomagot az alkalmazásba.

4. Adja hozzá `services.AddApplicationInsightsTelemetryWorkerService();` a `CreateHostBuilder()` metódushoz a `Program.cs` osztályban, az alábbi példában látható módon:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Módosítsa a `Worker.cs` az alábbi példa szerint.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. Állítsa be a kialakítási kulcsot.

    Bár a kialakítási kulcsot a `AddApplicationInsightsTelemetryWorkerService`argumentumként is megadhatja, javasoljuk, hogy adja meg a kialakítási kulcsot a konfigurációban. A következő mintakód bemutatja, hogyan adható meg a rendszerállapot kulcsa `appsettings.json`ban. A közzététel során győződjön meg arról, hogy a `appsettings.json` a rendszer átmásolja az alkalmazás gyökérkönyvtár mappájába.

```json
    {
        "ApplicationInsights":
            {
            "InstrumentationKey": "putinstrumentationkeyhere"
            },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

Azt is megteheti, hogy a kialakítási kulcsot a következő környezeti változók egyikében határozza meg.
`APPINSIGHTS_INSTRUMENTATIONKEY` vagy `ApplicationInsights:InstrumentationKey`

Például:`SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
VAGY `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

A `APPINSIGHTS_INSTRUMENTATIONKEY` általában meghatározza a rendszerállapot-kulcsot, amelyet a rendszer a webes feladatokhoz Web Apps üzembe helyezett alkalmazások számára biztosít.

> [!NOTE]
> A kód WINS-ben megadott rendszerállapot-kulcs a `APPINSIGHTS_INSTRUMENTATIONKEY`környezeti változón keresztül, amely más beállításokon keresztül nyerhető.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>Az üzemeltetett szolgáltatásokkal ASP.NET Core háttérbeli feladatok
[Ez a](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio) dokumentum ismerteti, hogyan hozhat létre háttérbeli feladatokat ASP.net Core 2.1/2.2 alkalmazásban.

A teljes példa [itt](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) van megosztva

1. Telepítse a Microsoft. ApplicationInsights. WorkerService (https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) csomagot az alkalmazásba.
2. Adja hozzá a `services.AddApplicationInsightsTelemetryWorkerService();`t a `ConfigureServices()` metódushoz, az alábbi példában látható módon:

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

Az alábbi kód `TimedHostedService` a háttérben futó feladat logikájának helyét.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = await httpClient.GetAsync("https://bing.com");
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. Állítsa be a kialakítási kulcsot.
   Használja ugyanazt a `appsettings.json` a fenti .NET Core 3,0 Worker Service-példa alapján.

## <a name="net-corenet-framework-console-application"></a>.NET Core/. NET Framework Console-alkalmazás

A cikk elején említettek szerint az új csomag lehetővé teszi, hogy a Application Insights Telemetria akár egy normál konzolos alkalmazásból is engedélyezzék. Ez a csomag a [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard), így a .net Core 2,0-as vagy újabb verziójában, illetve a .NET-keretrendszer 4.7.2 vagy újabb verziókban használható a konzolon futtatott alkalmazásokhoz.

A teljes példa [itt](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) van megosztva

1. Telepítse a Microsoft. ApplicationInsights. WorkerService (https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) csomagot az alkalmazásba.

2. Módosítsa a Program.cs az alábbi példában látható módon.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key must be specified here.
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

Ez a konzolos alkalmazás ugyanazt az alapértelmezett `TelemetryConfiguration`használja, és a korábbi szakaszban található példákkal megegyező módon testreszabható.

## <a name="run-your-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást. A fentiekben ismertetett feldolgozók a http-hívást másodpercenként bing.com, és néhány naplót is kibocsátanak a ILogger használatával. Ezek a sorok `StartOperation` `TelemetryClient`-hívásba vannak becsomagolva, amely egy művelet létrehozására szolgál (ebben a példában a "művelet" nevű `RequestTelemetry`). A Application Insights összegyűjti ezeket a ILogger-naplókat (alapértelmezés szerint a figyelmeztetést) és a függőségeket, és ezek a szülő-gyermek kapcsolattal rendelkező `RequestTelemetry` lesznek összekapcsolva. A korreláció a folyamat/hálózat határán is működik. Ha például a hívás egy másik figyelt összetevőre történt, akkor a rendszer ezt a szülővel is összefügg.

`RequestTelemetry` ez az egyéni művelet a beérkező webes kérések egy tipikus webalkalmazásban való megfelelője lehet. Habár nem szükséges egy művelet használata, az [Application Insights korrelációs adatmodellel](https://docs.microsoft.com/azure/azure-monitor/app/correlation) rendelkezik, és a `RequestTelemetry` a szülő műveletként működik, és minden, a munkavégző iteráción belül létrehozott telemetria, amely logikailag ugyanahhoz a művelethez tartozóként van kezelve. Ez a megközelítés azt is biztosítja, hogy az összes generált telemetria (automatikus és manuális) ugyanaz lesz a `operation_id`. Mivel a mintavételezés a `operation_id`on alapul, a mintavételi algoritmus vagy az összes telemetria egyetlen iterációból tartja vagy eldobja.

Az alábbi listában a Application Insights által automatikusan összegyűjtött teljes telemetria szerepelnek.

### <a name="live-metrics"></a>Élő metrikák

Az [élő metrikák](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) segítségével gyorsan ellenőrizheti, hogy a Application Insights figyelése megfelelően van-e konfigurálva. Habár néhány percet is igénybe vehet, amíg a telemetria elindul a Portálon és az elemzésekben, az élő metrikák közel valós időben mutatják be a futó folyamat CPU-használatát. Más telemetria is megjeleníthet, például a kérelmeket, a függőségeket, a Nyomkövetéseket stb.

### <a name="ilogger-logs"></a>ILogger-naplók

A súlyossági `Warning` vagy nagyobb `ILogger` keresztül kibocsátott naplók automatikusan rögzítésre kerülnek. A [ILogger-dokumentumok](ilogger.md#control-logging-level) követésével testreszabhatja, hogy a Application Insights mely naplózási szinteket rögzíti.

### <a name="dependencies"></a>Függőségek

A függőségi gyűjtemény alapértelmezés szerint engedélyezve van. [Ez](asp-net-dependencies.md#automatically-tracked-dependencies) a cikk az automatikusan összegyűjtött függőségeket ismerteti, valamint a manuális nyomon követésre szolgáló lépéseket is tartalmaz.

### <a name="eventcounter"></a>EventCounter

a `EventCounterCollectionModule` alapértelmezés szerint engedélyezve van, és a .NET Core 3,0-alkalmazásokból gyűjti össze a számlálók alapértelmezett készletét. A [EventCounter](eventcounters.md) -oktatóanyag a gyűjtött teljesítményszámlálók alapértelmezett készletét sorolja fel. Emellett a lista testreszabására vonatkozó utasításokat is tartalmaz.

### <a name="manually-tracking-additional-telemetry"></a>További telemetria manuális követése

Habár az SDK automatikusan gyűjti a telemetria a fentiekben leírtak szerint, a legtöbb esetben a felhasználónak további telemetria kell küldenie Application Insights szolgáltatásnak. A további telemetria ajánlott nyomon követni a függőségi Befecskendezésből `TelemetryClient` egy példányának beszerzésével, majd a támogatott `TrackXXX()` [API](api-custom-events-metrics.md) -módszerek egyikének meghívásával. Egy másik tipikus használati eset a [műveletek egyéni nyomon követése](custom-operations-tracking.md). Ezt a megközelítést a fenti feldolgozói példákban mutatjuk be.

## <a name="configure-the-application-insights-sdk"></a>A Application Insights SDK konfigurálása

A Worker Service SDK által használt alapértelmezett `TelemetryConfiguration` hasonlít a ASP.NET vagy ASP.NET Core alkalmazásban használt automatikus konfigurációhoz, mínusz a telemetria `HttpContext`-ból való dúsításához használt TelemetryInitializers.

Testreszabhatja a Application Insights SDK for Worker szolgáltatást az alapértelmezett konfiguráció módosításához. Előfordulhat, hogy a Application Insights ASP.NET Core SDK felhasználói megismerik a konfiguráció módosítását ASP.NET Core beépített [függőségi befecskendezés](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)használatával. A WorkerService SDK is hasonló alapelveken alapul. A `ConfigureServices()` szakaszban szinte minden konfigurációs módosítást hajthat végre, ha a megfelelő metódusokat hívja meg a `IServiceCollection`on, az alább részletezett módon.

> [!NOTE]
> Az SDK használatakor a konfiguráció módosítása `TelemetryConfiguration.Active` módosításával nem támogatott, és a módosítások nem fognak megjelenni.

### <a name="using-applicationinsightsserviceoptions"></a>A ApplicationInsightsServiceOptions használata

Néhány gyakori beállítást úgy módosíthat, hogy `ApplicationInsightsServiceOptions` átadásával `AddApplicationInsightsTelemetryWorkerService`, ahogy az alábbi példában látható:

```csharp
    using Microsoft.ApplicationInsights.WorkerService;

    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
    }
```

Vegye figyelembe, hogy az SDK-ban lévő `ApplicationInsightsServiceOptions` az ASP.NET Core SDK-ban `Microsoft.ApplicationInsights.AspNetCore.Extensions` helyett a névtérben található `Microsoft.ApplicationInsights.WorkerService`.

Gyakran használt beállítások a `ApplicationInsightsServiceOptions`

|Beállítás | Leírás | Alapértelmezett
|---------------|-------|-------
|EnableQuickPulseMetricStream | LiveMetrics funkció engedélyezése/letiltása | true
|EnableAdaptiveSampling | Adaptív mintavételezés engedélyezése/letiltása | true
|EnableHeartbeat | A szívverések funkció engedélyezése/letiltása, amely rendszeres időközönként (15 perces alapértelmezett) a "HeartBeatState" nevű egyéni metrikát küldi el a (z), például a .NET-es verzióval, az Azure-környezettel kapcsolatos információkkal, ha vannak ilyenek, stb. | true
|AddAutoCollectedMetricExtractor | Az AutoCollectedMetrics Extractor engedélyezése/letiltása, amely egy olyan TelemetryProcessor, amely előre összevont metrikákat küld a kérelmek/függőségek számára a mintavétel megkezdése előtt. | true

A legnaprakészebb listához tekintse [meg a `ApplicationInsightsServiceOptions`konfigurálható beállításait](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) .

### <a name="sampling"></a>Mintavételezés

Az Application Insights SDK for Worker szolgáltatás a rögzített sebességű és az adaptív mintavételezést is támogatja. Az adaptív mintavételezés alapértelmezés szerint engedélyezve van. A munkavégző szolgáltatás mintavételezésének konfigurálása ugyanúgy történik, mint [ASP.net Core alkalmazásokhoz](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers hozzáadása

Használjon [telemetria-inicializálást](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) , ha az összes telemetria ellátott tulajdonságokat szeretné megadni.

Vegyen fel minden új `TelemetryInitializer`t a `DependencyInjection` tárolóba, és az SDK automatikusan hozzáadja őket a `TelemetryConfiguration`hoz.

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers eltávolítása

A telemetria inicializálók alapértelmezés szerint jelennek meg. Az összes vagy adott telemetria-inicializáló eltávolításához használja az alábbi mintát a `AddApplicationInsightsTelemetryWorkerService()`meghívása *után* .

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>Telemetria-processzorok hozzáadása

A `IServiceCollection``AddApplicationInsightsTelemetryProcessor` bővítmény módszerével egyéni telemetria-processzorokat adhat hozzá `TelemetryConfiguration`okhoz. A telemetria processzorok [speciális szűrési forgatókönyvekben](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer) való használata lehetővé teszi, hogy a Application Insights a szolgáltatásnak küldött, a telemetria által befoglalt vagy kizárni kívánt funkciók jobban átirányíthatók legyenek. Használja az alábbi példát.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Alapértelmezett TelemetryModules konfigurálása vagy eltávolítása

A Application Insights telemetria-modulokkal automatikusan gyűjti az adott számítási feladatokhoz tartozó telemetria a manuális nyomon követés nélkül.

A következő automatikus gyűjtési modulok alapértelmezés szerint engedélyezve vannak. Ezeknek a moduloknak a feladata a telemetria automatikus gyűjtése. Az alapértelmezett viselkedés megváltoztatásához letilthatja vagy konfigurálhatja őket.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Az alapértelmezett `TelemetryModule`konfigurálásához használja a `IServiceCollection``ConfigureTelemetryModule<T>` bővítmény módszerét az alábbi példában látható módon.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>Telemetria-csatorna konfigurálása

Az alapértelmezett csatorna `ServerTelemetryChannel`. A következő példában látható módon felülbírálhatja azt.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>Telemetria dinamikus letiltása

Ha a telemetria feltételesen és dinamikusan szeretné letiltani, akkor feloldható `TelemetryConfiguration` példány ASP.NET Core függőségi injektálási tárolóval bárhol a kódban, és beállíthatja `DisableTelemetry` jelzőt.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Hogyan követhetem nyomon az automatikusan összegyűjtött telemetria?

`TelemetryClient` példányának beszerzése a konstruktor injekciójának használatával, és a szükséges `TrackXXX()` metódus meghívása. Nem javasoljuk, hogy hozzon létre új `TelemetryClient`-példányokat. A `TelemetryClient` egy egyedi példánya már regisztrálva van a `DependencyInjection` tárolóban, amely a telemetria többi részével megosztja a `TelemetryConfiguration`. Új `TelemetryClient`-példány létrehozása csak akkor javasolt, ha a többi telemetria eltérő konfigurációra van szükség.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Használhatom a Visual Studio IDE-t a Application Insights bevezetésére a Worker Service-projektbe?

A Visual Studio IDE-előkészítés jelenleg csak a ASP.NET/ASP.NET Core-alkalmazások esetében támogatott. Ez a dokumentum akkor frissül, ha a Visual Studio-hajók támogatják a feldolgozó szolgáltatások alkalmazásainak bevezetését.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Engedélyezhető Application Insights-figyelés az olyan eszközökkel, mint a Állapotmonitor?

Nem. A [Állapotmonitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) és a [Állapotmonitor v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) jelenleg csak a 4. x ASP.net támogatja.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Ha Linuxon futtatom az alkalmazást, az összes funkció támogatott?

Igen. Az SDK funkcióinak támogatása minden platformon azonos, a következő kivételekkel:

* A teljesítményszámlálók csak a Windows rendszerben támogatottak, az élő metrikák által megjelenített processzor/memória kivételével.
* Bár a `ServerTelemetryChannel` alapértelmezés szerint engedélyezve van, ha az alkalmazás Linux vagy MacOS rendszeren fut, a csatorna nem hoz létre automatikusan helyi tárolási mappát, hogy a telemetria átmenetileg megmaradjanak, ha hálózati problémák vannak. Ennek a korlátozásnak a miatt a telemetria elvész, ha ideiglenes hálózati vagy kiszolgálói problémák merülnek fel. A probléma megkerüléséhez konfigurálja a csatorna helyi mappáját:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>Mintaalkalmazások

[.Net Core Console-alkalmazás](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Használja ezt a mintát, ha a .NET Core (2,0 vagy újabb) vagy a .NET Framework (4.7.2 vagy újabb) nyelven írt konzol alkalmazást használ

[ASP .net Core háttérbeli feladatok a HostedServices](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Akkor használja ezt a mintát, ha a Asp.Net Core 2.1/2.2-es verzióban van, és a háttérben elvégzendő feladatokat az [alábbi](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2) hivatalos útmutatás alapján hozza létre

[.Net Core 3,0 Worker szolgáltatás](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Akkor használja ezt a mintát, ha a .NET Core 3,0 Worker szolgáltatásalkalmazás az [alábbi](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template) hivatalos útmutatás szerint van

## <a name="open-source-sdk"></a>Open-source SDK

[Olvassa el és járuljon hozzá a kódhoz](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="next-steps"></a>További lépések

* [Az API használatával](../../azure-monitor/app/api-custom-events-metrics.md) saját eseményeket és mérőszámokat küldhet az alkalmazás teljesítményének és használatának részletes áttekintéséhez.
* A [további függőségek nyomon követése nem történik meg automatikusan](../../azure-monitor/app/auto-collect-dependencies.md).
* Az [automatikusan összegyűjtött telemetria gazdagítása vagy szűrése](../../azure-monitor/app/api-filtering-sampling.md).
* [Függőség injekció ASP.net Coreban](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).
