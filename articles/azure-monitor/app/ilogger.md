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
ms.openlocfilehash: c456f8f7f08fdbd0020bfc49ceeec262fa0ac773
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56457978"
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
        logger.LogInformation("From Program. Running the host now.."); // This will be picked up up by AI
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
    
    // The following be picked up up by Application Insights.
        _logger.LogInformation("From ConfigureServices. Services.AddMVC invoked"); 
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
        // The following be picked up up by Application Insights.   
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following be picked up up by Application Insights.
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
        // All the following logs will be picked upby Application Insights.
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
```

Noha egy ASP.NET Core-alkalmazás a fenti módszer használható, több általánosan használt megközelítés lehet úgy, hogy rendszeres alkalmazásfigyelés (kérelmeket, függőségeket stb.) ILogger rögzítése az alább látható módon.

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

## <a name="next-steps"></a>További lépések

További információk:

- [Alapú ILogger naplózása](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
- [.NET-nyomkövetési naplók](../../azure-monitor/app/asp-net-trace-logs.md)
