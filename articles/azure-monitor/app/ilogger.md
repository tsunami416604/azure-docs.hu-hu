---
title: Ismerkedés a .NET hívásláncnaplók megtekintése az Azure Application Insights-ILogger
description: Az Azure Application Insights ILogger megvalósítási használata az ASP.NET Core- és konzolalkalmazásokat mintáit.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: 4c385d2af0d9e4e213cd690b3c30d8719588220d
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58399496"
---
# <a name="ilogger"></a>ILogger

ASP.NET Core támogatja, amely együttműködik a beépített és külső naplózási szolgáltatók különböző naplózási API-t. Ez a cikk bemutatja, hogyan kezelje a naplózás az Application Insights ILogger megvalósításra is konzolon, és az ASP.NET Core-alkalmazások. Alapú ILogger naplózásával kapcsolatos további tudnivalókért lásd: [Ez a cikk](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="console-application"></a>Konzolalkalmazás

Az alábbiakban látható egy minta-Konzolalkalmazás ILogger elküldheti az Application Insightsba konfigurálva.

Telepített csomagok:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(loggingBuilder =>
        {
        // Optional: Apply filters to configure LogLevel Trace or above is sent to ApplicationInsights for all
        // categories.
        loggingBuilder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
            loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");                
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional. Epecially in case of AspNetCore request info is already
        // present in scope.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

## <a name="aspnet-core-application"></a>ASP.NET Core application

Következő ASP.NET Core-alkalmazást konfigurált ILogger nyomkövetések küldése az application insightsba mintáját szemlélteti. Ebben a példában a ILogger elküldheti a Program.cs, Startup.cs vagy bármely más Contoller/alkalmazás logikáját kell követni.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = BuildWebHost(args);
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        logger.LogInformation("From Program. Running the host now.."); // This will be picked up by AI
        host.Run();
    }

    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(logging =>
        {
            logging.AddApplicationInsights("ikeyhere");

            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // ApplicationInsights for all categories.
            logging.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);

            // Additional filtering For category starting in "Microsoft",
            // only Warning or above will be sent to Application Insights.
            logging.AddFilter<ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Warning);
        })
        .Build();
}
```

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

        // The following will be picked up by Application Insights.
        _logger.LogInformation("From ConfigureServices. Services.AddMVC invoked"); 
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
        // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
            _logger.LogInformation("An example of a Information trace..");
            _logger.LogWarning("An example of a Warning trace..");
            _logger.LogTrace("An example of a Trace level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

A fenti, a különálló csomag mindkét példákban Microsoft.Extensions.Logging.ApplicationInsights szolgál. Alapértelmezés szerint ez a konfiguráció használ a legalább `TelemetryConfiguration` adatokat küld az Application Insights. Legalább azt jelenti, hogy a csatorna használt lesz `InMemoryChannel`, nem a mintavételi és a nem szabványos TelemetryInitializers. Ez a viselkedés felülbírálható a egy olyan konzolalkalmazást, az alábbi példában látható módon.

Ez a csomag telepítéséhez:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

A következő szakasz bemutatja, hogyan az alapértelmezett `TelemetryConfiguration`. Ez a példa konfigurálja `ServerTelemetryChannel`, mintavételi, és a egy egyéni `ITelemetryInitializer`.

```csharp
    // Create DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding ApplicationInsights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly call Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if application terminates, telemetry is sent to the back-end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

Noha a fenti módszer is használható, valamint egy ASP.NET Core-alkalmazás, több általánosan használt megközelítés lehet úgy, hogy rendszeres alkalmazásfigyelés (kérelmeket, függőségeket stb.) ILogger rögzítése az alább látható módon.

Ez a csomag telepítéséhez:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
```

Adja hozzá a következőt a `ConfigureServices` metódust. Ez a kód lehetővé teszi reguláris alkalmazásfigyelési alapértelmezett konfigurációval (ServerTelemetryChannel LiveMetrics, kérelem/függőségek, korrelációs stb.)

```csharp
services.AddApplicationInsightsTelemetry("ikeyhere");
```

Ebben a példában a konfiguráció által használt `ApplicationInsightsLoggerProvider` ugyanaz, mint rendszeres alkalmazásfigyelés. Ezért mindkét `ILogger` nyomkövetéseket és egyéb telemetriát (kérelmeket, függőségeket stb.) is indulnak ugyanazokat a `TelemetryInitializers`, `TelemetryProcessors`, és `TelemetryChannel`. Ezek korrelált és ugyanúgy mintavételezés mintavételezés/nem lesz.

Van azonban kivételt képez ez a viselkedés. Az alapértelmezett `TelemetryConfiguration` nem teljes mértékben állítsa be, mikor naplózza a `Program.cs` vagy `Startup.cs` magát, így ezeket a naplókat nem az alapértelmezett konfigurációban fog működni. Azonban minden más log (például naplók a tartományvezérlők, modellek stb.) a konfigurációs megosztásához.

## <a name="control-logging-level"></a>Naplózási szint vezérlési

Szereplőkkel a kódra, mint a fenti példák naplók szűrése, lehetőség arra is rögzíti az Application Insights naplózási szint módosításával szabályozhatja a `appsettings.json`. A [ASP.NET-naplózás – alapok dokumentáció](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering) szemlélteti, hogyan lehet ezt elérni. Kifejezetten az Application Insights, a szolgáltató aliasnevet van `ApplicationInsights`, ahogyan az az alábbi példában, amely konfigurálja `ApplicationInsights` csak a naplók rögzítése `Warning` és a fenti az összes kategóriából származó.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

*Látok néhány ILogger naplók jelennek meg kétszer az Application insights szolgáltatásban?*

* Ez akkor lehetséges, ha a régebbi (most már elavult) verziójával rendelkezik `ApplicationInsightsLoggerProvider` meghívásával engedélyezve `AddApplicationInsights` a `ILoggerFactory`. Ellenőrizze, hogy a `Configure` módszert a következő, és távolítsa el azt.

   ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
        // ..other code.
    }
   ```

* Ha kettős naplózási tapasztal, a Visual Studióban történő hibakeresése során, majd módosítsa ApplicationInsights módon, azzal lehetővé teszi, hogy a kód `EnableDebugLogger` hamisnak bizonyult. Ez a tulajdonság csak megfelelő a hibakeresés során.

   ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
        options.EnableDebugLogger = false;
        services.AddApplicationInsightsTelemetry(options);
        // ..other code.
    }
   ```



## <a name="next-steps"></a>További lépések

További információk:

- [Alapú ILogger naplózása](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
- [.NET-nyomkövetési naplók](../../azure-monitor/app/asp-net-trace-logs.md)
