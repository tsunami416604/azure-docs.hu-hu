---
title: Application Insights a Worker Service-alkalmazásokhoz (nem HTTP-alkalmazások)
description: A .NET Core/.NET Framework nem HTTP-alkalmazások figyelése az Azure Monitor Application Insights segítségével.
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 34a64ffa67b1a43a77391e0d50ddf1bfad0f73ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501159"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Alkalmazáselemzés a feldolgozószolgáltatás-alkalmazásokhoz (nem HTTP-alkalmazások)

Az Application Insights egy új SDK-t ad ki, `Microsoft.ApplicationInsights.WorkerService`amely a legalkalmasabb a nem HTTP-s számítási feladatokhoz, például az üzenetküldéshez, a háttérfeladatokhoz, a konzolalkalmazásokhoz stb. Az ilyen típusú alkalmazások nem rendelkeznek a bejövő HTTP-kérelem fogalmával, mint egy hagyományos ASP.NET/ASP.NET Core webalkalmazás, és így az Application Insights-csomagok használata [ASP.NET](asp-net.md) vagy [ASP.NET Core](asp-net-core.md) alkalmazások nem támogatott.

Az új SDK önmagában nem végez telemetriai-gyűjteményt. Ehelyett hozza más jól ismert Application Insights automatikus gyűjtők, mint [a DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) stb. Ez az SDK a `IServiceCollection` telemetriai-gyűjtemény engedélyezéséhez és konfigurálásához elérhetőbővítmény-metódusokat tesz elérhetővé.

## <a name="supported-scenarios"></a>Támogatott esetek

Az [Application Insights SDK a dolgozó i. szolgáltatás](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) a legalkalmasabb a nem HTTP-alkalmazások, függetlenül attól, hogy hol és hogyan futnak. Ha az alkalmazás fut, és hálózati kapcsolat tal rendelkezik az Azure-hoz, telemetriai adatokgyűjthetők. Az Application Insights figyelése mindenhol támogatott .NET Core támogatott. Ez a csomag használható az újonnan bevezetett [.NET Core 3.0 Worker Service](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances) [szolgáltatásban, Asp.Net a core 2.1/2.2](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2), a konzolalkalmazások (.NET Core/ .NET Framework) háttérfeladataiban stb.

## <a name="prerequisites"></a>Előfeltételek

Egy érvényes Application Insights instrumentation kulcs. Ez a kulcs szükséges az Application Insights bármely telemetriai adatok küldéséhez. Ha egy instrumentation kulcs lekért új Application Insights-erőforrást kell létrehoznia, olvassa [el az Application Insights-erőforrás létrehozása című témakört.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)

## <a name="using-application-insights-sdk-for-worker-services"></a>Az Application Insights SDK használata a dolgozói szolgáltatásokhoz

1. Telepítse a [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) csomagot az alkalmazásra.
   A következő kódrészlet azokat a módosításokat mutatja be, amelyeket hozzá kell adni a projekt fájljához. `.csproj`

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. A `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` híváskiterjesztés `IServiceCollection`imódszere biztosítja a műszerezési kulcsot. Ezt a módszert az alkalmazás elején kell meghívni. A pontos hely az alkalmazás típusától függ.

1. Egy `ILogger` példány `TelemetryClient` vagy példány lekérése a függőségi injektálás (DI) tárolóból a Konstruktor-injektálás használatával. `serviceProvider.GetRequiredService<TelemetryClient>();` Ez a lépés elindítja a beállítás és az `TelemetryConfiguration` automatikus gyűjtési modulok.

Az egyes alkalmazástípusokra vonatkozó konkrét utasításokat a következő szakaszok ismertetik.

## <a name="net-core-30-worker-service-application"></a>.NET Core 3.0 munkavégző szolgáltatás alkalmazása

A teljes példa [itt](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) van megosztva

1. A [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0) letöltése és telepítése
2. Új munkavégző szolgáltatás projekt létrehozása a Visual Studio új projektsablonjának vagy parancssorának használatával`dotnet new worker`
3. Telepítse a [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) csomagot az alkalmazásra.

4. Add `services.AddApplicationInsightsTelemetryWorkerService();` hozzá `CreateHostBuilder()` a `Program.cs` módszer az osztályban, mint ebben a példában:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Módosítsa `Worker.cs` a mint egy alábbi példa.

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

6. Állítsa be a műszerkulcsot.

    Bár a műszerezési kulcsot argumentumként `AddApplicationInsightsTelemetryWorkerService`megadhatja, javasoljuk, hogy adja meg a instrumentation kulcsot a konfigurációban. A következő kódminta bemutatja, hogyan `appsettings.json`adható meg egy műszerezési kulcs a ban. Győződjön `appsettings.json` meg arról, hogy a közzététel során az alkalmazás gyökérmappájába másolva van.

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

Másik lehetőségként adja meg a műszerezési kulcsot az alábbi környezeti változók egyikében.
`APPINSIGHTS_INSTRUMENTATIONKEY` vagy `ApplicationInsights:InstrumentationKey`

Például:`SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
Vagy`SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

Általában `APPINSIGHTS_INSTRUMENTATIONKEY` megadja a webalkalmazásokba webfeladatként telepített alkalmazások instrumentation kulcsát.

> [!NOTE]
> A kódban megadott instrumentation kulcs megnyeri `APPINSIGHTS_INSTRUMENTATIONKEY`a környezeti változót, amely más beállításokkal szemben nyer.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET Alapvető háttérfeladatok hosztolt szolgáltatásokkal

[Ez](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio) a dokumentum ismerteti, hogyan hozhat létre háttérfeladatokat ASP.NET Core 2.1/2.2 alkalmazásban.

A teljes példa [itt](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) van megosztva

1. Telepítse a Microsoft.ApplicationInsights.WorkerService(https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) csomagot az alkalmazásba.
2. Adja `services.AddApplicationInsightsTelemetryWorkerService();` hozzá `ConfigureServices()` a módszert, mint ebben a példában:

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

A következőkben `TimedHostedService` a háttérfeladat logikájának kódja látható.

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

3. Állítsa be a műszerkulcsot.
   Használja ugyanazt `appsettings.json` a fenti .

## <a name="net-corenet-framework-console-application"></a>.NET Core/.NET Framework Console alkalmazás

Ahogy azt a cikk elején, az új csomag használható az Application Insights telemetriai adatok engedélyezéséhez még egy rendszeres konzolalkalmazás. Ez a [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)csomag a .NET Core 2.0-s vagy újabb verzióban, illetve a .NET Framework 4.7.2-es vagy újabb verzióban található konzolalkalmazásokhoz használható.

A teljes példa [itt](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) van megosztva

1. Telepítse a Microsoft.ApplicationInsights.WorkerService(https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) csomagot az alkalmazásba.

2. Módosítsa Program.cs a példa alatt.

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
                // Hence instrumentation key and any changes to default logging level must be specified here.
                services.AddLogging(loggingBuilder => loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Category", LogLevel.Information));
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

Ez a konzolalkalmazás is `TelemetryConfiguration`ugyanazt az alapértelmezett , és testre ugyanúgy, mint a korábbi szakaszban.

## <a name="run-your-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást. A példa a munkavállalók a fenti teszi a http hívás minden második bing.com, és azt is bocsát ki néhány naplók segítségével ILogger. Ezek a sorok `StartOperation` a `TelemetryClient`művelet létrehozásához használt hívásbelsejében `RequestTelemetry` vannak csomagolva (ebben a példában "művelet"). Az Application Insights összegyűjti ezeket az ILogger-naplókat (figyelmeztetés vagy alapértelmezés `RequestTelemetry` szerint a fenti) és a függőségeket, és korrelál nak a szülő-gyermek kapcsolattal. A korreláció is működik a folyamat/hálózat határán. Ha például a hívás egy másik figyelt összetevőhöz történt, akkor az ezzel a szülővel is korrelál.

Ez az `RequestTelemetry` egyéni művelet lehet tekinteni, mint egy bejövő webes kérelem egy tipikus webalkalmazás. Bár nem szükséges egy művelet használata, illeszkedik a legjobban az `RequestTelemetry` Application Insights [korrelációs adatmodell](https://docs.microsoft.com/azure/azure-monitor/app/correlation) - a szülő műveletként, és minden telemetriai használatával a dolgozóitítás belül történik, mint logikailag ugyanahhoz a művelethez tartozó. Ez a megközelítés azt is biztosítja, hogy az összes létrehozott `operation_id`telemetriai adatok (automatikus és kézi) ugyanaz lesz. Mintavételi `operation_id`alapul, mintavételi algoritmus vagy megtartja, vagy eldobja az összes telemetriai egyetlen iteráció.

Az alábbi lista a teljes telemetriai adatok automatikusan az Application Insights által gyűjtött.

### <a name="live-metrics"></a>Élő metrikák

[Az élő metrikák](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) segítségével gyorsan ellenőrizheti, hogy az Application Insights figyelése megfelelően van-e konfigurálva. Bár eltarthat néhány percig, mielőtt a telemetriai adatok megjelennek a portálon és az elemzésben, a Live Metrics közel valós időben mutatja a futó folyamat PROCESSZOR-használatát. Más telemetriai adatokat is megjeleníthet, például kéréseket, függőségeket, nyomkövetéseket stb.

### <a name="ilogger-logs"></a>ILogger naplók

A súlyos vagy `ILogger` nagyobb `Warning` súlyosságú vagy annál nagyobb értéken kibocsátott naplókat a rendszer automatikusan rögzíti. Kövesse [ILogger docs](ilogger.md#control-logging-level) testre szabhatja, hogy mely naplószintek által rögzített Application Insights.

### <a name="dependencies"></a>Függőségek

A függőségi gyűjtemény alapértelmezés szerint engedélyezve van. [Ez](asp-net-dependencies.md#automatically-tracked-dependencies) a cikk ismerteti az automatikusan gyűjtött függőségeket, és a manuális nyomon követés végrehajtásának lépéseit is tartalmazza.

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule`alapértelmezés szerint engedélyezve van, és a .NET Core 3.0-s alkalmazásokból gyűjti a számlálók alapértelmezett készletét. Az [EventCounter](eventcounters.md) oktatóanyag felsorolja az összegyűjtött számlálók alapértelmezett készletét. Azt is utasításokat testre a listát.

### <a name="manually-tracking-additional-telemetry"></a>További telemetriai adatok manuális nyomon követése

Míg az SDK automatikusan gyűjti a telemetriai adatokat a fent leírtak szerint, a legtöbb esetben a felhasználónak további telemetriai adatokat kell küldenie az Application Insights szolgáltatásba. A további telemetriai adatok nyomon követésének `TelemetryClient` ajánlott módja a függőségi injektálás egy példányának beszerzése, majd a támogatott `TrackXXX()` [API-metódusok](api-custom-events-metrics.md) egyikének felhívása. Egy másik tipikus használati eset a [műveletek egyéni nyomon követése](custom-operations-tracking.md). Ezt a megközelítést a fenti feldolgozói példák mutatják be.

## <a name="configure-the-application-insights-sdk"></a>Az Application Insights SDK konfigurálása

A `TelemetryConfiguration` munkavégző szolgáltatás SDK által használt alapértelmezett érték hasonló a ASP.NET vagy ASP.NET Core alkalmazásban használt automatikus konfigurációhoz, `HttpContext`levonva belőle a telemetriai azonosítókat.

Az application insights SDK a feldolgozó szolgáltatás az alapértelmezett konfiguráció módosításához testreszabhatja. Az Application Insights ASP.NET Core SDK felhasználói ASP.NET Core beépített [függőségi injektálással](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)ismerhetik a konfiguráció módosítását. A WorkerService SDK is hasonló elveken alapul. Szinte az összes konfigurációs módosítást a `IServiceCollection`szakaszban a megfelelő metódusok hívásával, az `ConfigureServices()` alábbiakban részletezett módon.

> [!NOTE]
> Az SDK használata közben a `TelemetryConfiguration.Active` konfiguráció módosítása nem támogatott, és a módosítások nem jelennek meg.

### <a name="using-applicationinsightsserviceoptions"></a>Az ApplicationInsightsServiceOptions használata

Néhány gyakori beállítást módosíthat, `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetryWorkerService`ha átadja a t, ahogy ebben a példában is:

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

Vegye `ApplicationInsightsServiceOptions` figyelembe, hogy ebben az SDK `Microsoft.ApplicationInsights.WorkerService` a `Microsoft.ApplicationInsights.AspNetCore.Extensions` névtérben, szemben a ASP.NET Core SDK.

Gyakran használt beállítások`ApplicationInsightsServiceOptions`

|Beállítás | Leírás | Alapértelmezett
|---------------|-------|-------
|EnableQuickPulseMetricStream | LiveMetrics szolgáltatás engedélyezése/letiltása | igaz
|EnableAdaptiveSampling | Adaptív mintavételezés engedélyezése/letiltása | igaz
|Szívverés engedélyezése | Engedélyezés/letiltás a szívverések szolgáltatás, amely rendszeres időközönként (15 perces alapértelmezett) küld egy egyéni metrika nevű "HeartBeatState" információkkal a futásidejű, mint a .NET-verzió, az Azure Environment információk, ha van ilyen, stb. | igaz
|AddAutoCollectedMetric Extractor | AutoCollectedMetrics extractor engedélyezése/letiltása, amely egy TelemetryProcessor, amely előre összesített metrikákat küld a kérelmekről/függőségekről a mintavételezés előtt. | igaz

Tekintse meg a [konfigurálható beállításokat `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) a legfrissebb listát.

### <a name="sampling"></a>Mintavételezés

Az Application Insights SDK a dolgozói szolgáltatás támogatja a rögzített és adaptív mintavételezési támogatja. Az adaptív mintavételezés alapértelmezés szerint engedélyezve van. A munkavégző szolgáltatás mintavételezésének konfigurálása ugyanúgy történik, mint [ASP.NET alapalkalmazások esetében.](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications)

### <a name="adding-telemetryinitializers"></a>Telemetriai initializerek hozzáadása

[Telemetriai inicializálók](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) használata, ha meg szeretné határozni az összes telemetriai adatokkal küldött tulajdonságokat.

Adjon hozzá `TelemetryInitializer` újat a tárolóhoz, és `DependencyInjection` az `TelemetryConfiguration`SDK automatikusan hozzáadja őket a hoz.

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>TelemetriaI initializerek eltávolítása

Telemetriai inicializálók alapértelmezés szerint jelen vannak. Az összes vagy adott telemetriai inicializáló eltávolításához használja a következő mintakódot a hívás *után.* `AddApplicationInsightsTelemetryWorkerService()`

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

### <a name="adding-telemetry-processors"></a>Telemetriai processzorok hozzáadása

Egyéni telemetriai processzorokat `TelemetryConfiguration` adhat hozzá a `AddApplicationInsightsTelemetryProcessor` `IServiceCollection`bővítménymetódus használatával. Telemetriai processzorok [speciális szűrési forgatókönyvekben,](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer) hogy lehetővé tegye a közvetlenebb szabályozását, hogy mi szerepel, vagy ki van zárva az Application Insights szolgáltatásba küldött telemetriai adatok. Használja a következő példát.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Alapértelmezett Telemetriai modulok konfigurálása vagy eltávolítása

Az Application Insights telemetriai modulok at használ, hogy automatikusan összegyűjtse a telemetriai adatokat az adott számítási feladatok manuális nyomon követés nélkül.

A következő automatikus begyűjtési modulok alapértelmezés szerint engedélyezve vannak. Ezek a modulok felelősek a telemetriai adatok automatikus gyűjtéséért. Letilthatja vagy beállíthatja őket alapértelmezett viselkedésük módosításához.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Az alapértelmezett `TelemetryModule`beállításokhoz használja `ConfigureTelemetryModule<T>` a `IServiceCollection`bővítménymetódust a alkalmazásban, ahogy az a következő példában látható.

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

### <a name="configuring-telemetry-channel"></a>Telemetriai csatorna konfigurálása

Az alapértelmezett `ServerTelemetryChannel`csatorna a . Felülírhatja, ahogy a következő példa mutatja.

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

### <a name="disable-telemetry-dynamically"></a>Telemetriai adatok dinamikus letiltása

Ha azt szeretné, hogy a telemetriai adatok `TelemetryConfiguration` feltételesen és dinamikusan letiltása, `DisableTelemetry` feloldhatja példány ASP.NET Core függőségi injektálási tároló bárhol a kódban, és állítsa be a jelzőt rajta.

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

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Hogyan követhetem nyomon a nem automatikusan gyűjtött telemetriai adatokat?

Kap egy `TelemetryClient` példány segítségével konstruktor `TrackXXX()` injekció, és hívja a szükséges módszert rajta. Nem javasoljuk új `TelemetryClient` példányok létrehozását. Egy singleton `TelemetryClient` példány már regisztrálva van `DependencyInjection` `TelemetryConfiguration` a tárolóban, amely megosztja a többi telemetriai adatok. Új `TelemetryClient` példány létrehozása csak akkor ajánlott, ha szüksége van egy konfiguráció, amely elkülönül a többi telemetriai adatok.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Használhatom a Visual Studio IDE-t az Application Insights szolgáltatásra való bejuttatására egy Munkavégző szolgáltatás projektbe?

A Visual Studio IDE bevezetés jelenleg csak ASP.NET/ASP.NET Core Applications esetében támogatott. Ez a dokumentum akkor frissül, amikor a Visual Studio támogatja a bevezetési munkavégző szolgáltatás alkalmazásokat.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Engedélyezhetem az Application Insights figyelését olyan eszközökkel, mint az Állapotfigyelő?

Nem. [Az Állapotfigyelő](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) és [az Állapotfigyelő 2-es v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) jelenleg csak ASP.NET 4.x-es támogatást nyújt.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Ha linuxos anamilyen-t futtatok, minden funkció támogatott?

Igen. Az SDK funkciótámogatása minden platformon azonos, a következő kivételekkel:

* A teljesítményszámlálók csak a Windows rendszerben támogatottak, kivéve az élő metrikákban látható process CPU/Memory értékeket.
* Annak `ServerTelemetryChannel` ellenére, hogy alapértelmezés szerint engedélyezve van, ha az alkalmazás Linux vagy MacOS rendszerben fut, a csatorna nem hoz létre automatikusan egy helyi tárolómappát, hogy a telemetriai adatok ideiglenesen megmaradjanak, ha hálózati problémák merülnek fel. E korlátozás miatt a telemetriai adatok elvesznek, ha ideiglenes hálózati vagy kiszolgálói problémák merülnek fel. A probléma kerülő megoldásához konfiguráljon egy helyi mappát a csatornához:

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

[.NET Core konzolalkalmazás](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Ezt a mintát akkor használja, ha .NET Core (2.0 vagy újabb) vagy .NET Framework (4.7.2 vagy újabb) nyelven írt konzolalkalmazást használ.

[ASP .NET Alapvető háttérfeladatok hostedservices szolgáltatással](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Akkor használja ezt a mintát, ha Asp.Net Core 2.1/2.2-ben van, és háttérfeladatokat hoz létre a hivatalos [útmutatás](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2) nak

[.NET Core 3.0 dolgozói szolgáltatás](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Akkor használja ezt a mintát, ha a .NET Core 3.0 Worker Service alkalmazással rendelkezik az [itt](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template) található hivatalos útmutatás nak

## <a name="open-source-sdk"></a>Nyílt forráskódú SDK

[Olvassa el, és hozzájárul a kódot](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="next-steps"></a>További lépések

* [Az API-val](../../azure-monitor/app/api-custom-events-metrics.md) elküldheti saját eseményeit és mutatóit az alkalmazás teljesítményének és használatának részletes megtekintéséhez.
* [További függőségek nyomon követése, amelyeket nem követtek nyomon automatikusan.](../../azure-monitor/app/auto-collect-dependencies.md)
* [Az automatikusan gyűjtött telemetriai adatok gazdagítása vagy szűrése](../../azure-monitor/app/api-filtering-sampling.md).
* [Függőség injekció ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).
