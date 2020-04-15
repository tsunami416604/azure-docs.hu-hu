---
title: Fedezze fel a .NET nyomkövetési naplókat az ILogger segítségével - Azure Application Insights
description: Az Azure Application Insights ILogger-szolgáltató ASP.NET Core és Console alkalmazásokkal való használatának mintái.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 33effe9cfec6d766d573617ff03b58564e5b34d1
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313660"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider a .NET Core ILogger naplóihoz

ASP.NET Core támogatja a naplózási API-t, amely együttműködik a különböző beépített és külső naplózási szolgáltatók. A naplózás a **Log()** vagy annak egy változata meghívásával történik az *ILogger* példányokon. Ez a cikk bemutatja, hogyan használható *ApplicationInsightsLoggerProvider* rögzítésére ILogger naplók konzol és ASP.NET Core alkalmazások. Ez a cikk azt is ismerteti, hogyan ApplicationInsightsLoggerProvider integrálható más Application Insights telemetriai adatokkal.
További információ: [Bejelentkezés ASP.NET Core nyelven.](https://docs.microsoft.com/aspnet/core/fundamentals/logging)

## <a name="aspnet-core-applications"></a>ASP.NET alapalkalmazások

Az ApplicationInsightsLoggerProvider alapértelmezés szerint engedélyezve van a [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.1-es (és újabb) verziójában, ha bekapcsolja a rendszeres Application Insights-figyelést a következő módszerek valamelyikén keresztül:

- Az IWebHostBuilder **UseApplicationInsights** bővítménymetódusának hívásával (most már elavult)
- Az **AddApplicationInsightsTelemetria** bővítmény metódus ának hívásával az IServiceCollection

ILogger naplók, hogy ApplicationInsightsLoggerProvider rögzíti vonatkozik az azonos konfiguráció, mint bármely más telemetriai, hogy az összegyűjtött. Ugyanazt a Telemetriai initializereket és telemetriai processzorokat, ugyanazt a TelemetryChannel-t használják, és korrelálnak és mintavételeznek ugyanúgy, mint a többi telemetriai adatok. Ha a 2.7.1-es vagy újabb verziót használja, nincs szükség műveletre az ILogger-naplók rögzítéséhez.

Alapértelmezés szerint csak *a figyelmeztetési* vagy magasabb ILogger-naplók (az összes [kategóriából)](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-3.1#log-category)kerülnek elküldésre az Application Insights számára. A viselkedés módosításához azonban [szűrőket is alkalmazhat.](#control-logging-level) További lépések szükségesek az ILogger-naplók rögzítéséhez **Program.cs** vagy **Startup.cs.** [(Lásd: ILogger-naplók rögzítése Startup.cs és Program.cs ASP.NET Core alkalmazásokban.)](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps)

Ha a Microsoft.ApplicationInsights.AspNet SDK egy korábbi verzióját használja, vagy csak az ApplicationInsightsLoggerProvider-t szeretné használni az Application Insights figyelése nélkül, kövesse az alábbi eljárást:

1. Telepítse a NuGet csomagot:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Módosítsa **Program.cs** az itt látható módon:

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

A `ApplicationInsightsLoggerProvider`2. A következő kód egy példa vezérlőosztályt mutat be, amely naplók küldésére használja. `ILogger` A naplók at az Application Insights rögzíti.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Capture ILogger naplók Startup.cs és Program.cs ASP.NET Core alkalmazások

> [!NOTE]
> A Core 3.0-s és újabb ASP.NET `ILogger` már nem lehet beadni Startup.cs és Program.cs. További https://github.com/aspnet/Announcements/issues/353 részletek.

Az új ApplicationInsightsLoggerProvider képes rögzíteni a naplókat az alkalmazás-indítási folyamat korai szakaszában. Bár az ApplicationInsightsLoggerProvider automatikusan engedélyezve van az Application Insightsban (a 2.7.1-es verziótól kezdve), a folyamat későbbi részében nincs beállítva instrumentation kulcs. Így csak a **Controller**/other osztályok naplói lesznek rögzítve. Az **Program.cs** és **magával Startup.cs** kezdve minden napló rögzítéséhez explicit módon engedélyeznie kell egy instrumentation kulcsot az ApplicationInsightsLoggerProvider számára. Emellett *a TelemettryConfiguration* nincs teljesen beállítva, amikor **Program.cs** vagy **magától Startup.cs** naplóz. Így ezek a naplók lesz egy minimális konfiguráció, amely inMemoryChannel, nincs mintavétel, és nem szabványos telemetriai inicializálók vagy processzorok.

Az alábbi példák bemutatják ezt a képességet **a Program.cs** és **Startup.cs.**

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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Áttelepítés a régi ApplicationInsightsLoggerProvider-ről

A Microsoft.ApplicationInsights.AspNet SDK 2.7.1 előtti verziói támogatták a már elavult naplózási szolgáltatót. Ez a szolgáltató engedélyezve volt az ILoggerFactory **AddApplicationInsights()** bővítménymetódusán keresztül. Javasoljuk, hogy térjen át az új szolgáltatóra, amely két lépésből áll:

1. Távolítsa el az *ILoggerFactory.AddApplicationInsights()* hívást az **Startup.Configure()** metódusból a kettős naplózás elkerülése érdekében.
2. Alkalmazza újra a szűrési szabályokat a kódban, mert az új szolgáltató nem fogja betartani őket. Az *ILoggerFactory.AddApplicationInsights()* túlterhelése minimális LogLevel vagy szűrőfüggvényeket vett igénybe. Az új szolgáltatónál a szűrés maga a naplózási keretrendszer része. Ez nem az Application Insights-szolgáltató által történik. Tehát minden szűrők, amelyek keresztül *iLoggerFactory.AddApplicationInsights()* túlterhelések el kell távolítani. A szűrési szabályokat pedig a [Vezérlés naplózási szintre](#control-logging-level) vonatkozó utasítások szerint kell megadni. Ha *az appsettings.json* használatával szűri a naplózást, az továbbra is működni fog az új szolgáltatóval, mivel mindkettő ugyanazt a szolgáltatóaliast, az *ApplicationInsights-ot*használja.

Továbbra is használhatja a régi szolgáltatót. (Ez lesz távolítva csak egy nagy verzió változás 3. *xx*.) De azt javasoljuk, hogy térjen át az új szolgáltató a következő okok miatt:

- Az előző szolgáltató nem támogatja a [naplóhatóköröket.](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes) Az új szolgáltatóban a hatókörből származó tulajdonságok automatikusan egyéni tulajdonságokként kerülnek hozzáadásra az összegyűjtött telemetriai adatokhoz.
- A naplók most már sokkal korábban rögzíthetők az alkalmazás indítási folyamatában. A **Program** és **az Indítás** idiktacaosztályok naplói most már rögzíthetők.
- Az új szolgáltatónál a szűrés a keretszinten történik. Az Application Insights-szolgáltatónaplói ugyanúgy szűrhetők, mint más szolgáltatókesetében, például a beépített szolgáltatók, például a konzol, a debug és így tovább. Ugyanazokat a szűrőket több szolgáltatóra is alkalmazhatja.
- A ASP.NET Core (2.0 és újabb), az ajánlott módja annak, hogy [a naplózásszolgáltatók](https://github.com/aspnet/Announcements/issues/255) segítségével kiterjesztése módszerek ILoggingBuilder **Program.cs** is.

> [!Note]
> Az új szolgáltató a NETSTANDARD2.0-s vagy újabb verziót célzó alkalmazásokhoz érhető el. A [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.14.0-s verziójától kezdve új szolgáltató is elérhető a .NET Framework NET461 vagy újabb célalkalmazások hoz. Ha az alkalmazás a régebbi .NET Core verziókat ( például .NET Core 1.1) célozza meg, vagy ha a .NET Framework kisebb, mint A NET46, továbbra is használja a régi szolgáltatót.

## <a name="console-application"></a>Konzolalkalmazás

> [!NOTE]
> Van egy új Application Insights SDK nevű [Microsoft.ApplicationInsights.WorkerService,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) amely lehetővé teszi az Application Insights (ILogger és más Application Insights telemetriai) bármely konzolalkalmazások. Javasoljuk, hogy használja ezt a csomagot, és a kapcsolódó utasításokat [innen](../../azure-monitor/app/worker-service.md).

A következő kód egy mintakonzol-alkalmazást mutat be, amely iLogger-nyomkövetések küldésére van konfigurálva az Application Insights számára.

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

Ez a példa az `Microsoft.Extensions.Logging.ApplicationInsights`önálló csomagot használja. Alapértelmezés szerint ez a konfiguráció a "csupasz minimális" TelemettryConfiguration az Application Insights adatok küldéséhez. A minimális minimum azt jelenti, hogy az InMemoryChannel a használt csatorna. Nincs mintavétel, és nincs szabványos Telemetriai Initializers. Ez a viselkedés felülbírálható egy konzolalkalmazás, ahogy a következő példa mutatja.

Telepítse ezt a kiegészítő csomagot:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

A következő szakasz bemutatja, hogyan felülbírálhatja az alapértelmezett TelemettryConfiguration a **szolgáltatások használatával. Konfigurálja\<a TelemettryConfiguration>()** metódust. Ez a `ServerTelemetryChannel` példa beállítja és mintavételezi. Hozzáadja az egyéni ITelemetryInitializer a TelemettryConfiguration.

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

## <a name="control-logging-level"></a>Naplózási szint szabályozása

A ASP.NET Core *ILogger* infra beépített mechanizmussal rendelkezik a [naplószűrés](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering)alkalmazásához. Ez lehetővé teszi, hogy az egyes regisztrált szolgáltatóknak küldött naplókat, beleértve az Application Insights-szolgáltatót is, szabályozhatja. A szűrés történhet konfigurációban (általában *egy appsettings.json* fájl használatával) vagy kódban. Ezt a lehetőséget maga a keret biztosítja. Nem az Application Insights-szolgáltatóra jellemző.

A következő példák szűrőszabályokat alkalmaznak az ApplicationInsightsLoggerProvider alkalmazásra.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Szűrőszabályok létrehozása konfigurációban az appsettings.json alkalmazással

Az ApplicationInsightsLoggerProvider esetén a `ApplicationInsights`szolgáltató aliasa . Az *appsettings.json* következő szakasza általában arra utasítja a naplózási szolgáltatókat, hogy *a Figyelmeztetés* és a magasabb szintű naplózásszintjén jelentkezzenek be. Ezután felülbírálja `ApplicationInsightsLoggerProvider` a napló kategóriák, hogy kezdődik a "Microsoft" szinten *Hiba* és a fenti.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>Szűrőszabályok létrehozása a kódban

A következő kódrészlet konfigurálja a *naplót a Figyelmeztetés* és a felett minden kategóriából, valamint a *Hiba* és a fenti kategóriákból, amelyek a "Microsoft" kezdetű kategóriákból `ApplicationInsightsLoggerProvider`indulnak. Ez a konfiguráció megegyezik az *appsettings.json*korábbi részében szereplő konfigurációval.

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

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Melyek az ApplicationInsightsLoggerProvider régi és új verziói?

[A Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) beépített ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider) szolgáltatót tartalmazott, amely az **ILoggerFactory** bővítménymetódusokon keresztül lett engedélyezve. Ez a szolgáltató elavultnak van jelölve a 2.7.1-es verziótól. Ez lesz távoli teljesen -ban következő őrnagy változat cserél. A [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) csomag maga nem elavult. A kérelmek, függőségek és így tovább figyelésének engedélyezéséhez szükséges.

A javasolt alternatíva az új önálló csomag [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), amely tartalmaz egy továbbfejlesztett ApplicationInsightsLoggerProvider (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) és a kiterjesztés módszerek ILoggerBuilder engedélyezéséhez.

[A Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.1-es verziója függőséget igényel az új csomagtól, és lehetővé teszi az ILogger automatikus rögzítését.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Miért jelennek meg néhány ILogger-napló kétszer az Application Insightsban?

Az alkalmazási naplóból való hívással `AddApplicationInsights` az ApplicationInsightsLoggerProvider régebbi (már `ILoggerFactory`elavult) verziója engedélyezve van. Ellenőrizze, hogy a **Konfigurálás** módszer rendelkezik-e a következőkkel, és távolítsa el:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Ha a Visual Studio-ból történő hibakeresés során kettős naplózást tapasztal, állítsa `EnableDebugLogger` *hamisra* az Application Insightsot lehetővé tévő kódban, az alábbiak szerint. Ez a másolás és javítás csak akkor releváns, ha az alkalmazást hibakeresés közben javítja.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Frissítettem a [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.1-es verziójára, és az ILogger naplói automatikusan rögzítésre kerülnek. Hogyan kapcsolhatom ki teljesen ezt a funkciót?

A naplók szűrését általában a [Naplózási szint szabályozása](../../azure-monitor/app/ilogger.md#control-logging-level) című szakaszban láthatja. Az ApplicationInsightsLoggerProvider kikapcsolására `LogLevel.None`használja a következőt:

**Kódban:**

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

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Miért néhány ILogger naplók nem ugyanazokkal a tulajdonságokkal, mint mások?

Az Application Insights rögzíti és elküldi az ILogger-naplókat az összes többi telemetriai adatokhoz használt telemetriai konfiguráció használatával. De van egy kivétel. Alapértelmezés szerint a TelemettryConfiguration nincs teljesen beállítva, amikor **Program.cs** vagy **Startup.cs**naplóz. Az ezekből a helyekről származó naplók nem rendelkeznek az alapértelmezett konfigurációval, így nem fognak futtatni az összes Telemetriai Initializers és TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>A Microsoft.Extensions.Logging.ApplicationInsights önálló csomagot használom, és manuálisan szeretnék naplózni néhány további egyéni telemetriai szempontot. Hogy csináljam?

Ha az önálló csomagot `TelemetryClient` használja, nem adja be a DI-tárolóba, ezért `TelemetryClient` létre kell hoznia egy új példányt, és ugyanazt a konfigurációt kell használnia, mint a naplózó szolgáltató, ahogy azt a következő kód mutatja. Ez biztosítja, hogy ugyanazt a konfigurációt használja az összes egyéni telemetriai adatok, valamint a telemetriai adatok iLogger.

```csharp
public class MyController : ApiController
{
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
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
> Ha a Microsoft.ApplicationInsights.AspNetCore csomagot használja az Application Insights `TelemetryClient` engedélyezéséhez, módosítsa ezt a kódot, hogy közvetlenül a konstruktorban kerüljön be. Például lásd [ezt a GYIK](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Milyen Application Insights telemetriai típus jön létre iLogger naplók? Vagy hol láthatom iLogger naplók Application Insights?

Az ApplicationInsightsLoggerProvider rögzíti az ILogger-naplókat, és tracetelemetriát hoz létre belőlük. Ha egy kivétel objektum ot ad át a **Log()** metódus iLogger, *ExceptionTelemetry* jön létre a TraceTelemetry helyett. Ezek a telemetriai elemek találhatók ugyanazon a helyen, mint bármely más TraceTelemetry vagy ExceptionTelemetry az Application Insights, beleértve a portál, elemzés i vagy Visual Studio helyi hibakereső.

Ha mindig tracetelemetriai küldést szeretne küldeni, használja ezt a kódrészletet:```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Nincs telepítve az SDK, és az Azure Web Apps bővítményt használom az Application Insights engedélyezéséhez a ASP.NET Core alkalmazásaimhoz. Hogyan használhatom az új szolgáltatót? 

Az Application Insights bővítmény az Azure Web Apps az új szolgáltatót használja. A szűrési szabályokat az *alkalmazás appsettings.json* fájljában módosíthatja.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>A Microsoft.Extensions.Logging.ApplicationInsights önálló csomagot használom, és engedélyezem az Application Insights-szolgáltatót az **építő hívásával. AddApplicationInsights("ikey")**. Van-e lehetőség, hogy egy műszerezési kulcsot a konfiguráció?


Módosítsa Program.cs és az appsettings.json t az alábbiak szerint:

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

   A vonatkozó `appsettings.json`szakasz:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Ez a kód csak akkor szükséges, ha önálló naplózási szolgáltatót használ. A rendszeres Application Insights figyelés, a műszerezési kulcs automatikusan betöltődik a konfigurációs útvonal *ApplicationInsights: Instrumentationkey.* Appsettings.json kell kinéznie így:

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

* [Bejelentkezés ASP.NET Core-ba](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [.NET nyomkövetési naplók az Application Insightsban](../../azure-monitor/app/asp-net-trace-logs.md)
