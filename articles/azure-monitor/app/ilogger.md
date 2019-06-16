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
ms.openlocfilehash: 0691c35661a6d185a6aa5ed3383ad600653359d3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058598"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>A .NET Core ILogger naplókhoz ApplicationInsightsLoggerProvider

ASP.NET Core támogatja, amely együttműködik a különféle beépített és külső naplózási szolgáltatók naplózási API-t. Naplózási történik meghívásával **Log()** vagy valamely változatában, azt az *ILogger* példányok. Ez a cikk bemutatja, hogyan használható *ApplicationInsightsLoggerProvider* konzolon és az ASP.NET Core-alkalmazások ILogger naplók rögzítésére. Ez a cikk azt is bemutatja, hogyan integrálható a ApplicationInsightsLoggerProvider más Application Insights telemetria.
További tudnivalókért lásd: [az ASP.NET Core-naplózás](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET Core applications

Alapértelmezés szerint engedélyezve van a ApplicationInsightsLoggerProvider [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) verzió 2.7.0-beta3 (és újabb) Ha bekapcsolja a normál Application insights általi figyelés vagy a standard szintű csomag segítségével módszerek:
- Meghívásával a **UseApplicationInsights** IWebHostBuilder bővítmény metódus 
- Meghívásával a **AddApplicationInsightsTelemetry** IServiceCollection bővítmény metódus

ApplicationInsightsLoggerProvider rögzítő naplók ILogger ugyanazt a konfigurációt, mint bármely más gyűjtött telemetria vonatkozik. Ezek ugyanazoknak a TelemetryInitializers és TelemetryProcessors, az azonos TelemetryChannel és közötti kapcsolatot, majd az adatgyűjtés ugyanúgy, mint az egyéb telemetriai adatokat. Ha a verzió 2.7.0-beta3 használ, vagy később, semmit nem kell ILogger naplók rögzítése.

Csak *figyelmeztetés* vagy magasabb ILogger naplók (az összes kategóriába tartozó) alapértelmezés szerint az Application Insightsnak küldött. De [a alkalmazni annak érdekében, hogy ez a viselkedés](#control-logging-level). További lépések szükségesek ILogger származó naplók rögzítése **Program.cs** vagy **Startup.cs**. (Lásd: [ILogger rögzítése a Startup.cs és Program.cs naplók, az ASP.NET Core-alkalmazások](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Ha Microsoft.ApplicationInsights.AspNet SDK egy korábbi verzióját használja, vagy bármely más Application insights általi figyelés nélkül ApplicationInsightsLoggerProvider csak használandó, használja az alábbi eljárást:

1. Telepítse a NuGet-csomagot:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Módosítsa **Program.cs** itt látható módon:

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
                   // Providing an instrumentation key here is required if you're using
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

A kód a 2. lépésben konfigurálja `ApplicationInsightsLoggerProvider`. A következő kód bemutatja a vezérlő osztályhoz, amely használja példaként `ILogger` elküldeni a naplókat. A naplók az Application Insights által rögzített.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Az ASP.NET Core-alkalmazások a Startup.cs és Program.cs ILogger naplók rögzítése

> [!NOTE]
> Az ASP.NET Core 3.0-s és újabb már nem beszúrható `ILogger` a Startup.cs és a program.cs fájlban. Lásd: https://github.com/aspnet/Announcements/issues/353 további részletekért.

Az új ApplicationInsightsLoggerProvider rögzítheti az alkalmazás-indítási folyamatban korai származó naplók. Bár ApplicationInsightsLoggerProvider automatikusan engedélyezve van az Application Insights (verzió 2.7.0-beta3 kezdve), akkor nincs egy kialakítási kulcs beállítása felfelé mindaddig, amíg a folyamat későbbi részében. Ezért csak naplózza a **vezérlő**/ egyéb osztályok fog rögzíthetők. Kezdve minden napló rögzítéséhez **Program.cs** és **Startup.cs** önmagában, külön engedélyeznie kell egy kialakítási kulcsot a ApplicationInsightsLoggerProvider. Ezenkívül *TelemetryConfiguration* telepítése nem teljes mértékben be való bejelentkezéskor **Program.cs** vagy **Startup.cs** magát. Ezért ezeket a naplókat használó InMemoryChannel, nincs mintavételezése esetén, és nincs normál telemetriai inicializálók vagy a processzorok minimális konfigurációban fog működni.

A következő példák azt mutatják be, ez a funkció a **Program.cs** és **Startup.cs**.

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
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Telepítse át a régi ApplicationInsightsLoggerProvider

Microsoft.ApplicationInsights.AspNet SDK-verziókra 2.7.0-beta2 támogatva a naplózás szolgáltató, amelyik most már elavult. Ez a szolgáltató keresztül lett engedélyezve az **AddApplicationInsights()** ILoggerFactory a metódust. Javasoljuk, hogy telepít át az új szolgáltatót, amely két lépésből áll:

1. Távolítsa el a *ILoggerFactory.AddApplicationInsights()* , hívja meg a **Startup.Configure()** metódus dupla naplózási elkerülése érdekében.
2. Adja meg újból a kódban, szűrési szabályokat mivel azok nem is érvényesek az új szolgáltató által. A túlterhelések *ILoggerFactory.AddApplicationInsights()* minimális LogLevel vagy szűrő funkciók vett igénybe. Az új szolgáltatót, a szűrési része, a naplózási keretrendszer maga. Ez az Application Insights-szolgáltató nem végezhető el. Így minden olyan szűrők keresztül biztosított *ILoggerFactory.AddApplicationInsights()* túlterheléssel el kell távolítani. Szűrési szabályok meg kell adni a következő, és a [vezérelheti a naplózási szint](#control-logging-level) utasításokat. Ha *appsettings.json* naplózási szűréséhez, továbbra is dolgozhat az új szolgáltatót, mindkettő használja az ugyanazon szolgáltató alias, mert *ApplicationInsights*.

A régi szolgáltató továbbra is használhatja. (Ez fog szűnni csak 3 főverzió módosítása. *xx*.) De azt javasoljuk, hogy át az új szolgáltató a következő okok miatt:

- Az előző szolgáltató támogatása nem rendelkezik [hatókörök jelentkezzen](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). Az új szolgáltató tulajdonságai hatókörből automatikusan hozzáadódnak egyéni tulajdonságok formájában gyűjtött telemetriai adatokat.
- Naplók mostantól a az alkalmazás indítási folyamat jóval korábbi rögzíthetők. A naplók a **Program** és **indítási** osztályok most rögzíthetők.
- Az új szolgáltató kerül sor a keretrendszer szintjén magát. Szűrés ugyanúgy, mint más szolgáltatók, beleértve a beépített szolgáltatók, például a konzolon, a Debug naplók az Application Insights-szolgáltatóhoz, és így tovább. Több szolgáltató alkalmazhatja ugyanazokat a szűrőket is.
- Az ASP.NET Core (2.0-s és újabb verziók), ajánlott [naplózási szolgáltatók engedélyezése](https://github.com/aspnet/Announcements/issues/255) bővítő metódusok használata a ILoggingBuilder a **Program.cs** magát.

> [!Note]
> Az új szolgáltató az elérhető alkalmazások, amelyek NETSTANDARD2.0 vagy újabb. Ha az alkalmazás a .NET Core régebbi verziójú, mint például a .NET Core 1.1-es célozza, vagy célozza meg, hogy a .NET-keretrendszer, továbbra is a régi szolgáltatóval.

## <a name="console-application"></a>Konzolalkalmazás

A következő kód bemutatja egy minta-Konzolalkalmazás, amely konfigurálva van ILogger elküldheti az Application Insightsba.

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
        // Create the DI container.
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

Ebben a példában a különálló csomag `Microsoft.Extensions.Logging.ApplicationInsights`. Alapértelmezés szerint ez a konfiguráció használja a "legalább" TelemetryConfiguration adatokat küld az Application Insights. Legalább jelenti, hogy a csatorna használt InMemoryChannel. Nincs mintavételi és a nem szabványos TelemetryInitializers van. Ez a viselkedés felülbírálható a egy konzolalkalmazást, az alábbi példában látható módon.

Ez a csomag telepítéséhez:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

A következő szakasz bemutatja, hogyan felülbírálhatja az alapértelmezett TelemetryConfiguration használatával a **szolgáltatások. Konfigurálása<TelemetryConfiguration>()** metódust. Ebben a példában állít be `ServerTelemetryChannel` és mintavétel. Egy egyéni ITelemetryInitializer hozzáadja a TelemetryConfiguration.

```csharp
    // Create the DI container.
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

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Naplózási szint vezérlési

Az ASP.NET Core *ILogger* infrastruktúra rendelkezik olyan beépített mechanizmus a alkalmazni [napló szűrése](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). Ez lehetővé teszi, hogy minden egyes regisztrált szolgáltatói, például az Application Insights-szolgáltató elküldött naplókban szabályozhatja. A szűrés történhet konfigurációban (általában használatával egy *appsettings.json* fájl) vagy a kódban. Ennek a lehetőségnek a keretrendszer maga biztosítja. Esetén nem vonatkozik az Application Insights-szolgáltató.

Az alábbi példák ApplicationInsightsLoggerProvider Állapotszűrő-szabályok érvényesek.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>A konfigurációban az appsettings.json Állapotszűrő-szabályok létrehozására

A szolgáltató alias a ApplicationInsightsLoggerProvider, `ApplicationInsights`. Következő szakasza *appsettings.json* konfigurálja a naplók *figyelmeztetés* és a fenti az összes kategóriából származó és *hiba* és a fenti kezdődő kategóriákból " A Microsoft"küldendő `ApplicationInsightsLoggerProvider`.

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

Az alábbi kódrészlet konfigurálja a naplók *figyelmeztetés* és a fenti az összes kategóriából származó és a *hiba* és a fenti kell küldeni a "Microsoft" kezdetű kategóriákból `ApplicationInsightsLoggerProvider`. Ez a konfiguráció megegyezik az előző szakaszban található *appsettings.json*.

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

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Mik azok a régi és új verziói ApplicationInsightsLoggerProvider?

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) tartalmaz egy beépített ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), amelyre engedélyezték a keresztül  **ILoggerFactory** bővítő metódusokat. Ez a szolgáltató elavult verziójú 2.7.0-beta2 származó van megjelölve. Ez lesz teljesen el kell távolítani a főverzió módosítása. A [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) csomagba nem elavult. Ez szükséges ahhoz, hogy kérelmeket, függőségeket, és így tovább figyelését.

A javasolt alternatív az új önálló csomag [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), amely tartalmaz egy továbbfejlesztett ApplicationInsightsLoggerProvider () Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) és bővítési metódusai a ILoggerBuilder engedélyezi azt.

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) verzió 2.7.0-beta3 függőséget vesz fel az új csomagot, és lehetővé teszi a ILogger rögzítési automatikusan.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Miért jelennek meg bizonyos ILogger naplók kétszer az Application insights szolgáltatásban?

Párhuzamos akkor fordulhat elő, ha a régebbi (most már elavult) verzióját ApplicationInsightsLoggerProvider meghívásával engedélyezve van `AddApplicationInsights` a `ILoggerFactory`. Ellenőrizze, hogy a **konfigurálása** módszert a következő, és távolítsa el:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Dupla naplózási hibakeresése a Visual Studióból tapasztal, állítsa be `EnableDebugLogger` való *hamis* a kódban, amely lehetővé teszi az Application Insights, a következőképpen. Ez kiszűrésével és a javítás, amelynek csak az alkalmazás hibakeresése.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Tudom a frissített [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) verzió 2.7.0-beta3 és ILogger naplóinak automatikusan rögzített. Hogyan lehet kikapcsolni a funkciót teljes mértékben?

Tekintse meg a [vezérelheti a naplózási szint](../../azure-monitor/app/ilogger.md#control-logging-level) általában naplók szűrése a szakaszt. Szülőpéldánya ApplicationInsightsLoggerProvider, használja a `LogLevel.None`:

**A kódban:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**A config:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Miért néhány ILogger naplók nem rendelkezik azonos tulajdonságokkal másokkal?

Az Application Insights rögzíti, és az azonos TelemetryConfiguration minden egyéb telemetriai használt használatával ILogger naplókat küld. Azonban egy kivételt. Alapértelmezés szerint TelemetryConfiguration teljes értéke nem való bejelentkezéskor **Program.cs** vagy **Startup.cs**. Ezek a helyek naplóinak sem kell az alapértelmezett konfiguráció, így minden TelemetryInitializers és TelemetryProcessors, nem fog futni.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>A különálló csomag Microsoft.Extensions.Logging.ApplicationInsights használok, és szeretném manuálisan bejelentkezni néhány további egyéni telemetriai adatokat. Hogyan kell megtenni?

A különálló csomag használatakor `TelemetryClient` nem szúrhatja be a DI-tárolóhoz, így hozzon létre egy új példányát kell `TelemetryClient` és használja ugyanazt a konfigurációt a naplózó szolgáltatót használja az alábbi kód látható módon. Ez biztosítja, hogy ugyanazt a konfigurációt az összes egyéni telemetriát, valamint ILogger származó telemetriai adatok szolgál.

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Ha a Microsoft.ApplicationInsights.AspNetCore csomag segítségével az Application Insights engedélyezése, módosítsa a kód segítségével kérheti le `TelemetryClient` közvetlenül a konstruktor. Egy vonatkozó példáért lásd: [Ez a GYIK](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Az Application Insights telemetria típusa előállított ILogger naplók? Vagy ahol látható ILogger naplók az Application Insights?

ApplicationInsightsLoggerProvider ILogger logs rögzíti, és TraceTelemetry számmá. Ha egy kivétel objektum átadott a **Log()** ILogger, metódust *ExceptionTelemetry* helyett TraceTelemetry jön létre. Ezek az elemek telemetria az Application Insights, beleértve a portal, az elemzésekhez, vagy a Visual Studio ladicího megtalálható TraceTelemetry vagy ExceptionTelemetry ugyanazon a helyen.

Ha TraceTelemetry elküldése mindig szeretne használni, használja az ebben a kódrészletben: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Nem rendelkezem az SDK-t, és az Azure Web Apps-bővítmény használatával saját ASP.NET Core-alkalmazásokhoz az Application Insights engedélyezése. Hogyan használhatom az új szolgáltató? 

Az Application Insights bővítményt, az Azure Web Apps a régi szolgáltatót használja. A szűrési szabályokat a módosíthatja a *appsettings.json* fájlt az alkalmazás. Az új szolgáltató kihasználásához használatához buildelés kialakítási NuGet függőség véve a SDK-val. Ez a cikk is frissülnek, amikor a bővítményt, az új szolgáltató használatára vált.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>A különálló csomag Microsoft.Extensions.Logging.ApplicationInsights használatával és az Application Insights-szolgáltató engedélyezése meghívásával **builder. AddApplicationInsights("ikey")** . Kialakítási kulcs beszerzése a konfigurációs lehetőség van?


Program.cs és appsettings.json módosítsa a következőképpen:

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

   A vonatkozó részében `appsettings.json`:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Ez a kód csak egy önálló naplózási szolgáltatót használ esetén szükséges. A normál Application insights általi figyelés, a kialakítási kulcsot automatikusan betöltődik a konfigurációs elérési *ApplicationInsights: Instrumentationkey*. AppSettings.JSON kell kinéznie:

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

* [Az ASP.NET Core-naplózás](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [.NET-nyomkövetési naplókat az Application insights szolgáltatásban](../../azure-monitor/app/asp-net-trace-logs.md)
