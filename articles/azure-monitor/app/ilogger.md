---
title: Ismerkedés a .NET hívásláncnaplók megtekintése az Azure Application Insights-ILogger
description: Az Azure Application Insights ILogger szolgáltató használata az ASP.NET Core- és konzolalkalmazásokat mintáit.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: ea7f2e730b4963016d221705ba8c9356efffa858
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905273"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>A .NET Core ILogger naplókhoz ApplicationInsightsLoggerProvider

ASP.NET Core támogatja, amely együttműködik a különféle beépített és külső naplózási szolgáltatók naplózási API-t. Naplózás végzi el hívása Log() vagy valamely változatában `ILogger` példányok. Ez a cikk bemutatja, hogyan `ApplicationInsightsLoggerProvider` rögzítése `ILogger` naplózza a konzol és az ASP.NET Core-alkalmazások is. Ez a cikk is ismerteti, hogyan `ApplicationInsightsLoggerProvider` integrálva van a más Application Insights telemetria.
További naplózás az Asp.Net Core kapcsolatban lásd: [Ez a cikk](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET Core applications

Kezdve [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) frissítésétől kezdve a verzió 2.7.0-beta3 `ApplicationInsightsLoggerProvider` alapértelmezés szerint engedélyezve van, ha engedélyezve van a normál Application insights általi figyelés vagy a szabványos módszerek - használatával hívó `UseApplicationInsights` IWebHostBuilder a metódust vagy `AddApplicationInsightsTelemetry` IServiceCollection a metódust. `ILogger` naplók által rögzített `ApplicationInsightsLoggerProvider` más gyűjtött telemetria azonos konfigurációjú vonatkoznak. i.e Ugyanezek a rendelkeznek `TelemetryInitializer`s, `TelemetryProcessor`s, ugyanazokkal a `TelemetryChannel`, és a rendszer korrelált és mintát venni, ugyanúgy, mint minden más telemetriai adatokat.  A SDK vagy újabb verziója van, akkor a további lépésekre nincs szükség rögzítése `ILogger` naplókat.

Alapesetben csak `ILogger` kijelentkezik a `Warning` vagy újabb (az összes kategóriába tartozó) érkeznek az Application Insights. Ez a viselkedés módosítható szűrők alkalmazásával, ahogy [Itt](#control-logging-level). Emellett további lépésekre szükség, ha `ILogger` származó naplók `Program.cs` vagy `Startup.cs` rögzíthetők látható módon, hogy [Itt](#capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications).

Ha egy Microsoft.ApplicationInsights.AspNet SDK korábbi verzióját használja, vagy csak használandó ApplicationInsightsLoggerProvider, anélkül, hogy bármely más Application Insights-figyelést, kövesse az alábbi lépéseket.

1. Telepítse a nuget-csomagot.

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
    </ItemGroup>
```

2. módosítsa `Program.cs` látható

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
            builder =>
            {
                // Providing an instrumentation key here is required if you are using
                // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                // or if you want to capture logs from early in the application startup
                // pipeline from Startup.cs or Program.cs itself.
                builder.AddApplicationInsights("ikey");

                // Optional: Apply filters to control what logs are sent to Application Insights.
                // The following configures LogLevel Information or above to be sent to
                // Application Insights for all categories.
                builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                 ("", LogLevel.Information);
            }
        );
}
```

A fenti kód konfigurálja `ApplicationInsightsLoggerProvider`. Következő jeleníti meg a vezérlő osztályhoz, amely használja példaként `ILogger` elküldeni a naplókat, amelyek ApplicationInsights által rögzített.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications"></a>A Startup.cs, az Asp.Net Core-alkalmazások Program.cs ILogger naplók rögzítése

Az új ApplicationInsightsLoggerProvider korai szakaszában az alkalmazás indítási folyamat származó naplók rögzítése lehetősége. Még akkor is, ApplicationInsightsLoggerProvider viszont automatikusan engedélyezve van az Application Insights (a 2.7.0-beta3 és újabb verziók esetében), nincs kialakítási kulcs beállítása a folyamatban, ezért csak naplók vezérlőről az újabb, illetve más osztályok fog rögzíthetők. Minden kezdve naplók rögzítése `Program.cs` és `Startup.cs` , egy explicit módon engedélyeznie kell ApplicationInsightsLoggerProvider és a egy kialakítási kulcsot. Emellett az is fontos megjegyezni, hogy `TelemetryConfiguration` nem teljes mértékben állítsa be, mikor naplózza a `Program.cs` vagy `Startup.cs` magát, így ezeket a naplókat egy operációs rendszer nélküli helyreállításra, InMemoryChannel, nincs mintavételi és nem a normál telemetriai minimális konfigurációt fogja használni. inicializátory vagy a processzorok.

Példákat mutat be a következő `Program.cs` és `Startup.cs` a funkció használata.

#### <a name="example-programcs"></a>Példa Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // providing an instrumentation key here is required if you are using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Program.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Program", LogLevel.Trace);
            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Startup.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Startup", LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Példa Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
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

## <a name="migrating-from-old-applicationinsightsloggerprovider"></a>Régi ApplicationInsightsLoggerProvider-ről

2.7.0-beta2, mielőtt Microsoft.ApplicationInsights.AspNet SDK-verziók támogatott a naplózás szolgáltató, amely már elavult. Ez a szolgáltató engedélyezve lett a `AddApplicationInsights()` bővítmény metódusa `ILoggerFactory`. Ez a szolgáltató már elavult, és a felhasználók áttelepítése az új szolgáltató használata javasolt. Áttelepítés két lépésből áll.

1. Távolítsa el a ILoggerFactory.AddApplicationInsights() hívás `Startup.Configure()` metódus dupla naplózási elkerülése érdekében.
2. Adja meg újból a kódban szűrési szabályokat mivel azok nem is érvényesek az új szolgáltató által. ILoggerFactory.AddApplicationInsights() túlterhelésekkel minimális LogLevel vagy szűrő funkciók vett igénybe. Az új szolgáltatót, a szűrési része a naplózási keretrendszer magát, és az Application Insights-szolgáltató nem végzi. Így minden olyan szűrők keresztül megadott `ILoggerFactory.AddApplicationInsights()` túlterheléssel el kell távolítani, és a szűrési szabályok meg kell adni a következő [ezek](#control-logging-level) utasításokat. Ha `appsettings.json` naplózási szűréséhez, továbbra is működni fog az új szolgáltató is használja az ugyanazon szolgáltató Alias - **ApplicationInsights**.

Amíg a régi szolgáltató továbbra is használható (azt most már elavult, és csak a 3.xx főverzió módosítása törlődni fog), újabb szolgáltatóba való migrálás erősen ajánlott a következő okok miatt.

1. Előző szolgáltatót nem tartalmazott támogatási [hatókörök](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). Az új szolgáltató tulajdonságai hatókörből automatikusan hozzáadódnak egyéni tulajdonságok formájában gyűjtött telemetriai adatokat.
2. Naplók mostantól a az alkalmazás indítási folyamat jóval korábbi rögzíthetők. i.e Program és az indítási osztály naplóinak most rögzíthetők.
3. Az új szolgáltató a kerül sor a keretrendszer szintjén magát. Naplók szűrése az Application Insights-szolgáltató végezhető, pontosan ugyanúgy, mint más szolgáltatók, beleértve a beépített szolgáltatók, például a konzolon, a Debug stb. Akkor is több szolgáltató azonos szűrőket alkalmaz.
4. A [ajánlott](https://github.com/aspnet/Announcements/issues/255) naplózási szolgáltatók engedélyezése az Asp.Net Core (2.0-s és újabb verziók esetében) úgy, hogy a ILoggingBuilder bővítmény módszerekkel `Program.cs` magát.

> [!Note]
Az új szolgáltató érhető el az alkalmazások `NETSTANDARD2.0` vagy újabb verziója. Ha az alkalmazás például a .NET Core 1.1 régebbi .NET Core-verzió van-e állítva, vagy ha a .NET-keretrendszer célzó, továbbra is a régi szolgáltatóval.

## <a name="console-application"></a>Konzolalkalmazás

A következő kód bemutatja egy minta-Konzolalkalmazás küldéséhez konfigurált `ILogger` nyomkövetéseket az Application Insightsba.

Telepített csomagok:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
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
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
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

A fenti példában, a különálló csomag `Microsoft.Extensions.Logging.ApplicationInsights` szolgál. Alapértelmezés szerint ez a konfiguráció használ a legalább `TelemetryConfiguration` adatokat küld az Application Insights. Legalább azt jelenti, hogy a csatorna használt lesz `InMemoryChannel`, nem a mintavételi és a nem szabványos TelemetryInitializers. Ez a viselkedés felülbírálható a egy olyan konzolalkalmazást, az alábbi példában látható módon.

Ez a csomag telepítéséhez:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

A következő szakasz bemutatja, hogyan az alapértelmezett `TelemetryConfiguration` használatával `services.Configure<TelemetryConfiguration>()` metódust. Ebben a példában állít be `ServerTelemetryChannel`, mintavétel és hozzáadja az egyéni `ITelemetryInitializer` , a `TelemetryConfiguration`.

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

    // Add the logging pipelines to use. We are adding Application Insights only.
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

## <a name="control-logging-level"></a>Naplózási szint vezérlési

Az Asp.Net Core `ILogger` infrastruktúra rendelkezik beépített mechanizmus a alkalmazni [szűrés](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering) naplók, amely lehetővé teszi a felhasználóknak minden egyes regisztrált szolgáltatót, beleértve az Application Insights-szolgáltató küldött naplók szabályozhatja. Ez a szűrés történhet konfigurációban (általában használatával `appsettings.json` fájl) vagy a kódban. Ennek a lehetőségnek a keretrendszer maga biztosítja, és nem adott Application Insights-szolgáltatóhoz.

Az alábbi táblázat a példák az Állapotszűrő szabályok alkalmazására ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Állapotszűrő-szabályok létrehozására a konfigurációban az appSettings.JSON fájl

A szolgáltató alias a ApplicationInsightsLoggerProvider, `ApplicationInsights`. Az alább látható szakaszt a `appsettings.json` naplók konfigurálása `Warning` és a fenti az összes kategóriából származó `Error` és a fenti kell küldeni a "Microsoft" kezdetű kategóriákból `ApplicationInsightsLoggerProvider`.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>Állapotszűrő szabályok létrehozása a code-ban

Az alábbi kód kódtöredék úgy konfigurálja naplók `Warning` és a fenti az összes kategóriából származó `Error` és a fenti kell küldeni a "Microsoft" kezdetű kategóriákból `ApplicationInsightsLoggerProvider`. A konfigurációs ugyanaz, mint a fenti konfigurációs alatt `appsettings.json`.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

*1. Mi az a korábbi és új ApplicationInsightsLoggerProvider?*

* [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) mellékeltük egy beépített ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), amely engedélyezve lett ILoggerFactory használatával bővítő metódusok. Ez a szolgáltató 2.7.0-beta2 és újabb verziók esetében a elavultként van megjelölve, és el lesz távolítva az teljesen főverzió módosítása. [Ez](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) csomag maga nem elavult, és a kérelmeket, függőségeket stb figyelés engedélyezése szükséges.

* A javasolt alternatív az új önálló csomag [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), tartalmazó egy továbbfejlesztett ApplicationInsightsLoggerProvider () Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider), és engedélyezve van, bővítmények ILoggerBuilder metódust.

* [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) verzió 2.7.0-beta3 kezdve függőséget a fenti csomagot a, és lehetővé teszi, hogy `ILogger` automatikusan rögzítheti.

*2. Néhány látok `ILogger` naplók jelennek meg kétszer az Application insights szolgáltatásban?*

* A duplikáció lehetőség, ha a régebbi (most már elavult) verziójával rendelkezik `ApplicationInsightsLoggerProvider` meghívásával engedélyezve `AddApplicationInsights` a `ILoggerFactory`. Ellenőrizze, hogy a `Configure` módszert a következő, és távolítsa el azt.

   ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
        // ..other code.
    }
   ```

* Ha tapasztal a kétszeres naplózás, a Visual Studióban történő hibakeresése során, majd módosítsa az Application Insights módon, azzal lehetővé teszi, hogy a kód `EnableDebugLogger` hamisnak bizonyult. Duplikált problémáról és a javítás, amelynek csak az alkalmazás hibakeresését.

   ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
        options.EnableDebugLogger = false;
        services.AddApplicationInsightsTelemetry(options);
        // ..other code.
    }
   ```

*3. Tudom a frissített [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) verzió 2.7.0-beta3, és most már jelent meg, hogy a naplók `ILogger` automatikusan rögzítve lesznek. Hogyan lehet a kikapcsolása után ez a szolgáltatás teljesen?*

* Lásd: [ez](../../azure-monitor/app/ilogger.md#control-logging-level) tudni, hogyan szűrheti a szakasz az általános naplózza. Használja a szülőpéldánya ApplicationInsightsLoggerProvider `LogLevel.None` hozzá.

  A code-ban

    ```csharp
        builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                          ("", LogLevel.None);
    ```

  A config

    ```json
    {
      "Logging": {
        "ApplicationInsights": {
          "LogLevel": {
            "Default": "None"
          }
    }
    ```

*4. Néhány látok `ILogger` naplók nem kellene azonos tulajdonságokkal másokkal?*

* Application Insights rögzíti és küld `ILogger` naplók azonos `TelemetryConfiguration` használt minden egyéb telemetriai adatokat. Nincs-e szabály alól. Az alapértelmezett `TelemetryConfiguration` nem teljes mértékben állítsa be, mikor naplózza a `Program.cs` vagy `Startup.cs` magát, így ezek a helyek naplóinak nem fog az alapértelmezett konfiguráció, és ezért nem fut az összes a `TelemetryInitializer`s és `TelemetryProcessor`s.

*5. Az Application Insights telemetria típusa előállított `ILogger` naplók? vagy ahol látható `ILogger` naplók az Application Insights?*

* ApplicationInsightsLoggerProvider rögzíti `ILogger` naplózza, és létrehoz `TraceTelemetry` eszközökről. Ha egy kivétel objektum van a Log() metódusnak átadott a ILogger, majd helyett `TraceTelemetry`, egy `ExceptionTelemetry` jön létre. Ezek az elemek telemetriai bármely más, ugyanazon a helyen található `TraceTelemetry` vagy `ExceptionTelemetry` az Application Insights, beleértve a portal, az elemzésekhez, vagy a Visual Studio ladicího.
Ha inkább elküldése mindig `TraceTelemetry`, majd használja a kódrészlet ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```.

*5. Nem rendelkezem az SDK-t, és az Azure webalkalmazás-bővítmény használatával saját Asp.Net Core-alkalmazásokhoz az Application Insights engedélyezése. Hogyan használhatom az új szolgáltató?*

* Application Insights-bővítmény használata az Azure Web App a régi szolgáltatót használja. A szűrési szabályok módosítható `appsettings.json` az alkalmazáshoz. Ha azt szeretné, hogy igénybe vehesse az új szolgáltató használatához buildelés instrumentation nuget függőségi véve a SDK-val. Ez a dokumentum frissül, ha a bővítmény átvált az új szolgáltató használatára.

*6. A különálló csomag Microsoft.Extensions.Logging.ApplicationInsights használatával vagyok, és Application Insights-szolgáltató által hívó builder engedélyezése. AddApplicationInsights("ikey"). Kialakítási kulcs lekérése konfigurációs lehetőség van?*


* Módosítsa `Program.cs` és `appsettings.json` alább látható módon.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>()
            .ConfigureLogging((hostingContext, logging) =>
            {
                // hostingContext.HostingEnvironment can be used to determine environments as well.
                var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                logging.AddApplicationInsights(appInsightKey);
            });
}
```

A megfelelő szakasz `appsettings.json`

```json
{
  "myikeyfromconfig": "putrealikeyhere"
}
```

A fenti kód csak önálló naplózási szolgáltató használata esetén szükséges. Normál Application insights általi figyelés, a kialakítási kulcsot automatikusan betöltődik a konfigurációs elérési `ApplicationInsights:Instrumentationkey` és `appsettings.json` alábbihoz hasonlóan kell megjelennie.

```json
{
  "ApplicationInsights":
    {
        "Instrumentationkey":"putrealikeyhere"
    }
}
```

## <a name="next-steps"></a>További lépések

További információk:

* [Az Asp.Net Core-naplózás](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [.NET-nyomkövetési naplókat az Application insights szolgáltatásban](../../azure-monitor/app/asp-net-trace-logs.md)
