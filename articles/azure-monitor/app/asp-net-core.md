---
title: Azure-Application Insights ASP.NET Core alkalmazásokhoz | Microsoft Docs
description: ASP.NET Core webalkalmazások figyelése a rendelkezésre állás, a teljesítmény és a használat érdekében.
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
ms.openlocfilehash: a48c2fdcce5126747f00cd3b901839864d438346
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058279"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights ASP.NET Core alkalmazásokhoz

Ez a cikk azt ismerteti, hogyan engedélyezhető a Application Insights [ASP.net Core](https://docs.microsoft.com/aspnet/core) alkalmazáshoz. A cikkben szereplő utasítások elvégzése után a Application Insights összegyűjti a kérelmeket, a függőségeket, a kivételeket, a teljesítményszámlálókat, a szívveréseket és a naplókat a ASP.NET Core alkalmazásból.

Az itt használt példa egy [MVC-alkalmazás](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) , amely a célokat `netcoreapp2.2`célozza. Ezek az utasítások az összes ASP.NET Core alkalmazásra alkalmazhatók.

## <a name="supported-scenarios"></a>Támogatott esetek

A [ASP.NET Core Application INSIGHTS SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) -val a futtatásuk helyétől függetlenül nyomon követheti az alkalmazásait. Ha az alkalmazás fut, és hálózati kapcsolattal rendelkezik az Azure-hoz, a telemetria gyűjthet. A Application Insights figyelése mindenhol támogatott a .NET Core-ban. Támogatás kiterjed a következőkre:
* **Operációs rendszer**: Windows, Linux vagy Mac.
* **Üzemeltetési módszer**: Folyamatban vagy folyamatban. 
* **Üzembe helyezési módszer**: A keretrendszer függő vagy önálló.
* **Webkiszolgáló**: IIS (Internet Information Server) vagy vércse. 
* **Üzemeltetési platform**: A Azure App Service, az Azure VM, a Docker, az Azure Kubernetes szolgáltatás (ak) Web Apps funkciója stb.
* **IDE**: A Visual Studio, a VS Code vagy a Command line.

> [!NOTE]
> Ha a ASP.NET Core 3,0 – előzetes verzióval együtt használja a Application Insights, használja a [2.8.0-Beta2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0-beta2) vagy újabb verziót. Ez az egyetlen ismert verzió, amely a ASP.NET Core 3,0-es verziójával jól működik. A Visual Studio-alapú bevezetést a ASP.NET Core 3,0-es alkalmazások esetében még nem támogatja.

## <a name="prerequisites"></a>Előfeltételek

- Működő ASP.NET Core alkalmazás. Ha ASP.NET Core alkalmazást kell létrehoznia, kövesse ezt az [ASP.net Core oktatóanyagot](https://docs.microsoft.com/aspnet/core/getting-started/).
- Érvényes Application Insights kialakítási kulcs. Ez a kulcs szükséges ahhoz, hogy bármilyen telemetria küldjön a Application Insightsnak. Ha létre kell hoznia egy új Application Insights-erőforrást a kialakítási kulcs beszerzéséhez, tekintse meg a [Application Insights erőforrás létrehozása](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)című témakört.

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Application Insights kiszolgálóoldali telemetria engedélyezése (Visual Studio)

1. Nyissa meg a projektet a Visual Studióban.

    > [!TIP]
    > Ha szeretné, beállíthatja a projekthez tartozó verziókövetés beállítását, hogy nyomon követhesse a Application Insights által végrehajtott összes módosítást. A verziókövetés engedélyezéséhez válassza a **fájl** > **Hozzáadás a forrás vezérlőelemhez**lehetőséget.

2. Válassza a **projekt** > **Hozzáadás Application Insights telemetria**elemet.

3. Válassza az első **lépések**lehetőséget. A kiválasztott szöveg változhat a Visual Studio verziójától függően. Egyes korábbi verziók esetén az **ingyenes indítás** gomb használható.

4. Válassza ki előfizetését. Ezután válassza az **erőforrás** > -**regisztráció**lehetőséget.

5. Miután hozzáadta Application Insights a projekthez, ellenőrizze, hogy az SDK legújabb stabil kiadását használja-e. Ugrás a **Project** > **NuGet-csomagok** > kezelése**Microsoft. ApplicationInsights. AspNetCore**. Ha szükséges, válassza a **frissítés**lehetőséget.

     ![Képernyőfelvétel: a Application Insights csomag kiválasztása a frissítéshez](./media/asp-net-core/update-nuget-package.png)

6. Ha követte a választható tippet, és hozzáadta a projektet a forrás vezérlőelemhez, lépjen a**Team Explorer** > **változások** **megtekintése** > elemre. Ezután válassza ki az egyes fájlokat, és tekintse meg a Application Insights telemetria által végzett módosítások különbségeit.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Application Insights kiszolgálóoldali telemetria engedélyezése (nincs Visual Studio)

1. Telepítse a [ASP.NET Core Application INSIGHTS SDK NuGet-csomagot](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Javasoljuk, hogy mindig a legújabb stabil verziót használja. Az SDK teljes kibocsátási megjegyzéseit a [nyílt forráskódú GitHub-](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)tárházban találja.

    A következő mintakód a projekt `.csproj` fájljában felvenni kívánt módosításokat mutatja be.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. Adja `services.AddApplicationInsightsTelemetry();` hozzá a `ConfigureServices()` metódust `Startup` az osztályban, ahogy az alábbi példában is látható:

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

    Bár a kialakítási kulcsot argumentumként `AddApplicationInsightsTelemetry`is megadhatja, javasoljuk, hogy adja meg a kialakítási kulcsot a konfigurációban. Az alábbi mintakód bemutatja, hogyan adható meg a kialakítási kulcs a `appsettings.json`alkalmazásban. Győződjön meg `appsettings.json` arról, hogy a közzététel során a rendszer átmásolja az alkalmazás gyökerének mappájába.

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

    A web Appsre központilag telepített alkalmazások rendszerállapot- `APPINSIGHTS_INSTRUMENTATIONKEY` kulcsát általában megadja.

    > [!NOTE]
    > A kód WINS-ben megadott rendszerállapot-kulcs a környezeti `APPINSIGHTS_INSTRUMENTATIONKEY`változón keresztül, amely más beállításokon keresztül nyerhető.

## <a name="run-your-application"></a>Alkalmazás futtatása

Futtassa az alkalmazást, és tegye a kérelmeket. A telemetria-nek most Application Insightsre kell irányulnia. A Application Insights SDK automatikusan összegyűjti a következő telemetria.

|Kérelmek/függőségek |Részletek|
|---------------|-------|
|Kérelmek | Beérkező webes kérelmek az alkalmazásba. |
|HTTP vagy HTTPS | A-vel `HttpClient`végzett hívások. |
|SQL | A-vel `SqlClient`végzett hívások. |
|[Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) | Az Azure Storage-ügyféllel kezdeményezett hívások. |
|[EventHubs client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | A 1.1.0 és újabb verziók. |
|[ServiceBus client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 3\.0.0 és újabb verziók. |
|Azure Cosmos DB | Automatikusan nyomon követhető, ha a rendszer HTTP/HTTPS protokollt használ. A Application Insights nem rögzíti a TCP-módot. |

### <a name="performance-counters"></a>Teljesítményszámlálók

A [teljesítményszámlálók](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) támogatása ASP.net Core korlátozott:

* Az SDK 2.4.1-es és újabb verziói a teljesítményszámlálókat gyűjtik, ha az alkalmazás az Azure Web Apps (Windows) rendszeren fut.
* A 2.7.1-es és újabb verziójú SDK-verziók akkor gyűjtik a teljesítményszámlálókat, ha `NETSTANDARD2.0` az alkalmazás Windows-és célhelyeken vagy később fut.
* A .NET-keretrendszert célzó alkalmazások esetében az SDK összes verziója támogatja a teljesítményszámlálókat.
* Az SDK a 2.8.0-beta3 és újabb verziókban támogatja a CPU/memória számlálót Linux rendszeren. A Linux nem támogatja a többi számlálót. A Linux-(és más nem Windows-környezetekben található) rendszerszámlálók használatának ajánlott módja a [EventCounters](#eventcounter) használata.

### <a name="eventcounter"></a>EventCounter

A [EventCounter](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md)egy platformfüggetlen módszer a .NET/.net Core-ban lévő számlálók közzétételére és felhasználására. Bár ez a szolgáltatás korábban is létezett, nem voltak olyan beépített szolgáltatók, akik közzétették ezeket a számlálókat. A .NET Core 3,0-as verziótól kezdődően több számláló is közzé van téve, például CLR-számlálók, ASP.NET Core számlálók stb.

Az SDK a 2.8.0-beta3 és újabb verziókban támogatja a EventCounters gyűjteményét. Alapértelmezés szerint az SDK a következő számlálókat gyűjti, és ezek a számlálók Metrikaböngésző vagy az PerformanceCounter tábla elemzési lekérdezés használatával kérhetők le. A számlálók neve "Category |" formában jelenik meg. Számláló ".

|Category | Számláló|
|---------------|-------|
|System. Runtime | CPU – használat |
|System. Runtime | munkakészlet |
|System. Runtime | GC-heap-size |
|System. Runtime | Gen-0-GC-darabszám |
|System. Runtime | Gen-1 – GC-darabszám |
|System. Runtime | Gen-2 – GC – darabszám |
|System. Runtime | idő – GC |
|System. Runtime | Gen-0 – méret |
|System. Runtime | 1\. generációs méret |
|System. Runtime | 2\. generációs méret |
|System. Runtime | Kissné méret |
|System. Runtime | foglalási – arány |
|System. Runtime | szerelvény – darabszám |
|System. Runtime | kivételek száma |
|System. Runtime | szálkészlet munkaszála belépett – szálak száma |
|System. Runtime | figyelő – zárolási tartalom – darabszám |
|System. Runtime | szálkészlet munkaszála belépett – várólista hossza |
|System. Runtime | szálkészlet munkaszála belépett – befejezett elemek – darabszám |
|System. Runtime | aktív – időzítő – darabszám |
|Microsoft. AspNetCore. hosting | kérelmek/másodperc |
|Microsoft. AspNetCore. hosting | kérelmek összesen |
|Microsoft. AspNetCore. hosting | aktuális – kérelmek |
|Microsoft. AspNetCore. hosting | Sikertelen – kérelmek |

### <a name="ilogger-logs"></a>ILogger-naplók

A [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) -naplókat a `Warning` rendszer automatikusan rögzíti a 2.7.0-beta3 és újabb SDK-verziókban.

### <a name="live-metrics"></a>Élő metrikák

Néhány percet is igénybe vehet, mielőtt a telemetria elindul a portálon. Az összes működésének gyors biztosításához érdemes [élő metrikákat](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) használni, amikor kéréseket végez a futó alkalmazáshoz.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Ügyféloldali telemetria engedélyezése webalkalmazásokhoz

Az előző lépések elegendő segítséget nyújtanak a kiszolgálóoldali telemetria gyűjtésének megkezdéséhez. Ha az alkalmazás ügyféloldali összetevőket tartalmaz, kövesse a következő lépéseket a [használati telemetria](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)gyűjtésének megkezdéséhez.

1. A `_ViewImports.cshtml`alkalmazásban adja hozzá a befecskendezést:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. A `_Layout.cshtml`alkalmazásban `HtmlHelper` szúrja be a `<head>` szakaszt a szakasz végén, de minden más parancsfájl előtt. Ha az oldalról bármilyen egyéni JavaScript-telemetria szeretne jelenteni, szúrja be azt a kódrészlet után:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

A `.cshtml` korábban hivatkozott fájlnevek egy alapértelmezett MVC-alkalmazás sablonból származnak. Végül, ha megfelelően szeretné engedélyezni az ügyféloldali figyelést az alkalmazáshoz, a JavaScript- `<head>` kódrészletnek szerepelnie kell a figyelni kívánt alkalmazás minden oldalának szakaszában. Ezt a célt ehhez az alkalmazás-sablonhoz a JavaScript- `_Layout.cshtml`kódrészlet hozzáadásával érheti el. 

Ha a projekt nem tartalmazza `_Layout.cshtml`, az [ügyféloldali figyelést](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring)továbbra is hozzáadhatja. Ezt úgy teheti meg, hogy hozzáadja a JavaScript-kódrészletet egy ezzel egyenértékű `<head>` fájlhoz, amely az alkalmazáson belüli összes oldalt vezérli. Emellett a kódrészletet több oldalra is hozzáadhatja, de a megoldás nehezen karbantartható, és általában nem ajánlott.

## <a name="configure-the-application-insights-sdk"></a>A Application Insights SDK konfigurálása

Az alapértelmezett konfiguráció módosításához testreszabhatja a ASP.NET Core Application Insights SDK-t. Előfordulhat, hogy a Application Insights ASP.net SDK felhasználói megismerik a konfiguráció `ApplicationInsights.config` `TelemetryConfiguration.Active`módosítással történő módosítását. A konfigurációt a ASP.NET Core eltérő módon változtathatja meg. Adja hozzá a ASP.NET Core SDK-t az alkalmazáshoz, és konfigurálja ASP.NET Core beépített [függőségi befecskendezés](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)használatával. Szinte minden konfigurációs módosítást hajthat `ConfigureServices()` végre az `Startup.cs` osztály metódusában, hacsak nem irányította másképpen. A következő fejezetei további információkat nyújtanak.

> [!NOTE]
> ASP.net Core alkalmazásokban a konfiguráció `TelemetryConfiguration.Active` módosításának módosítása nem támogatott.

### <a name="using-applicationinsightsserviceoptions"></a>A ApplicationInsightsServiceOptions használata

A következő példához hasonlóan módosíthat néhány gyakori beállítást `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetry`, ahogy az alábbi példában is látható:

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

A beállítások teljes listája`ApplicationInsightsServiceOptions`

|Beállítás | Leírás | Alapértelmezett
|---------------|-------|-------
|EnableQuickPulseMetricStream | LiveMetrics funkció engedélyezése/letiltása | true
|EnableAdaptiveSampling | Adaptív mintavételezés engedélyezése/letiltása | true
|EnableHeartbeat | A szívverések funkció engedélyezése/letiltása, amely rendszeres időközönként (15 perces alapértelmezett) a "HeartBeatState" nevű egyéni metrikát küldi el a (z), például a .NET-es verzióval, az Azure-környezettel kapcsolatos információkkal, ha vannak ilyenek. | true
|AddAutoCollectedMetricExtractor | Az AutoCollectedMetrics Extractor engedélyezése/letiltása, amely egy olyan TelemetryProcessor, amely előre összevont metrikákat küld a kérelmek/függőségek számára a mintavétel megkezdése előtt. | true
|RequestCollectionOptions.TrackExceptions | Engedélyezheti vagy letilthatja a nem kezelt kivételek nyomon követését a kérelmek gyűjtési modulja által. | hamis a NETSTANDARD 2.0-ban (mivel a kivételeket a ApplicationInsightsLoggerProvider követte nyomon), ellenkező esetben igaz.

A legnaprakészebb listához tekintse [meg `ApplicationInsightsServiceOptions` a konfigurálható beállításokat](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs) .

### <a name="sampling"></a>Mintavételezés

A ASP.NET Core Application Insights SDK a rögzített sebességű és az adaptív mintavételezést is támogatja. Az adaptív mintavételezés alapértelmezés szerint engedélyezve van. 

További információ: [adaptív mintavételezés konfigurálása ASP.net Core alkalmazásokhoz](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers hozzáadása

Ha az összes telemetria ellátott globális tulajdonságokat szeretné definiálni, használja a [telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) -inicializálást.

Vegyen fel `TelemetryInitializer` minden újat `DependencyInjection` a tárolóba, ahogy az a következő kódban is látható. Az SDK automatikusan felveszi `TelemetryInitializer` a `DependencyInjection` tárolóba felvett bármelyiket.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers eltávolítása

A telemetria inicializálók alapértelmezés szerint jelennek meg. Az összes vagy adott telemetria-inicializáló eltávolításához használja a következő mintát a hívása `AddApplicationInsightsTelemetry()`után.

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

Egyéni telemetria- `TelemetryConfiguration` processzorokat a bővítmény `IServiceCollection`metódusának `AddApplicationInsightsTelemetryProcessor` használatával adhat hozzá. A telemetria processzorok [speciális szűrési forgatókönyvekben](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) való használata lehetővé teszi, hogy a Application Insights a szolgáltatásnak küldött, a telemetria által befoglalt vagy kizárni kívánt funkciók jobban átirányíthatók legyenek. Használja az alábbi példát.

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

* `RequestTrackingTelemetryModule`– RequestTelemetry gyűjt a bejövő webes kérelmekből.
* `DependencyTrackingTelemetryModule`– A kimenő HTTP-hívások és az SQL-hívások DependencyTelemetry gyűjti.
* `PerformanceCollectorModule`– A Windows PerformanceCounters gyűjti.
* `QuickPulseTelemetryModule`– Az élő metrika portálon megjelenített telemetria gyűjti.
* `AppServicesHeartbeatTelemetryModule`-Gyűjti a szívveréseket (amelyek egyéni mérőszámként lesznek elküldve), Azure App Service környezettel kapcsolatban, ahol az alkalmazás fut.
* `AzureInstanceMetadataTelemetryModule`-Gyűjti a szívveréseket (amelyek egyéni mérőszámként lesznek elküldve), az Azure-beli virtuálisgép-környezetről, ahol az alkalmazás fut.
* `EventCounterCollectionModule`– Gyűjti a [EventCounters.](#eventcounter). Ez a modul egy új szolgáltatás, amely az SDK 2.8.0-beta3 és újabb verziójában érhető el.

Az alapértelmezett beállítások `TelemetryModule`konfigurálásához használja a bővítmény `IServiceCollection`metódusát `ConfigureTelemetryModule<T>` a következő példában látható módon:.

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

Az alapértelmezett csatorna `ServerTelemetryChannel`:. A következő példában látható módon felülbírálhatja azt.

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

Ha a telemetria feltételesen és dinamikusan szeretné letiltani, akkor `TelemetryConfiguration` előfordulhat, hogy a kódban bárhol feloldja a példányt ASP.net Core függőségi `DisableTelemetry` injektálási tárolóval, és beállítja a jelölőt.

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

Vegye figyelembe, hogy a fentiek nem akadályozzák meg, hogy az automatikus gyűjtési modulok telemetria gyűjtsenek. A fenti megközelítéssel csak a telemetria küldése Application Insights lesz letiltva. Ha egy adott automatikus gyűjtési modul nem kívánatos, a legjobb, ha [eltávolítja a telemetria modult](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Hogyan követhetem nyomon az automatikusan összegyűjtött telemetria?

Szerezzen be egy `TelemetryClient` példányt a konstruktor injekció használatával, és hívja meg a `TrackXXX()` szükséges metódust. Nem javasoljuk, hogy új `TelemetryClient` példányokat hozzon létre egy ASP.net Core alkalmazásban. Az egy példánya `TelemetryClient` már regisztrálva van a `DependencyInjection` tárolóban, amely `TelemetryConfiguration` a többi telemetria együtt osztozik. Az új `TelemetryClient` példányok létrehozása csak akkor javasolt, ha olyan konfigurációra van szükség, amely eltér a többi telemetria. 

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

Igen, az ezzel a módszerrel való Application Insights engedélyezése érvényes. Ez a technika a Visual Studio bevezetésében és a Web Apps-bővítményekben használatos. Azt javasoljuk azonban, hogy `services.AddApplicationInsightsTelemetry()` használja a használatát, mert túlterheléseket biztosít a konfiguráció szabályozásához. Mindkét módszer belsőleg ugyanazt a dolgot végzi el, így ha nem kell egyéni konfigurációt alkalmaznia, bármelyik metódust meghívhatja.

`IWebHostBuilder`ASP.net Core 3,0- `IHostBuilder` as verzióban van lecserélve, és a félreértések elkerülése érdekében Application Insights a 2.8.0-beta3 a UseApplicationInsights () metódust elavultként jelöli meg, és a következő főverzióban el lesz távolítva.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>ASP.NET Core alkalmazást telepítem Web Apps. Továbbra is engedélyezni kell a Application Insights bővítményt a Web Appsból?

Ha az SDK-t a jelen cikkben látható módon telepíti a Build időpontban, nem kell engedélyeznie a [Application Insights bővítményt](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) a app Service portálról. Ha a bővítmény telepítve van, akkor a rendszer akkor is vissza fog térni, ha észleli, hogy az SDK már hozzá van adva az alkalmazáshoz. Ha engedélyezi a Application Insights a bővítményből, nem kell telepítenie és frissítenie az SDK-t. Ha azonban a jelen cikkben szereplő utasítások alapján engedélyez Application Insights, nagyobb rugalmasságot biztosít, mivel:

   * Application Insights telemetria továbbra is a következővel fog működni:
       * Minden operációs rendszer, beleértve a Windows, a Linux és a Mac rendszereket is.
       * Az összes közzétételi mód, beleértve az önálló vagy a keretrendszertől függőket is.
       * Minden cél keretrendszer, beleértve a teljes .NET-keretrendszert is.
       * Minden üzemeltetési lehetőség, beleértve a Web Apps, a virtuális gépeket, a Linuxot, a tárolókat, az Azure Kubernetes szolgáltatást és a nem Azure-beli üzemeltetést.
       * Az összes .NET Core verzió, beleértve az előzetes verziókat is.
   * A Visual studióból történő hibakeresés során a telemetria helyileg is megtekintheti.
   * Az API használatával nyomon követheti a `TrackXXX()` további egyéni telemetria.
   * Teljes hozzáférése van a konfigurációhoz.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Engedélyezhető Application Insights-figyelés az olyan eszközökkel, mint a Állapotmonitor?

Nem. A [Állapotmonitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) és a [Állapotmonitor v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) jelenleg csak a 4. x ASP.net támogatja.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Application Insights automatikusan engedélyezve van a saját ASP.NET Core 2,0 alkalmazáshoz?

Az `Microsoft.AspNetCore.All` 2,0-es metacsomag tartalmazza a Application Insights SDK-t (2.1.0-verzió). Ha az alkalmazást a Visual Studio Debugger alatt futtatja, a Visual Studio lehetővé teszi, hogy a Application Insights és a telemetria helyileg jelenítse meg az IDE-ben. A telemetria nem lett elküldve a Application Insights szolgáltatásnak, kivéve, ha meg van adva a kialakítási kulcs. Javasoljuk, hogy a cikk utasításait követve engedélyezze a Application Insights, még az 2,0-alkalmazások esetében is.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Ha Linuxon futtatom az alkalmazást, az összes funkció támogatott?

Igen. Az SDK funkcióinak támogatása minden platformon azonos, a következő kivételekkel:

* A teljesítményszámlálók csak a Windows rendszerben támogatottak.
* Annak ellenére `ServerTelemetryChannel` , hogy alapértelmezés szerint engedélyezve van, ha az alkalmazás Linux vagy MacOS rendszeren fut, a csatorna nem hoz létre automatikusan helyi tárolási mappát, hogy a telemetria átmenetileg megmaradjanak, ha hálózati problémák vannak. Ennek a korlátozásnak a miatt a telemetria elvész, ha ideiglenes hálózati vagy kiszolgálói problémák merülnek fel. A probléma megkerüléséhez konfigurálja a csatorna helyi mappáját:

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

[Olvassa el és járuljon hozzá a kódhoz](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Videó

- Tekintse meg ezt a külső lépésenkénti videót, amellyel a [Application Insights a .net Core és a Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) használatával konfigurálhatja a semmiből.
- Tekintse meg ezt a külső lépésenkénti videót a [.net Core és a Visual Studio Code nélküli Application Insights konfigurálásához](https://youtu.be/ygGt84GDync) .

## <a name="next-steps"></a>További lépések

* [Fedezze fel a felhasználói folyamatokat](../../azure-monitor/app/usage-flows.md) , hogy megtudja, hogyan navigálnak a felhasználók az alkalmazáson keresztül.
* [Egy pillanatkép-gyűjtemény konfigurálásával](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) megtekintheti a forráskód és a változók állapotát a kivétel pillanatában.
* [Az API használatával](../../azure-monitor/app/api-custom-events-metrics.md) saját eseményeket és mérőszámokat küldhet az alkalmazás teljesítményének és használatának részletes áttekintéséhez.
* A [rendelkezésre állási tesztek](../../azure-monitor/app/monitor-web-app-availability.md) segítségével folyamatosan, a világ minden pontján ellenőrizhető az alkalmazás.
* [Függőség injekció ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
