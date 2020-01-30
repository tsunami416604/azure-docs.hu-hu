---
title: Azure-Application Insights ASP.NET Core alkalmazásokhoz | Microsoft Docs
description: ASP.NET Core webalkalmazások figyelése a rendelkezésre állás, a teljesítmény és a használat érdekében.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/22/2019
ms.openlocfilehash: 5f54605dd5b43236a75fe73aa3b47a4e619530a1
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76765807"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights ASP.NET Core alkalmazásokhoz

Ez a cikk azt ismerteti, hogyan engedélyezhető a Application Insights [ASP.net Core](https://docs.microsoft.com/aspnet/core) alkalmazáshoz. A cikkben szereplő utasítások elvégzése után a Application Insights összegyűjti a kérelmeket, a függőségeket, a kivételeket, a teljesítményszámlálókat, a szívveréseket és a naplókat a ASP.NET Core alkalmazásból.

Az itt található példa egy [MVC-alkalmazás](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) , amely a `netcoreapp2.2`t célozza meg. Ezek az utasítások az összes ASP.NET Core alkalmazásra alkalmazhatók.

## <a name="supported-scenarios"></a>Támogatott esetek

A [ASP.NET Core Application INSIGHTS SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) -val a futtatásuk helyétől függetlenül nyomon követheti az alkalmazásait. Ha az alkalmazás fut, és hálózati kapcsolattal rendelkezik az Azure-hoz, a telemetria gyűjthet. A Application Insights figyelése mindenhol támogatott a .NET Core-ban. Támogatás kiterjed a következőkre:
* **Operációs rendszer**: Windows, Linux vagy Mac.
* **Üzemeltetési módszer**: folyamatban vagy folyamatban.
* **Üzembe helyezési módszer**: keretrendszer függő vagy önálló.
* **Webkiszolgáló**: IIS (Internet Information Server) vagy vércse.
* **Üzemeltetési platform**: az Azure app Service, az Azure VM, a Docker, az Azure Kubernetes Service (ak) Web Apps funkciója stb.
* **.Net Core futtatókörnyezet verziója**: 1. xx, 2. xx vagy 3. xx
* **Ide**: Visual Studio, vs Code vagy Command line.

> [!NOTE]
> Ha a ASP.NET Core 3,0-as Application Insightst használja, akkor használja a [2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) vagy újabb verziót. Ez az egyetlen olyan verzió, amely támogatja a ASP.NET Core 3,0-es verzióját.

## <a name="prerequisites"></a>Előfeltételek

- Működő ASP.NET Core alkalmazás. Ha ASP.NET Core alkalmazást kell létrehoznia, kövesse ezt az [ASP.net Core oktatóanyagot](https://docs.microsoft.com/aspnet/core/getting-started/).
- Érvényes Application Insights kialakítási kulcs. Ez a kulcs szükséges ahhoz, hogy bármilyen telemetria küldjön a Application Insightsnak. Ha létre kell hoznia egy új Application Insights-erőforrást a kialakítási kulcs beszerzéséhez, tekintse meg a [Application Insights erőforrás létrehozása](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)című témakört.

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Application Insights kiszolgálóoldali telemetria engedélyezése (Visual Studio)

1. Nyissa meg a projektet a Visual Studióban.

    > [!TIP]
    > Ha szeretné, beállíthatja a projekthez tartozó verziókövetés beállítását, hogy nyomon követhesse a Application Insights által végrehajtott összes módosítást. A verziókövetés engedélyezéséhez válassza a **fájl** > **Hozzáadás a forrás vezérlőelemhez**lehetőséget.

2. Válassza a **projekt** > **Application Insights telemetria hozzáadása**elemet.

3. Válassza az első **lépések**lehetőséget. A kiválasztott szöveg változhat a Visual Studio verziójától függően. Egyes korábbi verziók esetén az **ingyenes indítás** gomb használható.

4. Válassza ki előfizetését. Ezután válassza az **erőforrás** > **regisztráció**lehetőséget.

5. Miután hozzáadta Application Insights a projekthez, ellenőrizze, hogy az SDK legújabb stabil kiadását használja-e. Nyissa meg a **Project** > **NuGet-csomagok kezelése** > **Microsoft. ApplicationInsights. AspNetCore**. Ha szükséges, válassza a **frissítés**lehetőséget.

     ![Képernyőfelvétel: a Application Insights csomag kiválasztása a frissítéshez](./media/asp-net-core/update-nuget-package.png)

6. Ha követte a választható tippet, és felvette a projektet a forrás vezérlőelembe, lépjen a > **Team Explorer** > **módosításainak** **megtekintése** elemre. Ezután válassza ki az egyes fájlokat, és tekintse meg a Application Insights telemetria által végzett módosítások különbségeit.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Application Insights kiszolgálóoldali telemetria engedélyezése (nincs Visual Studio)

1. Telepítse a [ASP.NET Core Application INSIGHTS SDK NuGet-csomagot](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Javasoljuk, hogy mindig a legújabb stabil verziót használja. Az SDK teljes kibocsátási megjegyzéseit a [nyílt forráskódú GitHub-](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)tárházban találja.

    A következő mintakód a projekt `.csproj` fájljához hozzáadandó módosításokat mutatja be.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
        </ItemGroup>
    ```

2. Adja hozzá `services.AddApplicationInsightsTelemetry();` a `ConfigureServices()` metódushoz a `Startup` osztályban, az alábbi példában látható módon:

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. Állítsa be a kialakítási kulcsot.

    Bár a kialakítási kulcsot a `AddApplicationInsightsTelemetry`argumentumként is megadhatja, javasoljuk, hogy adja meg a kialakítási kulcsot a konfigurációban. A következő mintakód bemutatja, hogyan adható meg a rendszerállapot kulcsa `appsettings.json`ban. A közzététel során győződjön meg arról, hogy a `appsettings.json` a rendszer átmásolja az alkalmazás gyökérkönyvtár mappájába.

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    Azt is megteheti, hogy a rendszerállapot-kulcsot a következő környezeti változók egyikében is megadja:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Példa:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    A `APPINSIGHTS_INSTRUMENTATIONKEY` általában az Azure Web Apps üzembe helyezett alkalmazások kialakítási kulcsát adja meg.

    > [!NOTE]
    > A kód WINS-ben megadott rendszerállapot-kulcs a `APPINSIGHTS_INSTRUMENTATIONKEY`környezeti változón keresztül, amely más beállításokon keresztül nyerhető.

## <a name="run-your-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást, és tegye a kérelmeket. A telemetria-nek most Application Insightsre kell irányulnia. A Application Insights SDK automatikusan begyűjti a bejövő webes kérelmeket az alkalmazásba, valamint a következő telemetria is.

### <a name="live-metrics"></a>Élő metrikák

Az [élő metrikák](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) segítségével gyorsan ellenőrizheti, hogy a Application Insights figyelése megfelelően van-e konfigurálva. Habár néhány percet is igénybe vehet, amíg a telemetria elindul a Portálon és az elemzésekben, az élő metrikák közel valós időben mutatják be a futó folyamat CPU-használatát. Más telemetria is megjeleníthet, például a kérelmeket, a függőségeket, a Nyomkövetéseket stb.

### <a name="ilogger-logs"></a>ILogger-naplók

A súlyossági `Warning` vagy nagyobb `ILogger` keresztül kibocsátott naplók automatikusan rögzítésre kerülnek. A [ILogger-dokumentumok](ilogger.md#control-logging-level) követésével testreszabhatja, hogy a Application Insights mely naplózási szinteket rögzíti.

### <a name="dependencies"></a>Függőségek

A függőségi gyűjtemény alapértelmezés szerint engedélyezve van. [Ez](asp-net-dependencies.md#automatically-tracked-dependencies) a cikk az automatikusan összegyűjtött függőségeket ismerteti, valamint a manuális nyomon követésre szolgáló lépéseket is tartalmaz.

### <a name="performance-counters"></a>Teljesítményszámlálók

A [teljesítményszámlálók](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) támogatása ASP.net Core korlátozott:

* Az SDK 2.4.1-es és újabb verziói a teljesítményszámlálókat gyűjtik, ha az alkalmazás az Azure Web Apps (Windows) rendszeren fut.
* A 2.7.1-es és újabb verziójú SDK-verziók akkor gyűjtik a teljesítményszámlálókat, ha az alkalmazás Windows rendszeren fut, `NETSTANDARD2.0` vagy újabb rendszerű célokat.
* A .NET-keretrendszert célzó alkalmazások esetében az SDK összes verziója támogatja a teljesítményszámlálókat.
* A 2.8.0 és újabb verziójú SDK-verziók támogatják a CPU-/memória-számlálót a Linux rendszerben. A Linux nem támogatja a többi számlálót. A Linux-(és más nem Windows-környezetekben található) rendszerszámlálók használatának ajánlott módja a [EventCounters](#eventcounter) használata.

### <a name="eventcounter"></a>EventCounter

a `EventCounterCollectionModule` alapértelmezés szerint engedélyezve van, és a .NET Core 3,0-alkalmazásokból gyűjti össze a számlálók alapértelmezett készletét. A [EventCounter](eventcounters.md) -oktatóanyag a gyűjtött teljesítményszámlálók alapértelmezett készletét sorolja fel. Emellett a lista testreszabására vonatkozó utasításokat is tartalmaz.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Ügyféloldali telemetria engedélyezése webalkalmazásokhoz

Az előző lépések elegendő segítséget nyújtanak a kiszolgálóoldali telemetria gyűjtésének megkezdéséhez. Ha az alkalmazás ügyféloldali összetevőket tartalmaz, kövesse a következő lépéseket a [használati telemetria](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)gyűjtésének megkezdéséhez.

1. `_ViewImports.cshtml`a befecskendezés hozzáadása:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. `_Layout.cshtml`a `<head>` szakasz végén, de bármely más parancsfájl előtt szúrjon be `HtmlHelper`. Ha az oldalról bármilyen egyéni JavaScript-telemetria szeretne jelenteni, szúrja be azt a kódrészlet után:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

A korábban hivatkozott `.cshtml` fájlnevek egy alapértelmezett MVC-alkalmazás sablonból származnak. Végül, ha megfelelően szeretné engedélyezni az ügyféloldali figyelést az alkalmazáshoz, a JavaScript-kódrészletnek szerepelnie kell a figyelni kívánt alkalmazás minden oldalának `<head>` szakaszában. Ezt a célt az alkalmazás-sablonhoz a JavaScript-kódrészlet `_Layout.cshtml`való hozzáadásával hajthatja végre. 

Ha a projekt nem tartalmaz `_Layout.cshtml`, akkor továbbra is hozzáadhat [ügyféloldali figyelést](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Ezt úgy teheti meg, hogy hozzáadja a JavaScript-kódrészletet egy egyenértékű fájlhoz, amely az alkalmazásban lévő összes oldal `<head>` vezérli. Emellett a kódrészletet több oldalra is hozzáadhatja, de a megoldás nehezen karbantartható, és általában nem ajánlott.

## <a name="configure-the-application-insights-sdk"></a>A Application Insights SDK konfigurálása

Az alapértelmezett konfiguráció módosításához testreszabhatja a ASP.NET Core Application Insights SDK-t. Előfordulhat, hogy a Application Insights ASP.NET SDK felhasználói megismerik a konfiguráció módosítását `ApplicationInsights.config` vagy a `TelemetryConfiguration.Active`módosításával. A konfigurációt a ASP.NET Core eltérő módon változtathatja meg. Adja hozzá a ASP.NET Core SDK-t az alkalmazáshoz, és konfigurálja ASP.NET Core beépített [függőségi befecskendezés](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)használatával. A `Startup.cs` osztály `ConfigureServices()` metódusában szinte minden konfigurációs módosítást hajthat végre, kivéve, ha másként van átirányítva. A következő fejezetei további információkat nyújtanak.

> [!NOTE]
> ASP.NET Core alkalmazásokban a konfiguráció módosítása `TelemetryConfiguration.Active` módosításával nem támogatott.

### <a name="using-applicationinsightsserviceoptions"></a>A ApplicationInsightsServiceOptions használata

Néhány gyakori beállítást úgy módosíthat, hogy `ApplicationInsightsServiceOptions` átadásával `AddApplicationInsightsTelemetry`, ahogy az alábbi példában látható:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    // Disables adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;

    // Disables QuickPulse (Live Metrics stream).
    aiOptions.EnableQuickPulseMetricStream = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
}
```

A `ApplicationInsightsServiceOptions` beállításainak teljes listája

|Beállítás | Leírás | Alapértelmezett
|---------------|-------|-------
|EnableQuickPulseMetricStream | LiveMetrics funkció engedélyezése/letiltása | igaz
|EnableAdaptiveSampling | Adaptív mintavételezés engedélyezése/letiltása | igaz
|EnableHeartbeat | A szívverések funkció engedélyezése/letiltása, amely rendszeres időközönként (15 perces alapértelmezett) a "HeartBeatState" nevű egyéni metrikát küldi el a (z), például a .NET-es verzióval, az Azure-környezettel kapcsolatos információkkal, ha vannak ilyenek, stb. | igaz
|AddAutoCollectedMetricExtractor | Az AutoCollectedMetrics Extractor engedélyezése/letiltása, amely egy olyan TelemetryProcessor, amely előre összevont metrikákat küld a kérelmek/függőségek számára a mintavétel megkezdése előtt. | igaz
|RequestCollectionOptions.TrackExceptions | Engedélyezheti vagy letilthatja a nem kezelt kivételek nyomon követését a kérelmek gyűjtési modulja által. | hamis a NETSTANDARD 2.0-ban (mivel a kivételeket a ApplicationInsightsLoggerProvider követte nyomon), ellenkező esetben igaz.

A legnaprakészebb listához tekintse [meg a `ApplicationInsightsServiceOptions`konfigurálható beállításait](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) .

### <a name="sampling"></a>Mintavétel

A ASP.NET Core Application Insights SDK a rögzített sebességű és az adaptív mintavételezést is támogatja. Az adaptív mintavételezés alapértelmezés szerint engedélyezve van. 

További információ: [adaptív mintavételezés konfigurálása ASP.net Core alkalmazásokhoz](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers hozzáadása

Ha az összes telemetria ellátott globális tulajdonságokat szeretné definiálni, használja a [telemetria-inicializálást](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) .

A következő kódban látható módon vegyen fel minden új `TelemetryInitializer` a `DependencyInjection` tárolóba. Az SDK automatikusan felveszi a `DependencyInjection` tárolóba felvenni kívánt `TelemetryInitializer`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers eltávolítása

A telemetria inicializálók alapértelmezés szerint jelennek meg. Az összes vagy adott telemetria-inicializáló eltávolításához használja az alábbi mintát a `AddApplicationInsightsTelemetry()`meghívása *után* .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

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

A `IServiceCollection``AddApplicationInsightsTelemetryProcessor` bővítmény módszerével egyéni telemetria-processzorokat adhat hozzá `TelemetryConfiguration`okhoz. A [speciális szűrési helyzetekben](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer)telemetria processzorokat használ. Használja az alábbi példát.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddApplicationInsightsTelemetry();
    services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

    // If you have more processors:
    services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
}
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Alapértelmezett TelemetryModules konfigurálása vagy eltávolítása

A Application Insights telemetria-modulok használatával automatikusan gyűjti az adott számítási feladatokhoz tartozó hasznos telemetria, anélkül, hogy a felhasználó manuális követését kellene használnia.

A következő automatikus gyűjtési modulok alapértelmezés szerint engedélyezve vannak. Ezeknek a moduloknak a feladata a telemetria automatikus gyűjtése. Az alapértelmezett viselkedés megváltoztatásához letilthatja vagy konfigurálhatja őket.

* `RequestTrackingTelemetryModule` – RequestTelemetry gyűjt a bejövő webes kérelmekből.
* `DependencyTrackingTelemetryModule` – a kimenő HTTP-hívások és az SQL-hívások DependencyTelemetry gyűjti.
* `PerformanceCollectorModule` – a Windows PerformanceCounters gyűjti.
* `QuickPulseTelemetryModule` – az élő metrika portálon megjelenített telemetria gyűjti.
* `AppServicesHeartbeatTelemetryModule` – gyűjti a szívveréseket (amelyek egyéni mérőszámként lesznek elküldve), Azure App Service-környezetről, ahol az alkalmazás fut.
* `AzureInstanceMetadataTelemetryModule` – gyűjti a szívveréseket (amelyek egyéni mérőszámként lesznek elküldve), az Azure-beli virtuálisgép-környezetről, ahol az alkalmazás üzemeltetve van.
* `EventCounterCollectionModule` – gyűjti a [EventCounters.](eventcounters.md) Ez a modul egy új szolgáltatás, amely az SDK 2.8.0-es és újabb verzióiban érhető el.

Az alapértelmezett `TelemetryModule`konfigurálásához használja a `IServiceCollection``ConfigureTelemetryModule<T>` bővítmény módszerét az alábbi példában látható módon.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // The following configures DependencyTrackingTelemetryModule.
    // Similarly, any other default modules can be configured.
    services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
            {
                module.EnableW3CHeadersInjection = true;
            });

    // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
    services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                module.Counters.Clear();
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

    // The following removes PerformanceCollectorModule to disable perf-counter collection.
    // Similarly, any other default modules can be removed.
    var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
    if (performanceCounterService != null)
    {
        services.Remove(performanceCounterService);
    }
}
```

### <a name="configuring-a-telemetry-channel"></a>Telemetria-csatorna konfigurálása

Az alapértelmezett csatorna `ServerTelemetryChannel`. A következő példában látható módon felülbírálhatja azt.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>Telemetria dinamikus letiltása

Ha a telemetria feltételesen és dinamikusan szeretné letiltani, akkor feloldható `TelemetryConfiguration` példány ASP.NET Core függőségi injektálási tárolóval bárhol a kódban, és beállíthatja `DisableTelemetry` jelzőt.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

A fenti nem akadályozza meg, hogy az automatikus gyűjtési modulok telemetria gyűjtsenek. A fenti megközelítéssel csak a telemetria küldése Application Insights lesz letiltva. Ha egy adott automatikus gyűjtési modul nem szükséges, érdemes [eltávolítani a telemetria modult](#configuring-or-removing-default-telemetrymodules) .

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="does-application-insights-support-aspnet-core-30"></a>Application Insights támogatást ASP.NET Core 3,0?

Igen. Frissítsen [Application INSIGHTS SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) -ra ASP.net Core 2.8.0 vagy újabb verzióra. Az SDK régebbi verziói nem támogatják a 3,0-es ASP.NET Core.

Emellett, ha [itt](#enable-application-insights-server-side-telemetry-visual-studio)Visual Studio-alapú utasításokat használ, frissítsen a visual Studio 2019 (16.3.0) legújabb verziójára a bevezetéshez. A Visual Studio korábbi verziói nem támogatják a ASP.NET Core 3,0 alkalmazások automatikus előkészítését.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Hogyan követhetem nyomon az automatikusan összegyűjtött telemetria?

`TelemetryClient` példányának beszerzése a konstruktor injekciójának használatával, és a szükséges `TrackXXX()` metódus meghívása. Nem javasoljuk, hogy új `TelemetryClient` példányokat hozzon létre egy ASP.NET Core alkalmazásban. A `TelemetryClient` egy egyedi példánya már regisztrálva van a `DependencyInjection` tárolóban, amely a telemetria többi részével megosztja a `TelemetryConfiguration`. Új `TelemetryClient`-példány létrehozása csak akkor javasolt, ha a többi telemetria eltérő konfigurációra van szükség.

Az alábbi példa azt szemlélteti, hogyan követhet nyomon a vezérlő további telemetria.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

További információ a Application Insightsban található egyéni adatjelentésekről: [Application Insights egyéni metrikák API-referencia](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Egyes Visual Studio-sablonok a IWebHostBuilder UseApplicationInsights () bővítményi metódusát használták a Application Insights engedélyezéséhez. Még érvényes ez a használat?

A bővítményi módszer `UseApplicationInsights()` továbbra is támogatott, Application Insights SDK-beli 2.8.0-verzióban elavultként van megjelölve. Az SDK a következő főverziójában el lesz távolítva. A Application Insights-telemetria engedélyezésének ajánlott módja a `AddApplicationInsightsTelemetry()` használata, mert túlterheléseket biztosít a konfiguráció szabályozásához. Emellett ASP.NET Core 3,0-es alkalmazásokban `services.AddApplicationInsightsTelemetry()` az Application bepillantást az egyetlen módszer.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>ASP.NET Core alkalmazást telepítem Web Apps. Továbbra is engedélyezni kell a Application Insights bővítményt a Web Appsból?

Ha az SDK-t a jelen cikkben látható módon telepíti a Build időpontban, nem kell engedélyeznie a [Application Insights bővítményt](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) a app Service portálról. Ha a bővítmény telepítve van, akkor a rendszer akkor is vissza fog térni, ha észleli, hogy az SDK már hozzá van adva az alkalmazáshoz. Ha engedélyezi a Application Insights a bővítményből, nem kell telepítenie és frissítenie az SDK-t. Ha azonban a jelen cikkben szereplő utasítások alapján engedélyez Application Insights, nagyobb rugalmasságot biztosít, mivel:

   * Application Insights telemetria továbbra is a következővel fog működni:
       * Minden operációs rendszer, beleértve a Windows, a Linux és a Mac rendszereket is.
       * Az összes közzétételi mód, beleértve az önálló vagy a keretrendszertől függőket is.
       * Minden cél keretrendszer, beleértve a teljes .NET-keretrendszert is.
       * Minden üzemeltetési lehetőség, beleértve a Web Apps, a virtuális gépeket, a Linuxot, a tárolókat, az Azure Kubernetes szolgáltatást és a nem Azure-beli üzemeltetést.
       * Az összes .NET Core verzió, beleértve az előzetes verziókat is.
   * A Visual studióból történő hibakeresés során a telemetria helyileg is megtekintheti.
   * A `TrackXXX()` API használatával nyomon követheti a további egyéni telemetria.
   * Teljes hozzáférése van a konfigurációhoz.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Engedélyezhető Application Insights-figyelés az olyan eszközökkel, mint a Állapotmonitor?

Nem. A [Állapotmonitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) és a [Állapotmonitor v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) jelenleg csak a 4. x ASP.net támogatja.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Application Insights automatikusan engedélyezve van a saját ASP.NET Core 2,0 alkalmazáshoz?

A `Microsoft.AspNetCore.All` 2,0 metacsomag tartalmazza a Application Insights SDK-t (2.1.0-verzió). Ha az alkalmazást a Visual Studio Debugger alatt futtatja, a Visual Studio lehetővé teszi, hogy a Application Insights és a telemetria helyileg jelenítse meg az IDE-ben. A telemetria nem lett elküldve a Application Insights szolgáltatásnak, kivéve, ha meg van adva a kialakítási kulcs. Javasoljuk, hogy a cikk utasításait követve engedélyezze a Application Insights, még az 2,0-alkalmazások esetében is.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Ha Linuxon futtatom az alkalmazást, az összes funkció támogatott?

Igen. Az SDK funkcióinak támogatása minden platformon azonos, a következő kivételekkel:

* Az SDK az [eseménynaplókat](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters) a Linux rendszeren gyűjti [, mivel a teljesítményszámlálók csak](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters) a Windows rendszerben támogatottak. A legtöbb metrika ugyanaz.
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
        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="is-this-sdk-supported-for-the-new-net-core-30-worker-service-template-applications"></a>Támogatott ez az SDK az új .NET Core 3,0 Worker Service template-alkalmazásokhoz?

Ez az SDK `HttpContext`igényel, ezért nem működik semmilyen nem HTTP-alkalmazásban, beleértve a .NET Core 3,0 Worker Service-alkalmazásokat. Tekintse meg [ezt](worker-service.md) a dokumentumot, amely lehetővé teszi az Application bepillantást az alkalmazásokban az újonnan kiadott Microsoft. ApplicationInsights. WorkerService SDK használatával.

## <a name="open-source-sdk"></a>Nyílt forráskódú SDK

[Olvassa el és járuljon hozzá a kódhoz](https://github.com/microsoft/ApplicationInsights-dotnet#recent-updates).

## <a name="video"></a>Videó

- Tekintse meg ezt a külső lépésenkénti videót, amellyel a [Application Insights a .net Core és a Visual Studio használatával konfigurálhatja](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) a semmiből.
- Tekintse meg ezt a külső lépésenkénti videót a [.net Core és a Visual Studio Code nélküli Application Insights konfigurálásához](https://youtu.be/ygGt84GDync) .

## <a name="next-steps"></a>Következő lépések

* [Fedezze fel a felhasználói folyamatokat](../../azure-monitor/app/usage-flows.md) , hogy megtudja, hogyan navigálnak a felhasználók az alkalmazáson keresztül.
* [Egy pillanatkép-gyűjtemény konfigurálásával](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) megtekintheti a forráskód és a változók állapotát a kivétel pillanatában.
* [Az API használatával](../../azure-monitor/app/api-custom-events-metrics.md) saját eseményeket és mérőszámokat küldhet az alkalmazás teljesítményének és használatának részletes áttekintéséhez.
* A [rendelkezésre állási tesztek](../../azure-monitor/app/monitor-web-app-availability.md) segítségével folyamatosan, a világ minden pontján ellenőrizhető az alkalmazás.
* [Függőség injekció ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
