---
title: Azure Application Insights ASP.NET alapalkalmazásokhoz | Microsoft dokumentumok
description: Monitor ASP.NET Core webes alkalmazások a rendelkezésre állás, a teljesítmény és a használat.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: d6a0e507022452f1491e71651ba3bc8db3d1c090
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284789"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights ASP.NET alapalkalmazásokhoz

Ez a cikk ismerteti, hogyan engedélyezheti az Application Insights egy [ASP.NET Core](https://docs.microsoft.com/aspnet/core) alkalmazás. Ebben a cikkben az utasítások befejezésekor az Application Insights összegyűjti a kérelmeket, függőségeket, kivételeket, teljesítményszámlálókat, szívveréseket és naplókat a ASP.NET Core alkalmazásból.

A példa fogjuk használni itt egy [MVC alkalmazás,](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) amely a célok `netcoreapp2.2`. Ezeket az utasításokat az összes ASP.NET Core alkalmazásra alkalmazhatja.

## <a name="supported-scenarios"></a>Támogatott esetek

Az [Application Insights SDK ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) monitorozása az alkalmazások nem számít, hol és hogyan futnak. Ha az alkalmazás fut, és hálózati kapcsolat tal rendelkezik az Azure-hoz, telemetriai adatokgyűjthetők. Az Application Insights figyelése mindenhol támogatott .NET Core támogatott. A támogatás a következőkre terjed ki:
* **Operációs rendszer**: Windows, Linux vagy Mac.
* **Hosting módszer**: Folyamatban vagy folyamaton kívüli.
* **Telepítési módszer**: Keretrendszerfüggő vagy önálló.
* **Webkiszolgáló**: IIS (Internet Information Server) vagy Kestrel.
* **Üzemeltetési platform:** Az Azure App Service, az Azure VM, a Docker, az Azure Kubernetes Service (AKS) webalkalmazások szolgáltatása és így tovább.
* **.NET Core Runtime verzió:** 1.XX, 2.XX vagy 3.XX
* **IDE**: Visual Studio, VS Code vagy parancssor.

> [!NOTE]
> Ha ASP.NET Core 3.X-et és az Application Insightsot használja, használja a [2.8.0-s](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) vagy újabb verziót. Ez az egyetlen verzió, amely támogatja ASP.NET Core 3.X.

## <a name="prerequisites"></a>Előfeltételek

- Egy működő ASP.NET Core alkalmazás. Ha létre kell hoznia egy ASP.NET Core alkalmazást, kövesse ezt [a ASP.NET Core oktatóanyagot.](https://docs.microsoft.com/aspnet/core/getting-started/)
- Egy érvényes Application Insights instrumentation kulcs. Ez a kulcs szükséges az Application Insights bármely telemetriai adatok küldéséhez. Ha egy instrumentation kulcs lekért új Application Insights-erőforrást kell létrehoznia, olvassa [el az Application Insights-erőforrás létrehozása című témakört.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Az Application Insights kiszolgálóoldali telemetriai adatainak engedélyezése (Visual Studio)

1. Nyissa meg a projektet a Visual Studióban.

    > [!TIP]
    > Ha szeretné, beállíthatja a projekt forrásvezérlőt, hogy nyomon követhesse az Application Insights összes módosítását. A forrásvezérlés engedélyezéséhez jelölje > **be a Fájlhozzáadás a forrásvezérlőhöz jelölőnégyzetet.** **File**

2. Válassza **a Project** > **Add Application Insights telemetriát.**

3. Válassza **az Első lépések lehetőséget.** A kijelölés szövege a Visual Studio verziójától függően eltérő lehet. Néhány korábbi verzió helyett egy **Ingyenes start** gombot használ.

4. Válassza ki előfizetését. Ezután válassza **az Erőforrás-nyilvántartás** > **Register**lehetőséget.

5. Miután hozzáadta az Application Insightsot a projekthez, ellenőrizze, hogy az SDK legújabb stabil kiadását használja-e. Nyissa meg a **Project** > **Manage NuGet Packages** > **Microsoft.ApplicationInsights.AspNetCore**című lapot. Ha szükséges, válassza a **Frissítés**lehetőséget.

     ![Képernyőkép afrissítéshez készült Application Insights-csomag kiválasztásának helyével](./media/asp-net-core/update-nuget-package.png)

6. Ha követte a választható tippet, és hozzáadta a projektet a forrásvezérlőhöz, nyissa meg a**Csapatkezelő** > módosításai **megtekintése** > című**webhelyet.** Ezután jelölje ki az egyes fájlokat az Application Insights telemetriai adatok által végrehajtott módosítások diff view megtekintéséhez.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Az Application Insights kiszolgálóoldali telemetriai adatainak engedélyezése (nincs Visual Studio)

1. Telepítse az [Application Insights SDK NuGet csomagot ASP.NET Core számára.](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) Javasoljuk, hogy mindig a legújabb stabil verziót használja. Keresse meg az SDK teljes kiadási jegyzeteit a [nyílt forráskódú GitHub-tárban.](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)

    A következő kódminta a projekt `.csproj` fájljába hozzáadandó módosításokat mutatja be.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
        </ItemGroup>
    ```

2. Add `services.AddApplicationInsightsTelemetry();` hozzá `ConfigureServices()` a `Startup` módszer az osztályban, mint ebben a példában:

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

3. Állítsa be a műszerkulcsot.

    Bár a műszerezési kulcsot argumentumként `AddApplicationInsightsTelemetry`megadhatja, javasoljuk, hogy adja meg a instrumentation kulcsot a konfigurációban. A következő kódminta bemutatja, hogyan `appsettings.json`adható meg egy műszerezési kulcs a ban. Győződjön `appsettings.json` meg arról, hogy a közzététel során az alkalmazás gyökérmappájába másolva van.

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

    Másik lehetőségként adja meg a műszerezési kulcsot a következő környezeti változók egyikében:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Példa:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    Általában `APPINSIGHTS_INSTRUMENTATIONKEY` az Azure Web Apps-alkalmazásokban telepített alkalmazások instrumentation kulcsát adja meg.

    > [!NOTE]
    > A kódban megadott instrumentation kulcs megnyeri `APPINSIGHTS_INSTRUMENTATIONKEY`a környezeti változót, amely más beállításokkal szemben nyer.

## <a name="run-your-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást, és kéréseket küldjön hozzá. Telemetriai kell most az Application Insights. Az Application Insights SDK automatikusan gyűjti a bejövő webes kérelmeket az alkalmazás, valamint a következő telemetriai adatokat is.

### <a name="live-metrics"></a>Élő metrikák

[Az élő metrikák](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) segítségével gyorsan ellenőrizheti, hogy az Application Insights figyelése megfelelően van-e konfigurálva. Bár eltarthat néhány percig, mielőtt a telemetriai adatok megjelennek a portálon és az elemzésben, a Live Metrics közel valós időben mutatja a futó folyamat PROCESSZOR-használatát. Más telemetriai adatokat is megjeleníthet, például kéréseket, függőségeket, nyomkövetéseket stb.

### <a name="ilogger-logs"></a>ILogger naplók

A súlyos vagy `ILogger` nagyobb `Warning` súlyosságú vagy annál nagyobb értéken kibocsátott naplókat a rendszer automatikusan rögzíti. Kövesse [ILogger docs](ilogger.md#control-logging-level) testre szabhatja, hogy mely naplószintek által rögzített Application Insights.

### <a name="dependencies"></a>Függőségek

A függőségi gyűjtemény alapértelmezés szerint engedélyezve van. [Ez](asp-net-dependencies.md#automatically-tracked-dependencies) a cikk ismerteti az automatikusan gyűjtött függőségeket, és a manuális nyomon követés végrehajtásának lépéseit is tartalmazza.

### <a name="performance-counters"></a>Teljesítményszámlálók

A ASP.NET Core [teljesítményszámlálóinak](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) támogatása korlátozott:

* Az SDK 2.4.1-es és újabb verziói teljesítményszámlálókat gyűjtenek, ha az alkalmazás az Azure Web Apps (Windows) alkalmazásban fut.
* Az SDK 2.7.1-es és újabb verziói teljesítményszámlálókat gyűjtenek, ha az alkalmazás Windows rendszerben fut, és a célokat vagy újabb verziókat célozza `NETSTANDARD2.0` meg.
* A .
* Az SDK 2.8.0-s és újabb verziói támogatják a cpu/memória számlálót Linux alatt. A Linux nem támogat más számlálót. A rendszerszámlálók Linux (és más nem Windows-környezetekben) beszereznie ajánlott módja az [EventCounters](#eventcounter) használata

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule`alapértelmezés szerint engedélyezve van, és a .NET Core 3.X alkalmazások alapértelmezett számlálókészletét gyűjti össze. Az [EventCounter](eventcounters.md) oktatóanyag felsorolja az összegyűjtött számlálók alapértelmezett készletét. Azt is utasításokat testre a listát.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Ügyféloldali telemetria engedélyezése webalkalmazásokhoz

Az előző lépések elegendőek a kiszolgálóoldali telemetriai adatok gyűjtésének megkezdéséhez. Ha az alkalmazás ügyféloldali összetevőkkel rendelkezik, a következő lépésekkel kezdje el gyűjteni a [használati telemetriai adatokat.](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)

1. A `_ViewImports.cshtml`alkalmazásban adjunk hozzá injekciót:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. A `_Layout.cshtml`be `HtmlHelper` szúrja be `<head>` a szakasz végén, de bármely más parancsfájl előtt. Ha egyéni JavaScript-telemetriai adatokat szeretne jelenteni az oldalról, adja be a kódrészlet után:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```
    
Másik lehetőségként `FullScript` használja `ScriptBody` a elérhető sdk v2.14. Ezt akkor használja, ha `<script>` a tartalombiztonsági házirend beállításához szabályoznia kell a címkét:

    ```cshtml
        <script> // apply custom changes to this script tag.
            @Html.Raw(JavaScriptSnippet.ScriptBody)
        </script>
    ```

A `.cshtml` korábban hivatkozott fájlnevek egy alapértelmezett MVC alkalmazássablonból származnak. Végső soron, ha azt szeretné, hogy megfelelően engedélyezze az ügyféloldali figyelés az alkalmazás, a JavaScript-kódrészlet meg kell jelennie a szakaszminden egyes oldalon az `<head>` alkalmazás, amely figyelni kívánt. Ezt a célt az alkalmazássablonhoz úgy érheti el, hogy hozzáadja a JavaScript-kódrészletet a alkalmazáshoz. `_Layout.cshtml` 

Ha a projekt nem `_Layout.cshtml`tartalmazza, továbbra is hozzáadhat [ügyféloldali figyelést.](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring) Ezt úgy teheti meg, hogy a JavaScript-kódrészletet `<head>` egy egyenértékű fájlhoz adja hozzá, amely az alkalmazás összes lapját vezérli. Vagy hozzáadhatja a kódrészletet több oldalhoz, de ezt a megoldást nehéz karbantartani, és általában nem javasoljuk.

## <a name="configure-the-application-insights-sdk"></a>Az Application Insights SDK konfigurálása

Az application insights SDK ASP.NET Core az alapértelmezett konfiguráció módosításához testreszabhatja. Az Application Insights ASP.NET SDK felhasználói ismerhetik `ApplicationInsights.config` a konfiguráció `TelemetryConfiguration.Active`módosítását a használatával vagy módosításával. A ASP.NET Core konfigurációját eltérően módosítja. Adja hozzá a ASP.NET Core SDK-t az alkalmazáshoz, és konfigurálja ASP.NET Core beépített [függőségi injektálással.](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) Szinte az összes konfigurációs változtatást az `ConfigureServices()` `Startup.cs` osztályod metódusában, kivéve, ha másképp van utasítva. A következő szakaszok további információkat tartalmaznak.

> [!NOTE]
> A ASP.NET Core alkalmazásokban a `TelemetryConfiguration.Active` konfiguráció módosítása nem támogatott.

### <a name="using-applicationinsightsserviceoptions"></a>Az ApplicationInsightsServiceOptions használata

Néhány gyakori beállítást módosíthat, `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetry`ha átadja a t, ahogy ebben a példában is:

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

A beállítások teljes listája a`ApplicationInsightsServiceOptions`

|Beállítás | Leírás | Alapértelmezett
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | Engedélyezés/letiltás`PerformanceCounterCollectionModule` | igaz
|EnableRequestTrackingTelemetryModule   | Engedélyezés/letiltás`RequestTrackingTelemetryModule` | igaz
|EnableEventCounterCollectionModule   | Engedélyezés/letiltás`EventCounterCollectionModule` | igaz
|EnableDependencyTrackingTelemetryModule   | Engedélyezés/letiltás`DependencyTrackingTelemetryModule` | igaz
|EnableAppServicesHeartbeatTelemetryModule  |  Engedélyezés/letiltás`AppServicesHeartbeatTelemetryModule` | igaz
|EnableAzureInstanceMetadataTelemetryModule   |  Engedélyezés/letiltás`AzureInstanceMetadataTelemetryModule` | igaz
|EnableQuickPulseMetricStream | LiveMetrics szolgáltatás engedélyezése/letiltása | igaz
|EnableAdaptiveSampling | Adaptív mintavételezés engedélyezése/letiltása | igaz
|Szívverés engedélyezése | Engedélyezés/letiltás a szívverések szolgáltatás, amely rendszeres időközönként (15 perces alapértelmezett) küld egy egyéni metrika nevű "HeartBeatState" információkkal a futásidejű, mint a .NET-verzió, az Azure Environment információk, ha van ilyen, stb. | igaz
|AddAutoCollectedMetric Extractor | AutoCollectedMetrics extractor engedélyezése/letiltása, amely egy TelemetryProcessor, amely előre összesített metrikákat küld a kérelmekről/függőségekről a mintavételezés előtt. | igaz
|RequestCollectionOptions.TrackKivételek | A nem kezelt kivétel-követés jelentésének engedélyezése/letiltása a Kérelemgyűjtés modul által. | false a NETSTANDARD2.0-ban (mert a kivételek nyomon követése az ApplicationInsightsLoggerProvider-nél történik), egyébként igaz.

Tekintse meg a [konfigurálható beállításokat `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) a legfrissebb listát.

### <a name="sampling"></a>Mintavételezés

Az Application Insights SDK ASP.NET Core támogatja a fix sebességű és adaptív mintavételezést. Az adaptív mintavételezés alapértelmezés szerint engedélyezve van. 

További információ: [Adaptív mintavételezés konfigurálása ASP.NET Core alkalmazásokhoz](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)című témakörben talál.

### <a name="adding-telemetryinitializers"></a>Telemetriai initializerek hozzáadása

[Telemetriai inicializálók](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) használata, ha meg szeretné határozni az összes telemetriai adatokkal küldött globális tulajdonságokat.

Adjon hozzá `TelemetryInitializer` újat a `DependencyInjection` tárolóhoz az alábbi kódban látható módon. Az SDK automatikusan `TelemetryInitializer` felveszi a `DependencyInjection` tárolóhoz hozzáadott adatokat.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>TelemetriaI initializerek eltávolítása

Telemetriai inicializálók alapértelmezés szerint jelen vannak. Az összes vagy adott telemetriai inicializáló eltávolításához használja a következő mintakódot a hívás *után.* `AddApplicationInsightsTelemetry()`

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

### <a name="adding-telemetry-processors"></a>Telemetriai processzorok hozzáadása

Egyéni telemetriai processzorokat `TelemetryConfiguration` adhat hozzá a `AddApplicationInsightsTelemetryProcessor` `IServiceCollection`bővítménymetódus használatával. Telemetriai processzorokat speciális [szűrési forgatókönyvekben](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer)használ. Használja a következő példát.

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

### <a name="configuring-or-removing-default-telemetrymodules"></a>Alapértelmezett Telemetriai modulok konfigurálása vagy eltávolítása

Az Application Insights telemetriai modulok segítségével automatikusan összegyűjti az adott számítási feladatok hasznos telemetriai adatait anélkül, hogy a felhasználó manuális nyomon követést igényelne.

A következő automatikus begyűjtési modulok alapértelmezés szerint engedélyezve vannak. Ezek a modulok felelősek a telemetriai adatok automatikus gyűjtéséért. Letilthatja vagy beállíthatja őket alapértelmezett viselkedésük módosításához.

* `RequestTrackingTelemetryModule`- Gyűjti RequestTelemetry a bejövő webes kérelmek.
* `DependencyTrackingTelemetryModule`- Összegyűjti a függőségtelemetriaa a kimenő http-hívások és sql hívások.
* `PerformanceCollectorModule`- Windows PerformanceCounters gyűjti.
* `QuickPulseTelemetryModule`- Gyűjti a telemetriai adatok megjelenítéséhez az élő metrikák portálon.
* `AppServicesHeartbeatTelemetryModule`- Összegyűjti a szívverés (amely egyéni metrikákként küldi el), az Azure App Service-környezet, ahol az alkalmazás üzemelteti.
* `AzureInstanceMetadataTelemetryModule`- Összegyűjti a szívverések (amelyek egyéni metrikákként küldött), az Azure virtuális gép környezet, ahol az alkalmazás üzemelteti.
* `EventCounterCollectionModule`- Összegyűjti [EventCounters.](eventcounters.md) Ez a modul egy új funkció, és elérhető Az SDK 2.8.0-s vagy újabb verziója.

Az alapértelmezett `TelemetryModule`beállításokhoz használja `ConfigureTelemetryModule<T>` a `IServiceCollection`bővítménymetódust a alkalmazásban, ahogy az a következő példában látható.

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

A 2.12.2-es [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) verziótól kezdve egyszerű opciót tartalmaz az alapértelmezett modulok letiltására.

### <a name="configuring-a-telemetry-channel"></a>Telemetriai csatorna konfigurálása

Az alapértelmezett `ServerTelemetryChannel`csatorna a . Felülírhatja, ahogy a következő példa mutatja.

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

### <a name="disable-telemetry-dynamically"></a>Telemetriai adatok dinamikus letiltása

Ha azt szeretné, hogy a telemetriai adatok `TelemetryConfiguration` feltételesen és dinamikusan letiltása, `DisableTelemetry` feloldhatja példány ASP.NET Core függőségi injektálási tároló bárhol a kódban, és állítsa be a jelzőt rajta.

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

A fenti nem akadályozza meg az automatikus gyűjtési modulok telemetriai adatok gyűjtését. Csak a telemetriai adatok küldése az Application Insights letiltásra kerül a fenti megközelítéssel. Ha egy adott automatikus gyűjtési modul nem kívánatos, a legjobb, ha [eltávolítja a telemetriai modult](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="does-application-insights-support-aspnet-core-3x"></a>Támogatja az Application Insights ASP.NET Core 3.X-et?

Igen. Frissítsen az [Application Insights SDK-ra ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.8.0-s vagy újabb verziójához. Az SDK régebbi verziói nem támogatják ASP.NET Core 3.X-et.

Ha [innen](#enable-application-insights-server-side-telemetry-visual-studio)használja a Visual Studio-alapú utasításokat, frissítsen a Visual Studio 2019 legújabb verziójára (16.3.0) a fedélzetre. A Visual Studio korábbi verziói nem támogatják az automatikus bevezetést ASP.NET Core 3.X alkalmazásokhoz.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Hogyan követhetem nyomon a nem automatikusan gyűjtött telemetriai adatokat?

Kap egy `TelemetryClient` példány segítségével konstruktor `TrackXXX()` injekció, és hívja a szükséges módszert rajta. Nem javasoljuk, hogy `TelemetryClient` hozzon létre új példányokat egy ASP.NET Core alkalmazásban. Egy singleton `TelemetryClient` példány már regisztrálva van `DependencyInjection` `TelemetryConfiguration` a tárolóban, amely megosztja a többi telemetriai adatok. Új `TelemetryClient` példány létrehozása csak akkor ajánlott, ha szüksége van egy konfiguráció, amely elkülönül a többi telemetriai adatok.

A következő példa bemutatja, hogyan követheti nyomon a vezérlő további telemetriai adatokat.

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

Az Application Insights ban az egyéni adatjelentésekkel kapcsolatos további információkért lásd: [Application Insights egyéni metrikák API-hivatkozás.](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/)

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Egyes Visual Studio-sablonok az IWebHostBuilder UseApplicationInsights() bővítménymetódusát használták az Application Insights engedélyezéséhez. Ez a használat még mindig érvényes?

Bár a `UseApplicationInsights()` bővítmény metódus továbbra is támogatott, elavultnak van jelölve az Application Insights SDK 2.8.0-s verziójában. Ez az SDK következő fő verziójában lesz eltávolítva. Az Application Insights telemetriai adatok `AddApplicationInsightsTelemetry()` engedélyezésének ajánlott módja a használat, mert túlterhelést biztosít bizonyos konfigurációk vezérléséhez. Emellett ASP.NET Core 3.X-alkalmazásokban `services.AddApplicationInsightsTelemetry()` is csak úgy engedélyezheti az alkalmazáselemzéseket.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>A ASP.NET Core alkalmazásomat telepítem a webalkalmazásokba. Továbbra is engedélyezzem az Application Insights-bővítményt a Web Apps ből?

Ha az SDK van telepítve a jelen cikkben látható buildelési időpontban, nem kell engedélyeznie az [Application Insights bővítményt](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) az App Service-portálról. Még ha a bővítmény telepítve is van, akkor is leáll, ha azt észleli, hogy az SDK már hozzá van adva az alkalmazáshoz. Ha engedélyezi az Application Insights a bővítményből, nem kell telepítenie és frissítenie az SDK-t. Ha azonban a cikkben található utasításokat követve engedélyezi az Application Insights ot, nagyobb rugalmasságot biztosít, mivel:

   * Az Application Insights telemetriai adatai továbbra is működni fognak:
       * Minden operációs rendszer, beleértve a Windows, Linux és Mac rendszereket.
       * Minden közzétételi mód, beleértve az önálló vagy keretrendszerfüggő t.
       * Minden célkeretrendszer, beleértve a teljes .
       * Minden üzemeltetési lehetőség, beleértve a webalkalmazásokat, a virtuális gépeket, a Linuxot, a tárolókat, az Azure Kubernetes szolgáltatást és a nem Azure-üzemeltetést.
       * Az összes .NET Core verzió, beleértve az előzetes verziókat is.
   * A telemetriai adatokat helyileg láthatja, amikor a Visual Studio-ból hibakeresést alkalmaz.
   * További egyéni telemetriai adatokat `TrackXXX()` az API használatával nyomon követheti.
   * Teljes mértékben ön szabályozhatja a konfigurációt.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Engedélyezhetem az Application Insights figyelését olyan eszközökkel, mint az Állapotfigyelő?

Nem. [Az Állapotfigyelő](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) és [az Állapotfigyelő 2-es v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) jelenleg csak ASP.NET 4.x-es támogatást nyújt.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Az Application Insights automatikusan engedélyezve van a ASP.NET Core 2.0-s alkalmazáshoz?

A `Microsoft.AspNetCore.All` 2.0-s metacsomag tartalmazza az Application Insights SDK-t (2.1.0-s verzió). Ha az alkalmazást a Visual Studio hibakereső alatt futtatja, a Visual Studio engedélyezi az Application Insights ot, és helyileg jeleníti meg a telemetriai adatokat magában az IDE-ben. Telemetriai adatok nem lett elküldve az Application Insights szolgáltatás, kivéve, ha egy instrumentation kulcs meg van adva. Javasoljuk, hogy kövesse az ebben a cikkben található utasításokat az Application Insights engedélyezéséhez, még 2.0-s alkalmazások esetén is.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Ha linuxos anamilyen-t futtatok, minden funkció támogatott?

Igen. Az SDK funkciótámogatása minden platformon azonos, a következő kivételekkel:

* Az SDK [eseményekszámlálókat](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters) gyűjt Linuxon, mert [a teljesítményszámlálókcsak](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters) a Windows ban támogatottak. A legtöbb mutató ugyanaz.
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
        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>Ez az SDK támogatja az új .NET Core 3.X worker service sablonalkalmazásokat?

Ez az SDK megköveteli `HttpContext`, és így nem működik a nem HTTP-alkalmazások, beleértve a .NET Core 3.X Worker Service alkalmazások. Tekintse meg [ezt](worker-service.md) a dokumentumot az ilyen alkalmazások ban az alkalmazások elemzési engedélyezéséhez az újonnan kiadott Microsoft.ApplicationInsights.WorkerService SDK használatával.

## <a name="open-source-sdk"></a>Nyílt forráskódú SDK

[Olvassa el, és hozzájárul a kódot](https://github.com/microsoft/ApplicationInsights-dotnet#recent-updates).

## <a name="video"></a>Videó

- Tekintse meg ezt a külső, lépésről lépésre készült videót, [amelyen az Application Insightsat a .NET Core és](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) a Visual Studio segítségével konfigurálhatja.
- Tekintse meg ezt a külső, lépésről lépésre készült videót, [amelyen az Application Insights .NET Core és Visual Studio code -val konfigurálható.](https://youtu.be/ygGt84GDync)

## <a name="next-steps"></a>További lépések

* Fedezze fel a [felhasználói folyamatokat](../../azure-monitor/app/usage-flows.md) annak megértéséhez, hogy a felhasználók hogyan navigálnak az alkalmazásban.
* [Állítson be egy pillanatkép-gyűjteményt](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) a forráskód és a változók állapotának megtekintéséhez a kivétel pillanatában.
* [Az API-val](../../azure-monitor/app/api-custom-events-metrics.md) elküldheti saját eseményeit és mutatóit az alkalmazás teljesítményének és használatának részletes megtekintéséhez.
* A [rendelkezésre állási tesztek segítségével](../../azure-monitor/app/monitor-web-app-availability.md) folyamatosan ellenőrizheti alkalmazását a világ minden tájáról.
* [Függőség injekció ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
