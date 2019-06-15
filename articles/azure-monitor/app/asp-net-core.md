---
title: Az Azure Application Insights az ASP.NET Core |} A Microsoft Docs
description: Figyelje az ASP.NET Core webes alkalmazások elérhetőségéről, teljesítményéről és kihasználtságáról.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mbullwin
ms.openlocfilehash: cb7ace20fd0a59dafff3d7f8240f54c3c8e12492
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66226400"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights az ASP.NET Core-alkalmazások

Ez a cikk bemutatja, hogyan készült Application Insights engedélyezése egy [ASP.NET Core](https://docs.microsoft.com/aspnet/core) alkalmazás. Amikor befejezte a jelen cikkben lévő utasítások, az Application Insights indul el kéréseket, függőségek, kivételek, teljesítményszámlálók, szívverések és naplók összegyűjtése az ASP.NET Core-alkalmazást. Mintaalkalmazás van egy [MVC alkalmazás](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) célzó `netcoreapp2.2`, de ezek az utasítások érvényesek minden ASP.NET Core-alkalmazásra.

## <a name="supported-scenarios"></a>Támogatott esetek

A [Application Insights SDK (Software Development Kitet) az ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) figyelheti az alkalmazások, függetlenül attól, hol és hogyan az alkalmazás futtatásakor. Ha az alkalmazás fut, és rendelkezik hálózati kapcsolattal az Azure-ba, telemetriai adatokat lehessen gyűjteni. Ez azt jelenti, hogy az Application Insights figyelési támogatott mindenhol .NET Core használata támogatott. Ez a támogatás magában foglalja, bármely operációs rendszert (Windows, Linux, Mac), a üzemeltetési módot (a folyamat a vs-folyamaton), az üzembe helyezési módszer (keretrendszer-függő vagy önálló), webkiszolgáló (IIS, a Kestrel), (az Azure Web Apps, Azure virtuális Gépen, Docker, a üzemeltetési platform Az Azure Kubernetes Service (AKS), és így tovább.) vagy az IDE (Visual Studio, a VS Code-parancssor.)

## <a name="prerequisites"></a>Előfeltételek

- Egy működő ASP.NET Core-alkalmazást. Kövesse a [ASP.NET Core első lépések útmutató](https://docs.microsoft.com/aspnet/core/getting-started/) hozhat létre egy ASP.NET Core-alkalmazást, ha szükséges.
- Egy érvényes az Application Insights kialakítási kulcsot, amely azonban szükséges a telemetriát az Application Insights szolgáltatás. Kövesse a [erőforrás létrehozása utasításokat](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) hozzon létre egy új Application Insights-erőforrást, ha szükséges, és a egy kialakítási kulcs beszerzése.

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Az Application Insights kiszolgálóoldali telemetria (Visual Studio) engedélyezése

1. Nyissa meg a projektjét a Visual Studióban.

    > [!TIP]
    > Nem kötelező lépés, miközben hasznos lehet a állítható be verziókövetés a projekthez, így nyomon követheti az Application Insights által végzett összes módosítást. Forrás vezérlő select engedélyezéséhez **fájl** > **Hozzáadás a Forráskezelőhöz**.

2. Válassza ki **projekt** > **Application Insights Telemetria hozzáadása**.

3. Válassza ki **Ismerkedés**. (A Visual Studio verziójától függően a szöveg kis mértékben eltérhetnek. Egyes régebbi verziók esetében a **ingyenes próba megkezdése** inkább gombra.)

4. Válassza ki az előfizetését, majd válassza ki **erőforrás** > **regisztrálása**.

5. Az Application Insights hozzáadása a projekthez, után ellenőrizze, hogy erősítse meg, hogy az SDK legújabb stabil kiadását használja-e. Lépjen a **projekt** > **NuGet-csomagok kezelése** > **Microsoft.ApplicationInsights.AspNetCore** > Ha szükséges, válassza ki a **Frissítés**.

     ![Képernyőfelvétel a NuGet csomag képernyő kezelése az Application Insights-csomag frissítése a kijelölt](./media/asp-net-core/update-nuget-package.png)

6. Ha a választható tipp követni, és a projekthez hozzáadandó verziókövetés, majd lekérheti a **nézet** > **Team Explorer** > **módosítások** és Válassza ki az egyes fájlok az Application Insights telemetria hozzáadása végzett módosítások diff megjelenítéséhez.

## <a name="enable-application-insights-server-side-telemetry-without-visual-studio"></a>Az Application Insights kiszolgálóoldali telemetria (nélkül a Visual Studio) engedélyezése

1. Telepítse a [ASP.NET Core Application Insights SDK NuGet-csomagja](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Azt javasoljuk, hogy mindig a legújabb stabil verzióját használja. Kibocsátási megjegyzések az SDK találhatók a [nyissa meg a GitHub-adattárat forrás](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    A következő kódrészlet azt mutatja be, a módosítások a projekthez hozzáadandó `.csproj` fájlt.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
        </ItemGroup>
    ```

2. Adjon hozzá `services.AddApplicationInsightsTelemetry();` , a `ConfigureServices()` metódus az a `Startup` osztály. Az alábbi teljes példa.

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // code adding other services for your application
            services.AddMvc();
        }
    ```

3. Állítsa be a kialakítási kulcsot.

    Bár a kialakítási kulcsot biztosít jako argument Pro `AddApplicationInsightsTelemetry`, azt javasoljuk, hogy a konfigurációs adja meg a kialakítási kulcsot. A következő bemutatja, hogyan adjon meg egy kialakítási kulcsot az `appsettings.json`. Győződjön meg arról, hogy `appsettings.json` közzététel során az alkalmazás gyökér mappába másolja.

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

    Azt is megteheti a kialakítási kulcsot is adható meg az alábbi környezeti változókat valamelyikében.

    ÁLLÍTANI AZ APPINSIGHTS_INSTRUMENTATIONKEY

    ApplicationInsights:InstrumentationKey

    Példa:

    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    `APPINSIGHTS_INSTRUMENTATIONKEY` általában segítségével adja meg az üzembe helyezett Azure Web Apps alkalmazások számára.

    > [!NOTE]
    > Egy környezeti változó keresztül kód wins-ben megadott kialakítási kulcs `APPINSIGHTS_INSTRUMENTATIONKEY`, amely wins-más lehetőségekhez képest.

## <a name="run-your-application"></a>Az alkalmazás futtatása

 Futtassa az alkalmazást, és hozzá kérelmeket. Telemetria most el kell Application Insights halad. A következő telemetriát az Application Insights SDK automatikusan gyűjti.

|Kérések/függőségek |Részletek|
|---------------|-------|
|Kérelmek | A beérkező webes kérelmek az alkalmazáshoz. |
|Http/Https | A hívások `HttpClient`. |
|SQL | A hívások `SqlClient`. |
|[Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) | Az Azure Storage-kliens indított hívások. |
|[EventHub Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1\.1.0-s verzió vagy újabb verzió. |
|[ServiceBus Client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 3\.0.0-s verziójával vagy újabb verzió. |
|Azure Cosmos DB | Csak automatikusan rögzíti a HTTP/HTTPS használata esetén. Az Application Insights nem rögzíti a TCP-módot. |

### <a name="performance-counters"></a>Teljesítményszámlálók

Támogatja a [teljesítményszámlálók](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) ASP.NET Core korlátozódik, a következő

   * SDK verziója 2.4.1 és teljesítményszámlálók fent gyűjt, ha az alkalmazás fut, az Azure Web App (Windows)
   * SDK verziója 2.7.0-beta3 és teljesítményszámlálók fent gyűjt, ha az alkalmazás a Windows rendszert futtató, és célcsoport-kezelési `NETSTANDARD2.0` vagy újabb verziója.
   * Az alkalmazások .NET-keretrendszerre teljesítményszámlálók SDK összes verziói támogatottak.
   * Ez a cikk is frissülnek, amikor teljesítmény számláló támogatás a Linux.

### <a name="ilogger-logs"></a>ILogger naplók

[ILogger naplók](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) súlyosságú `Warning` vagy újabb rendszer automatikusan rögzített, az SDK-verzió 2.7.0-beta3 vagy újabb.

### <a name="live-metrics"></a>Élő metrikák

Eltarthat néhány percig, telemetriai adatok jelennek meg a portál elindításához. Gyorsan ellenőrizheti, ha minden megfelelően működik, akkor érdemes választani, [élő mérőszámok](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream), míg lehetővé kéri, hogy a futó alkalmazás.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Ügyféloldali Telemetria engedélyezése webes alkalmazásokhoz

A fenti lépések elegendőek a kiszolgálóoldali telemetriai adatok gyűjtésének megkezdéséhez. Ha az alkalmazás ügyféloldali összetevők, majd kövesse az alábbi lépéseket a gyűjtésének megkezdéséhez [használat telemetriai adatai](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview) onnan.

1. A `_ViewImports.cshtml`, injektálási hozzáadása:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. A `_Layout.cshtml`, insert HtmlHelper végéhez `<head>` szakaszt, de minden egyéb parancsfájl előtt. Ez a kódrészlet után bármely egyéni JavaScript-telemetriát az oldalról szeretne jelentést kell beszúrásra:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

A `.cshtml` fájlneveket, a fentiekben említett egy alapértelmezett MVC alkalmazássablon származnak. Végső soron megfelelően engedélyezéséhez az alkalmazás az ügyféloldali figyelés kell lennie a JavaScript-kódrészletet a `<head>` minden oldalán megjelenik az alkalmazáshoz, amely a figyelni kívánt szakasza. A kérelem sablon hozzáadása a Javascript-kódrészletet a `_Layout.cshtml` hatékonyan érheti el ezt a célt. Ha a projekt nem rendelkezik az adott fájl továbbra is felvehetőek [az ügyféloldali figyelés](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Szeretné csak kell vagy ad hozzá a JavaScript egy azzal egyenértékű fájlt, amely szabályozza a `<head>` az alkalmazást, vagy másik lehetőségként belüli összes lapokra sikerült felvenni a kódrészletet több személy lapok, bár ez nehéz lenne, és általában nincs ajánlott.

## <a name="configuring-application-insights-sdk"></a>Az Application Insights SDK konfigurálása

Application Insights SDK for ASP.NET Core testre szabható az alapértelmezett konfiguráció módosítását. Lehet, hogy az Application Insights SDK-t az ASP.NET felhasználói jól ismerik a konfigurációs `ApplicationInsights.config`, vagy módosításával `TelemetryConfiguration.Active`. ASP.NET Core, a konfiguráció másképp történik. Az ASP.NET Core SDK vesznek fel az alkalmazás és ASP.NET Core beépített segítségével konfigurált [függőségi beszúrást](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Szinte minden konfigurációmódosítás történik, az a `ConfigureServices()` módszere a `Startup.cs` osztályhoz, amennyiben másként nincs feltüntetve. Kövesse az alábbiakban további.

> [!NOTE]
>  Változó-konfigurációjának módosítása `TelemetryConfiguration.Active` az ASP.NET Core-alkalmazások nem ajánlott.

### <a name="configuring-using-applicationinsightsserviceoptions"></a>ApplicationInsightsServiceOptions használatával konfigurálása

Néhány gyakori beállításainak módosításához átadásával lehetséges `ApplicationInsightsServiceOptions` való `AddApplicationInsightsTelemetry`. Az alábbiakban egy példa látható.

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

A konfigurálható beállítások listája pontosan `ApplicationInsightsServiceOptions` található [Itt](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Mintavételezés

Application Insights SDK for ASP.NET Core FixedRate és adaptív mintavételezés is támogatja. Az adaptív mintavételezési alapértelmezés szerint engedélyezve van. Kövesse a [adaptív mintavételezés útmutatást](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)tekintse meg a mintavétel az ASP.NET Core-alkalmazások konfigurálása.

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers hozzáadása

[Telemetria inicializálók](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) küldött összes telemetriai adat az általános tulajdonságainak megadása szeretné használják.

Egy új `TelemetryInitializer`, adja hozzá a DependencyInjection tárolóba alább látható módon. `TelemetryInitializer`s DependencyInjection tárolóhoz adni fog észlelnie kell az SDK automatikusan.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Removing TelemetryInitializers

Távolítsa el az összes, vagy adott TelemetryInitializers, amelyek alapértelmezés szerint jelen van, használja az alábbi mintakód **után** hívása `AddApplicationInsightsTelemetry()`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // Remove a specific built-in TelemetryInitializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                         (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
    }
```

### <a name="adding-telemetryprocessors"></a>TelemetryProcessors hozzáadása

Egyéni telemetriát processzorok lehet hozzáadni a `TelemetryConfiguration` a bővítmény módszer használatával `AddApplicationInsightsTelemetryProcessor` a `IServiceCollection`. Telemetria processzorok használt [speciális forgatókönyvek szűrés](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) , hogy mit tartalmaz, vagy zárva a telemetriát küld az Application Insights szolgáltatás több közvetlenül lehet szabályozni az. Használja az alábbi példát.

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

### <a name="configuring-or-removing-default-telemetrymodules"></a>Konfigurálás vagy alapértelmezett fájl TelemetryModules eltávolítása

Az Application Insights telemetria modult használ, így [hasznos információk automatikus gyűjtése](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies) kapcsolatos, adott munkaterhelés konkrét további konfiguráció nélkül.

A következő automatikus adatgyűjtő modulok alapértelmezés szerint engedélyezve vannak, és automatikusan a telemetriai adatok gyűjtésének felelős. Is ezek le van tiltva, és úgy konfigurálva, hogy az alapértelmezett viselkedés módosítható.

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Minden olyan alapértelmezett `TelemetryModule`, használja a bővítmény módszert `ConfigureTelemetryModule<T>` a `IServiceCollection` az alábbi példában látható módon.

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

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-telemetry-channel"></a>Telemetria csatorna konfigurálása

A használt alapértelmezett csatorna a `ServerTelemetryChannel`. Az alábbi példa szerint felülbírálható.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="i-want-to-track-additional-telemetry-other-than-the-auto-collected-telemetry-how-do-i-do-it"></a>Szeretnék nyomon követheti az automatikusan gyűjtött telemetria eltérő további telemetriai adatokat. Hogyan készíthetek azt?

Szerezzen be egy példányát `TelemetryClient` a konstruktor injektálási használ, és hívja a szükséges `TrackXXX()` metódust. Nem javasoljuk, hogy hozzon létre új `TelemetryClient` példányok az ASP.NET Core-alkalmazás-példányként `TelemetryClient` már regisztrálva van a DI tároló, amely közös `TelemetryConfiguration` telemetriai adatot rest-tel. Hozzon létre egy új `TelemetryClient` példány ajánlott csak akkor, ha szeretné, hogy a többi a telemetria egy külön konfigurációs van. Az alábbi példa bemutatja, hogyan nyomon lehet követni további telemetria a vezérlő.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // use constructor injection to get TelemetryClient instance
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // call required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

 Tekintse meg [egyéni metrikákat az Application Insights API-referencia](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/) egyéni jelentések készítése az Application Insights-adatok leírását.

### <a name="some-visual-studio-templates-used-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Néhány Visual Studio-sablonok a IWebHostBuilder UseApplicationInsights() metódust segítségével az Application Insights engedélyezése. Még mindig érvényes felhasználási?

Ezzel a módszerrel az Application Insights engedélyezése érvényes, és a Visual Studio az előkészítési, illetve a az Azure Web App bővítményeket is használható. Azonban javasoljuk, hogy használjon `services.AddApplicationInsightsTelemetry()` néhány konfigurációs szabályozásához túlterheléssel biztosít. Mindkét módszerek belsőleg járjon el ugyanígy, ha nem tartozik a alkalmazni egyéni konfiguráció, a hívó vagy nem okoz gondot.

### <a name="i-am-deploying-my-aspnet-core-application-to-azure-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Saját ASP.NET Core-alkalmazás az Azure Web Appshez vagyok telepítése Kell továbbra is engedélyezhető a Web Apps Application Insights-bővítmény?

Az SDK-val, ahogyan az ebben a cikkben összeállítás során települ, ha nincs az App Service-portál a Application Insights-bővítmény engedélyezéséhez szükség. Akkor is, ha a bővítmény telepítve van, ki, amikor azt észleli, hogy az SDK-t már szerepel az alkalmazás biztonsági. Az Application Insights engedélyezése a bővítmény a Önnek nem kell a telepítése és frissítése az SDK-t. Azonban ez a cikk megfelelően az Application Insights engedélyezése csak olyan rugalmasabb, az alábbi okok miatt.
   * Application Insights Telemetria továbbra is működnek:
       * Az összes operációs rendszer – Windows, Linux, Mac rendszerre.
       * Az összes közzététele módok – önálló vagy keretrendszer-függő.
       * Az összes cél keretrendszereket, beleértve a teljes .NET-keretrendszer.
       * Az összes üzemeltetési lehetőségek – Azure Web APP, virtuális gépek, Linux, tárolók, az AKS, nem Azure-beli.
   * Telemetria látható helyileg, a Visual Studióban történő hibakeresése során.
   * Lehetővé teszi, hogy követési további egyéni telemetriát `TrackXXX()` API-t.
   * A konfiguráció teljes hozzáféréssel rendelkezik.

### <a name="can-i-enable-application-insights-monitoring-using-tools-like-status-monitor"></a>Lehet engedélyezni az Application Insights figyelési hasonló eszközökkel Állapotfigyelőt?

Nem. [Az állapotfigyelő](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) és a közelgő helyettesítő [Állapotfigyelőt v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) jelenleg támogatja az ASP.NET 4.x csak.

### <a name="i-have-an-aspnet-core-20-application-isnt-application-insights-automatically-enabled-without-me-doing-anything"></a>Az ASP.NET Core 2.0-alkalmazások vannak. Nem az Application Insights automatikusan engedélyezve van velem csinál semmit nélkül?

`Microsoft.AspNetCore.All` 2.0-s metapackage Application Insights SDK-t (2.1.0-ás) tartalmazza, és futtatja az alkalmazást a Visual Studio hibakereső funkcióját, ha a Visual Studio Application Insights lehetővé teszi, és telemetriát helyileg magát az IDE-ben jeleníti meg. Telemetria az Application Insights szolgáltatásba nem lett elküldve, egy kialakítási kulcs explicit módon není zadána klauzule. Javasoljuk, hogy kövesse a jelen cikkben lévő utasítások még a 2.0-hoz készült Application Insights engedélyezése alkalmazásokat.

### <a name="i-run-my-application-in-linux-are-all-features-supported-in-linux-as-well"></a>Az alkalmazásom Linux rendszeren futtatható. Minden funkció támogatja a Linux is?

* Igen. Az SDK szolgáltatás támogatása a következő azonos az összes platform, a következő kivételekkel:

    * Teljesítményszámlálók még nem támogatottak a nem-Windows.
    * Annak ellenére, hogy `ServerTelemetryChannel` engedélyezve van alapértelmezés szerint az alkalmazás fut, Linux vagy MacOS rendszeren, ha a csatorna nem hoz létre automatikusan egy helyi tároló mappa a telemetriai adatokat ideiglenesen hálózati merül fel. Ez a korlátozás hatására a telemetriai adatok elvesznek, ha vannak az átmeneti hálózati vagy kiszolgálói problémák. A probléma megoldása van a felhasználó egy helyi mappába a csatorna konfigurálhatja az alább látható módon.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure channel to use the given folder to temporarily
        // store telemetry items during network or application insights server issues.
        // User should ensure that the given folder already exists,
        // and that application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="open-source-sdk"></a>Open-source SDK
[Olvassa el, és hozzájárulnak a kód](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Videó

- Külső részletes videó [Application Insights konfigurálása a .NET Core és a Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) sablon nélkül.
- Külső részletes videó [Application Insights konfigurálása a .NET Core és a Visual Studio Code](https://youtu.be/ygGt84GDync) sablon nélkül.

## <a name="next-steps"></a>További lépések
* [Ismerje meg a felhasználói folyamatok](../../azure-monitor/app/usage-flows.md) tudni, hogy a felhasználók hogyan navigálnak az alkalmazáson keresztül.
* [Pillanatkép gyűjtésének konfigurálása](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) forráskód és a változók állapotának megtekintéséhez jelenleg a függvény kivételt vált ki.
* [Az API-val](../../azure-monitor/app/api-custom-events-metrics.md) küldése a saját események és mérőszámok az alkalmazás teljesítményének és használatának részletesebb megjelenítéséhez.
* Használat [rendelkezésre állási tesztek](../../azure-monitor/app/monitor-web-app-availability.md) ellenőrizheti az alkalmazás folyamatosan az egész világon.
