---
title: Az Azure Application Insights az ASP.NET Core-alkalmazások |} A Microsoft Docs
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
ms.openlocfilehash: 5ea7ec41ccc721e8eafda56aa7463505ba089845
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827811"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights az ASP.NET Core-alkalmazások

Ez a cikk bemutatja, hogyan készült Application Insights engedélyezése egy [ASP.NET Core](https://docs.microsoft.com/aspnet/core) alkalmazás. Amikor befejezte a jelen cikkben lévő utasítások, az Application Insights kérelmeket, függőségeket, kivételek, teljesítményszámlálók, szívverések és naplók-fájlokból gyűjt az ASP.NET Core-alkalmazást. 

Itt fogjuk használni a példa egy [MVC alkalmazás](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) , amelynek a célja `netcoreapp2.2`. Ezek az utasítások minden ASP.NET Core-alkalmazás alkalmazhat.

## <a name="supported-scenarios"></a>Támogatott esetek

A [Application Insights SDK for ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) figyelheti az alkalmazások, függetlenül attól, hol és hogyan futnak. Ha az alkalmazás fut, és rendelkezik hálózati kapcsolattal az Azure-ba, telemetriai adatokat lehessen gyűjteni. Application Insights figyelési támogatott mindenhol .NET Core használata támogatott. Támogatási tartalmazza:
* **Operációs rendszer**: Windows, Linux és Mac rendszerre.
* **Üzemeltetési metódus**: A folyamat vagy folyamaton kívül. 
* **Az üzembe helyezési módszer**: Keretrendszer függő vagy önálló.
* **Webkiszolgáló**: Az IIS (Internet Information Server) vagy a Kestrel. 
* **Üzemeltetési platformot**: A Web Apps funkció az Azure App Service, Azure virtuális Gépen, Docker, Azure Kubernetes Service (AKS), és így tovább.
* **IDE**: A Visual Studio, a VS Code, vagy parancssort.

## <a name="prerequisites"></a>Előfeltételek

- Egy működő ASP.NET Core-alkalmazás. Ha szeretne létrehozni egy ASP.NET Core-alkalmazást, kövesse ezt [oktatóanyag ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started/).
- Egy érvényes az Application Insights-kialakítási kulcsot. Ez a kulcs bármilyen telemetriát küldjön az Application Insights szükséges. Ha szeretne létrehozni egy új Application Insights-erőforrást egy kialakítási, tekintse meg lekérni [hozzon létre egy Application Insights-erőforrást](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Az Application Insights kiszolgálóoldali telemetria (Visual Studio) engedélyezése

1. Nyissa meg a projektjét a Visual Studióban.

    > [!TIP]
    > Ha szeretné, állíthat be verziókövetés a projekt így nyomon követheti az Application Insights által végrehajtott módosítások. Válassza ki ahhoz, hogy a verziókövetés, **fájl** > **Hozzáadás a Forráskezelőhöz**.

2. Válassza ki **projekt** > **Application Insights Telemetria hozzáadása**.

3. Válassza ki **Ismerkedés**. A kijelölés szövege a Visual Studio verziójától függően változhat. Néhány korábbi verzióihoz egy **ingyenes próba megkezdése** inkább gombra.

4. Válassza ki előfizetését. Válassza ki **erőforrás** > **regisztrálása**.

5. Az Application Insights hozzáadása a projekthez, után ellenőrizze, hogy erősítse meg, hogy az SDK legújabb stabil kiadását használja-e. Lépjen a **projekt** > **NuGet-csomagok kezelése** > **Microsoft.ApplicationInsights.AspNetCore**. Ha szeretne, válassza a **frissítés**.

     ![Képernyőfelvétel: hol kell válassza ki az Application Insights-csomag frissítése](./media/asp-net-core/update-nuget-package.png)

6. Ha a választható tipp követni, és a projekthez hozzáadandó verziókövetés, lépjen a **nézet** > **Team Explorer** > **módosítások**. Ezután válassza ki az egyes fájlokat egy Application Insights telemetria által végzett módosítások diff nézetét lássák.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Az Application Insights kiszolgálóoldali telemetria (nincsenek Visual Studio) engedélyezése

1. Telepítse a [ASP.NET Core Application Insights SDK NuGet-csomagja](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Azt javasoljuk, hogy mindig a legújabb stabil verzióját használja. Keresse meg a kibocsátási megjegyzések az SDK a a [nyílt forráskódú GitHub-adattárat](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    Az alábbi példakód bemutatja a módosításokat a projekthez hozzáadandó `.csproj` fájlt.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. Adja hozzá `services.AddApplicationInsightsTelemetry();` , a `ConfigureServices()` metódus az a `Startup` osztályt, ahogy ebben a példában is látható:

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

    Bár a kialakítási kulcs megadhat jako argument Pro `AddApplicationInsightsTelemetry`, azt javasoljuk, hogy a konfigurációs adja meg a kialakítási kulcsot. Az alábbi példakód bemutatja, hogyan adjon meg egy kialakítási kulcsot az `appsettings.json`. Győződjön meg arról, hogy `appsettings.json` közzététele során az alkalmazás gyökér mappába másolja.

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

    Azt is megteheti adja meg a kialakítási kulcsot az alábbi környezeti változókat közül választhat:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Példa:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    Általában `APPINSIGHTS_INSTRUMENTATIONKEY` a kialakítási kulcsot a Web Apps szolgáltatásban üzembe helyezett alkalmazások esetén adja meg.

    > [!NOTE]
    > Egy kialakítási kulcsot a megadott kód wins-ben a környezeti változó keresztül `APPINSIGHTS_INSTRUMENTATIONKEY`, amely wins-más lehetőségekhez képest.

## <a name="run-your-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást, és hozzá kérelmeket. Az Application Insights most áramlásának telemetriai adatokat. Az Application Insights SDK automatikusan gyűjti a következő telemetriát.

|Kérések/függőségek |Részletek|
|---------------|-------|
|Kérelmek | A beérkező webes kérelmek az alkalmazáshoz. |
|HTTP- vagy HTTPS | A hívások `HttpClient`. |
|SQL | A hívások `SqlClient`. |
|[Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) | Az Azure Storage-ügyféllel indított hívások. |
|[EventHubs client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1\.1.0-s verzió vagy újabb. |
|[ServiceBus client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 3\.0.0-s verziójával, és később. |
|Azure Cosmos DB | Nyomon követi a automatikusan, csak a HTTP/HTTPS használata esetén. Az Application Insights nem rögzíti a TCP-módot. |

### <a name="performance-counters"></a>Teljesítményszámlálók

Támogatja a [teljesítményszámlálók](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) az ASP.NET Core korlátozva:

   * SDK-verziókra 2.4.1, és később gyűjtése teljesítményszámlálók, ha az alkalmazás fut a Web Apps (Windows).
   * SDK-verziók 2.7.0-beta3 és újabb verziók gyűjtése teljesítmény teljesítményszámlálók, ha az alkalmazás fut a Windows és a tárolók `NETSTANDARD2.0` vagy újabb.
   * .NET-keretrendszerre alkalmazások az SDK összes verziói támogatják a teljesítményszámlálókat.
 
Ez a cikk is frissülnek, amikor teljesítmény számláló támogatás a Linux.

### <a name="ilogger-logs"></a>ILogger naplók

[ILogger naplók](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) súlyosságú `Warning` vagy annál nagyobb az SDK-verziók 2.7.0-beta3 és későbbi automatikusan rögzítve lesznek.

### <a name="live-metrics"></a>Élő metrikák

Telemetria indítása a portálon megjelenő előtt néhány percbe is telhet. Gyorsan győződjön meg arról, hogy minden megfelelően működik, érdemes használni [élő mérőszámok](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) amikor Ön kéréseket küld a futó alkalmazás.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Webalkalmazások ügyféloldali telemetria engedélyezése

Az előző lépések vonatkoznak a kiszolgálóoldali telemetriai adatok gyűjtésének megkezdéséhez nyújt segítséget. Ha az alkalmazás ügyféloldali összetevők, kövesse a következő lépéseket gyűjtésének megkezdéséhez [használat telemetriai adatai](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview).

1. A `_ViewImports.cshtml`, injektálási hozzáadása:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. A `_Layout.cshtml`, insert `HtmlHelper` végén a `<head>` szakaszt, de minden egyéb parancsfájl előtt. Ha azt szeretné, a lapon minden olyan egyéni JavaScript telemetriai jelentését, betöltése után ez a kódrészlet:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

A `.cshtml` korábban hivatkozott fájlok nevei megkülönböztetik alapértelmezett MVC alkalmazást sablon alapján. Végső soron megfelelően az ügyféloldali figyelési alkalmazás engedélyezni szeretné, ha a JavaScript-kódrészletet szerepelnie kell a `<head>` minden oldalán megjelenik az alkalmazáshoz, amely a figyelni kívánt szakasza. Adja hozzá a JavaScript-kódrészletet a is elérheti ezt a célt a alkalmazás sablon `_Layout.cshtml`. 

Ha nem adja meg a projekt `_Layout.cshtml`, továbbra is felvehetőek [az ügyféloldali figyelés](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Ezt megteheti egy azzal egyenértékű fájlt, amely szabályozza a JavaScript-kódrészletet hozzáadásával is a `<head>` lapokat az alkalmazáson belül. Vagy több oldalra is hozzáadhat a kódrészletet, de ez a megoldás nehezen fenntartható és általában nem ajánlott.

## <a name="configure-the-application-insights-sdk"></a>Az Application Insights SDK konfigurálása

Testre szabhatja az Application Insights SDK for ASP.NET Core, az alapértelmezett konfiguráció módosítása. Lehet, hogy ismeri a konfiguráció módosításának használatával az Application Insights SDK-t az ASP.NET felhasználói `ApplicationInsights.config` vagy módosításával `TelemetryConfiguration.Active`. Eltérően az ASP.NET Core konfigurációjának módosítása Az ASP.NET Core SDK hozzáadása az alkalmazáshoz, és konfigurálja az ASP.NET Core beépített [függőségi beszúrást](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). A szinte az összes konfigurációs módosítások a `ConfigureServices()` módszere a `Startup.cs` osztályhoz, kivéve, ha más módon irányítja. Az alábbi szakaszok nyújtanak további információt.

> [!NOTE]
> Az ASP.NET Core-alkalmazások, módosítja a konfigurációt módosításával `TelemetryConfiguration.Active` nem támogatott.

### <a name="using-applicationinsightsserviceoptions"></a>ApplicationInsightsServiceOptions használatával

Néhány gyakori beállítások módosíthatók átadásával `ApplicationInsightsServiceOptions` való `AddApplicationInsightsTelemetry`, amint az ebben a példában:

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

További információkért lásd: a [a konfigurálható beállítások `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Mintavételezés

Az Application Insights SDK for ASP.NET Core a rögzített és adaptív mintavételezés is támogatja. Az adaptív mintavételezési alapértelmezés szerint engedélyezve van. 

További információkért lásd: [adaptív mintavételezés ASP.NET Core-alkalmazások konfigurálása](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers hozzáadása

Használat [telemetriai inicializálók](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) való küldött összes telemetriai adat az általános tulajdonságainak megadása.

Vegye fel az új `TelemetryInitializer` , a `DependencyInjection` tároló az alábbi kódban látható módon. Az SDK automatikusan felveszi bármely `TelemetryInitializer` , bekerül a `DependencyInjection` tároló.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Removing TelemetryInitializers

Alapértelmezés szerint telemetriai inicializálók találhatók. Távolítsa el az összes, vagy adott telemetriai inicializálók, használja az alábbi mintakód *után* hívja `AddApplicationInsightsTelemetry()`.

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

### <a name="adding-telemetry-processors"></a>Telemetria processzorok hozzáadása

Adhat hozzá egyéni telemetriát processzorral `TelemetryConfiguration` a bővítmény módszer használatával `AddApplicationInsightsTelemetryProcessor` a `IServiceCollection`. Használhatja a telemetriai adatok processzorra [speciális forgatókönyvek szűrés](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) , hogy mit tartalmaz vagy zárva a telemetriát küld az Application Insights szolgáltatás több közvetlenül lehet szabályozni az. Használja az alábbi példát.

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

Az Application Insights telemetria-modulok használ [hasznos információk automatikus gyűjtését](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies) kapcsolatos, adott munkaterhelés konkrét további konfiguráció nélkül.

A következő automatikus – adatgyűjtő modulok alapértelmezés szerint engedélyezve vannak. Ezeket a modulokat automatikusan a telemetriai adatok gyűjtésének felelős. Tiltsa le, vagy konfigurálhatók úgy, hogy az alapértelmezett viselkedés módosítható.

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Minden olyan alapértelmezett `TelemetryModule`, használja a bővítmény módszert `ConfigureTelemetryModule<T>` a `IServiceCollection`, az alábbi példában látható módon.

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

### <a name="configuring-a-telemetry-channel"></a>A telemetriai adatok csatorna konfigurálása

Az alapértelmezett csatorna `ServerTelemetryChannel`. Felülbírálhatja az alábbi példában látható módon.

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

### <a name="disable-telemetry-dynamically"></a>Telemetria dinamikusan letiltása

Ha le kívánja tiltani telemetriai feltételesen és dinamikusan, esetleg elhárítható `TelemetryConfiguration` példányon, amelyen az ASP.NET Core függőségi injektálási tároló bárhol a kódba, és állítsa be `DisableTelemetry` azt a jelzőt.

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

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Hogyan követheti nyomon, automatikusan nem gyűjtött telemetria?

Első példányát `TelemetryClient` a konstruktor injektálási használ, és hívja a szükséges `TrackXXX()` metódust. Nem javasoljuk, hogy új létrehozása `TelemetryClient` ASP.NET Core alkalmazás-példányok. Egy egyszeres példányát `TelemetryClient` már regisztrálva van a `DependencyInjection` tároló, amely közös `TelemetryConfiguration` telemetriai adatot rest-tel. Hozzon létre egy új `TelemetryClient` példány ajánlott csak ha szüksége van egy konfigurációt, amely elkülönül a többi a telemetriát. 

Az alábbi példa bemutatja, hogyan nyomon lehet követni további telemetria a vezérlő.

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

Egyéni adatok, jelentések készítése az Application Insights kapcsolatos további információkért lásd: [egyéni metrikákat az Application Insights API-referencia](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Néhány Visual Studio-sablonok a UseApplicationInsights() metódust IWebHostBuilder az Application Insights engedélyezésére szolgáló. Még mindig érvényes felhasználási?

Igen, ezzel a módszerrel az Application Insights engedélyezése esetén érvényes. Ezzel a technikával a Visual Studio-előkészítési és a Web Apps-bővítmények a szolgál. Javasoljuk azonban, használatával `services.AddApplicationInsightsTelemetry()` mert túlterheléssel szabályozhatja a konfigurálást. Mindkét módszer végre ugyanezt a cégen belül, így nem kell egyéni konfigurációt alkalmazni, ha mindkét módszer segítségével meghívhatja.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Web Apps szolgáltatásban az ASP.NET Core alkalmazás vagyok telepítése Kell továbbra is engedélyezhető a Web Apps Application Insights-bővítmény?

Az SDK-val, ahogyan az ebben a cikkben összeállítás során települ, ha nem kívánja engedélyezni az App Service-portálról az Application Insights bővítményt. Akkor is, ha a bővítmény telepítve van, ki, amikor azt észleli, hogy az SDK-t már szerepel az alkalmazás biztonsági. Ha engedélyezi az Application Insights a bővítményt, nem kell telepíteni, és az SDK frissítése. De ha ez a cikk utasításait követve engedélyezi az Application Insights, mert több beleszólása van:

   * Application Insights telemetria továbbra is működnek:
       * Az összes operációs rendszer, beleértve a Windows, Linux és Mac rendszerre.
       * Az összes közzététele módok, például önálló vagy függő keretrendszer.
       * Az összes cél keretrendszereket, beleértve a teljes .NET-keretrendszer.
       * Az összes üzemeltetési beállításokat, beleértve a Web Apps, virtuális gépek, Linux, tárolók, Azure Kubernetes Service-ben és nem Azure-beli üzemeltetéséhez.
   * Telemetria láthatja helyileg amikor hibakeresése a Visual Studióból.
   * További egyéni telemetriát használatával követheti nyomon a `TrackXXX()` API-t.
   * A konfiguráció teljes hozzáféréssel rendelkezik.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Lehet engedélyezni az Application Insights Állapotfigyelőt más eszközök használatával figyelése?

Nem. [Az állapotfigyelő](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) és [Állapotfigyelőt v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) jelenleg támogatja az ASP.NET 4.x csak.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Az Application Insights automatikusan engedélyezve van az ASP.NET Core 2.0 az alkalmazásom?

A `Microsoft.AspNetCore.All` 2.0 metapackage tartalmazza az Application Insights SDK-t (2.1.0-ás). Ha futtatja az alkalmazást a Visual Studio hibakereső funkcióját, a Visual Studio Application Insights lehetővé teszi, és telemetriát helyileg magát az IDE-ben jeleníti meg. Telemetria az Application Insights szolgáltatásba nem lett elküldve, ha egy kialakítási kulcs lett megadva. Javasoljuk, hogy kövesse a jelen cikkben lévő utasítások még a 2.0-hoz készült Application Insights engedélyezése alkalmazásokat.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Futtathatom saját alkalmazás Linux rendszeren, ha az összes funkciók támogatottak?

Igen. Az SDK szolgáltatás támogatása megegyezik az összes platform, a következő kivételekkel:

* Teljesítményszámlálók csak a Windows támogatott.
* Annak ellenére, hogy `ServerTelemetryChannel` engedélyezve van alapértelmezés szerint az alkalmazás fut, Linux vagy MacOS rendszeren, ha a csatorna nem hozza létre automatikusan egy helyi tároló mappa a telemetriai adatokat ideiglenesen hálózati merül fel. Ez a korlátozás miatt telemetriai elvész ideiglenes hálózati vagy kiszolgálói problémák esetén. A probléma megkerüléséhez, hozzon létre egy helyi mappába a csatorna:

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

## <a name="open-source-sdk"></a>Open-source SDK

[Olvassa el, és hozzájárulnak a kód](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Videó

- Tekintse meg a külső részletes videó [az Application Insights beállítása a .NET Core és a Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) sablon nélkül.
- Tekintse meg a külső részletes videó [az Application Insights beállítása a .NET Core és a Visual Studio Code](https://youtu.be/ygGt84GDync) sablon nélkül.

## <a name="next-steps"></a>További lépések

* [Ismerje meg a felhasználói folyamatok](../../azure-monitor/app/usage-flows.md) tudni, hogy a felhasználók hogyan navigálnak az alkalmazáson keresztül.
* [Konfiguráljon egy pillanatkép gyűjteményt](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) forráskód és a változók állapotának megtekintéséhez jelenleg a függvény kivételt vált ki.
* [Az API-val](../../azure-monitor/app/api-custom-events-metrics.md) küldése a saját események és metrikák, az alkalmazás teljesítményének és használatának részletes nézetének megtekintéséhez.
* Használat [rendelkezésre állási tesztek](../../azure-monitor/app/monitor-web-app-availability.md) ellenőrizheti az alkalmazás folyamatosan az egész világon.
* [Függőségi beszúrást az ASP.NET Core](https://docs.microsoft.com/aspnet/fundamentals/dependency-injection)
