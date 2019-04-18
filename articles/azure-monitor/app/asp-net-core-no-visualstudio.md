---
title: Az Azure Application Insights az ASP.NET Core Visual Studio nélkül |} A Microsoft Docs
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
ms.date: 04/03/2019
ms.author: cithomas
ms.openlocfilehash: 8243523887ec9861459b2d196126237cf89bad97
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59288366"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights az ASP.NET Core-alkalmazások

Ez a cikk ismerteti a lépéseket, ha engedélyezi az Application Insights egy [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2) alkalmazás nélkül a Visual Studio IDE használatával. Ha van telepítve, majd Visual Studio IDE [ez](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) dokumentum tartalmaz részletes útmutatást a Visual Studio. A jelen cikkben lévő utasítások végrehajtásával az Application Insights indul el kéréseket, függőségek, kivételek, teljesítményszámlálók, szívverések és naplók összegyűjtése az ASP.NET Core-alkalmazást. A használt mintaalkalmazás egy [MVC alkalmazás](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/?view=aspnetcore-2.2) célzó `netcoreapp2.2`, de az itt leírt utasításokat vonatkoznak minden olyan ASP.NET Core alkalmazáshoz. Kivételek hívjuk, amikor a alkalmazni.

## <a name="supported-scenarios"></a>Támogatott esetek

Application Insights SDK (Software Development Kitet) az ASP.NET Core figyelheti az alkalmazások, függetlenül attól, hol és hogyan az alkalmazás futtatásakor. Ha az alkalmazás fut, és az Application Insights szolgáltatás hálózati kapcsolattal rendelkezik, a telemetriai adatok várhatóan gyűjtendő. Ez a támogatás magában foglalja, de már nem korlátozódik bármely operációs rendszert (Windows, Linux, Mac), a üzemeltetési módot (a folyamat a vs-folyamaton), az üzembe helyezési módszer (keretrendszer-függő vagy önálló), webkiszolgáló (IIS, a Kestrel), platform-(Azure-webalkalmazások, Azure virtuális Gépre, Docker, az AKS és így tovább.) vagy az IDE (Visual Studio, a VS Code-parancssor.)

## <a name="prerequisites"></a>Előfeltételek

- Egy működő ASP.NET Core-alkalmazást. Hajtsa végre a [ez](https://docs.microsoft.com/aspnet/core/getting-started/) útmutató hozhat létre egy ASP.NET Core-alkalmazást, ha szükséges.
- Egy érvényes az Application Insights kialakítási kulcsot, amely azonban szükséges a telemetriát az Application Insights szolgáltatás. Hajtsa végre a [ezek](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) utasításokat követve hozzon létre egy új Application Insights-erőforrást, ha szükséges, és a egy kialakítási kulcs beszerzése.

## <a name="enabling-application-insights-server-side-telemetry"></a>Application Insights kiszolgálóoldali Telemetria engedélyezése

1. Telepítse az Application Insights SDK for ASP.NET Core, amely egy normál NuGet-csomagot. Javasoljuk, hogy mindig a legújabb stabil verzióját használja. A jelen cikkben ismertetett funkciók némelyike nem lehet régebbi verziókban érhető el. Kibocsátási megjegyzések az SDK-ban található [Itt](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases). 

Következő .csproj fájlban a projekthez hozzáadandó változásait jeleníti meg.

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
```

2. Adjon hozzá `services.AddApplicationInsightsTelemetry();` való `ConfigureServices()` metódus az a `Startup` osztály. Az alábbi teljes példa.

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

3. A telepítő a kialakítási kulcsot.

   Bár a kialakítási kulcsot biztosít jako argument Pro `services.AddApplicationInsightsTelemetry("putinstrumentationkeyhere");`, javasoljuk, hogy adja meg a kialakítási kulcsot a konfigurációban. A következő bemutatja, hogyan adjon meg egy kialakítási kulcsot az `appsettings.json`. Győződjön meg arról, hogy `appsettings.json` közzététel során az alkalmazás gyökér mappába másolja.

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

    Alternately, the instrumentation key can also be specified in either of the following environment variables.
    APPINSIGHTS_INSTRUMENTATIONKEY
    ApplicationInsights:InstrumentationKey

    Example:
    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

`APPINSIGHTS_INSTRUMENTATIONKEY` általában segítségével adja meg az üzembe helyezett Azure Web Apps alkalmazások számára.

> [!NOTE]
> Egy környezeti változó keresztül kód wins-ben megadott kialakítási kulcs `APPINSIGHTS_INSTRUMENTATIONKEY`, amely wins-más lehetőségekhez képest.

4. Futtassa az alkalmazást, és hozzá kérelmeket. Telemetria most el kell Application Insights halad. A következő telemetriát az Application Insights SDK automatikusan gyűjti.

    1. Kérelmek - bejövő webes kérelmeket az alkalmazás.
    1. Függőségek
        1. A HTTP/Https-hívások `HttpClient`
        1. Az SQL-hívások `SqlClient`
        1. Az Azure storage-hívások [Azure Storage-kliens](https://www.nuget.org/packages/WindowsAzure.Storage/)*
        1. [Az EventHub ügyfél-SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) 1.1.0-s verzió vagy újabb verzió
        1. [A ServiceBus-ügyfél-SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) 3.0.0-s verziójával vagy újabb verzió

             * Az azure Cosmos DB automatikusan nyomon van, csak a HTTP/HTTPS használata esetén. Az Application Insights nem rögzíti a TCP-módot.


    1. [Teljesítményszámlálók](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)
        1. Teljesítményszámlálók az ASP.NET Core támogatása korlátozódik, a következő
            1. SDK verziója 2.4.1 és teljesítményszámlálók fent gyűjt, ha az alkalmazás fut, az Azure Web App (Windows)
            1. SDK verziója 2.7.0-beta3 és teljesítményszámlálók fent gyűjt, ha az alkalmazás a Windows rendszert futtató, és célcsoport-kezelési `NETSTANDARD2.0` vagy újabb verziója.
            1. Alkalmazások a teljes .NET-keretrendszert teljesítményszámlálók SDK összes verziói támogatottak.

            Ez a dokumentum frissül, amikor teljesítmény számláló támogatás a Linux.
    1. [Élő metrikák](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)
    1. `ILogger` naplók súlyosságú `Warning` vagy újabb rendszer automatikusan rögzített, az SDK-verzió 2.7.0-beta3 vagy újabb. További információ [Itt](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

Eltarthat néhány percig, telemetriai adatok jelennek meg a portál elindításához. Gyorsan ellenőrizheti, ha minden megfelelően működik, akkor érdemes választani, [élő mérőszámok](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream), míg lehetővé kéri, hogy a futó alkalmazás.

## <a name="send-ilogger-logs-to-application-insights"></a>Az Application Insights ILogger naplók küldése

Az Application Insights ILogger küldött rögzítését naplókat támogatja. Olvassa el a teljes dokumentációt [Itt](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

## <a name="enable-client-side-telemetry-for-web-applications"></a>Ügyféloldali Telemetria engedélyezése webes alkalmazásokhoz

A fenti lépések elegendőek a kiszolgálóoldali telemetria gyűjtésének megkezdéséhez. Ha az alkalmazás ügyféloldali összetevőinek, majd kövesse az alábbi lépéseket való gyűjtésének megkezdéséhez [használat telemetriai adatai](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview) onnan.

1. _ViewImports.cshtml vegyen fel injektálási:

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. A _Layout.cshtml, insert HtmlHelper végéhez `<head>` szakaszt, de minden egyéb parancsfájl előtt. Ez a kódrészlet után bármely egyéni JavaScript-telemetriát az oldalról szeretne jelentést kell beszúrásra:

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

Módosítsa megfelelően a tényleges alkalmazás fájlok neveit. A fent használt nevek egy MVC alkalmazássablon származnak.

## <a name="configuring-application-insights-sdk"></a>Az Application Insights SDK konfigurálása

Application Insights SDK for ASP.NET Core testre szabható az alapértelmezett konfiguráció módosítását. Lehet, hogy az Application Insights SDK-t az ASP.NET felhasználói jól ismerik a konfigurációs `ApplicationInsights.config`, vagy módosításával `TelemetryConfiguration.Active`. ASP.NET Core, a konfiguráció másképp történik. Az ASP.NET Core SDK-t használó ASP.NET Core beépített alkalmazás bekerül [DependencyInjection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) mechanizmus és a konfigurálás az ugyanaz lenne is használni DependencyInjection. Szinte minden konfigurációmódosítás történik, az a `ConfigureServices()` módszere a `Startup.cs` osztályhoz, amennyiben másként nincs feltüntetve. Kövesse az alábbiakban további.

> [!NOTE]
> Fontos megjegyezni, hogy módosítása konfigurációja módosításával `TelemetryConfiguration.Active` az ASP.NET Core-alkalmazások nem ajánlott.

### <a name="configuring-using-applicationinsightsserviceoptions"></a>ApplicationInsightsServiceOptions használatával konfigurálása

Néhány gyakori beállításainak módosításához átadásával lehetséges `ApplicationInsightsServiceOptions` való `services.AddApplicationInsightsTelemetry();`. Az alábbiakban egy példa látható.

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

A konfigurálható beállítások listája pontosan `ApplicationInsightsServiceOptions` található [Itt](https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/f0b8631e482d25982eb52092103b34e3ff6e5fef/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Mintavételezés

Application Insights SDK for ASP.NET Core FixedRate és adaptív mintavételezés is támogatja. Az adaptív mintavételezési alapértelmezés szerint engedélyezve van. Hajtsa végre a [ez](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) a dokumentumot, az ASP.NET Core-alkalmazások mintavétel konfigurálása.

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers hozzáadása

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

Egyéni telemetriát processzorok lehet hozzáadni a `TelemetryConfiguration` a bővítmény módszer használatával `AddApplicationInsightsTelemetryProcessor` a `IServiceCollection`. Használja az alábbi példát.

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

A következő automatikus adatgyűjtő modulok alapértelmezés szerint engedélyezve vannak, és automatikusan a telemetriai adatok gyűjtésének felelős. Is ezek le van tiltva, és úgy konfigurálva, hogy az alapértelmezett viselkedés módosítható.

1. `RequestTrackingTelemetryModule`
1. `DependencyTrackingTelemetryModule`
1. `PerformanceCollectorModule`
1. `QuickPulseTelemetryModule`
1. `AppServicesHeartbeatTelemetryModule`
1. `AzureInstanceMetadataTelemetryModule`

Minden olyan alapértelmezett `TelemetryModule`, használja a bővítmény módszert `ConfigureTelemetryModule<T>` a `IServiceCollection` az alábbi példában látható módon.

```csharp
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
    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

*1. Szeretnék nyomon követheti az automatikusan gyűjtött telemetria szereplőkkel néhány további telemetriai adatokat. Hogyan készíthetek azt?*

* Szerezzen be egy példányát `TelemetryClient` a konstruktor injektálási használ, és hívja a szükséges `TrackXXX()` metódust. Nem javasoljuk, hogy hozzon létre új `TelemetryClient` példányok az ASP.NET Core-alkalmazás-példányként `TelemetryClient` már regisztrálva van a DI tároló, amely közös `TelemetryConfiguration` telemetriai adatot rest-tel. Hozzon létre egy új `TelemetryClient` példány ajánlott csak akkor, ha szeretné, hogy a többi a telemetria egy külön konfigurációs van. Az alábbi példa bemutatja, hogyan nyomon lehet követni további telemetria a vezérlő.

```csharp
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

*2. Néhány Visual Studio-sablonok a IWebHostBuilder UseApplicationInsights() metódust segítségével az Application Insights engedélyezése. Még mindig érvényes felhasználási?*

* Ezzel a módszerrel az Application Insights engedélyezése érvényes, és a Visual Studio az előkészítési, illetve a az Azure Web App bővítményeket is használható. Azonban javasoljuk, hogy használjon `services.AddApplicationInsightsTelemery()` néhány konfigurációs szabályozásához túlterheléssel biztosít. Mindkét metódus belsőleg végzi ugyanaz, ha nem tartozik a alkalmazni egyéni konfiguráció, a hívó vagy nem okoz gondot.

*3. Saját ASP.NET Core-alkalmazás az Azure Web Appshez vagyok telepítése Kell továbbra is engedélyezhető a Web Apps Application Insights-bővítmény?*

* SDK-t időben telepítve van, ahogyan az ebben a cikkben, van-e nem kell ahhoz, hogy a Web Apps-portálról az Application Insights bővítményt. Akkor is, ha a bővítmény telepítve van, akkor lesz visszatartási, ha azt észleli, hogy az alkalmazás már szerepel SDK-t. Az Application Insights engedélyezése a bővítmény Önnek nem kell a telepítése és frissítése az SDK-t az alkalmazáshoz. Azonban ez a cikk megfelelően az Application Insights engedélyezése csak olyan rugalmasabb, az alábbi okok miatt.
    1. Application Insights Telemetria továbbra is működni fog
        1. Az összes operációs rendszer – Windows, Linux, Mac rendszerre.
        1. Minden közzétételi mód – önálló vagy keretrendszer-függő.
        1. Az összes cél keretrendszereket, beleértve a teljes .NET-keretrendszer.
        1. Az összes üzemeltetési lehetőségek – Azure Web APP, virtuális gépek, Linux, tárolók, az AKS, nem Azure-beli.
    1. Telemetria látható helyileg, a Visual Studióban történő hibakeresése során.
    1. Lehetővé teszi, hogy követési további egyéni telemetriát `TrackXXX()` API-t.
    1. A konfiguráció teljes hozzáféréssel rendelkezik.

*4. Lehet engedélyezni az Application Insights figyelési hasonló eszközökkel Állapotfigyelőt?*

* Nem. [Az állapotfigyelő](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) és a közelgő helyettesítő [IISConfigurator](https://github.com/Microsoft/ApplicationInsights-Announcements/issues/21) jelenleg csak az ASP.NET támogatja. A dokumentum fog frissülni. Ha támogatja az ASP.NET Core-alkalmazás érhető el.

*5. Az ASP.NET Core 2.0-alkalmazások rendelkezem? Nem az Application Insights automatikusan engedélyezve van a számukra nélkül me csinál semmit?*

* `Microsoft.AspNetCore.All` 2.0-s metapackage Application Insights SDK-t (2.1.0-ás) tartalmazza, és futtatja az alkalmazást a Visual Studio hibakereső funkcióját, ha a Visual Studio lehetővé teszi, hogy az application insights és telemetriát helyileg magát az IDE-ben jeleníti meg. Telemetria az Application Insights szolgáltatásba nem lett elküldve, egy kialakítási kulcs explicit módon není zadána klauzule. Azt javasoljuk, hogy hajtsa végre a jelen cikkben lévő utasítások az Application Insights, ahhoz, hogy még a 2.0-hoz készült alkalmazások.

*6. Az alkalmazásom Linux rendszeren futtatható. Minden funkció támogatja a Linux is?*

* Igen. Az SDK szolgáltatás támogatása a következő azonos az összes platform, a következő kivételekkel:
    1. Teljesítményszámlálók még nem támogatottak a nem-Windows. Ez a dokumentum frissül, Linux-támogatást hozzáadásakor.
    1. Annak ellenére, hogy `ServerTelemetryChannel` engedélyezve van alapértelmezés szerint az alkalmazás fut, a nem Windows rendszerű, ha a csatorna nem hoz létre automatikusan egy helyi tároló mappa a telemetriai adatokat ideiglenesen hálózati merül fel. Ez a korlátozás hatására a telemetriai adatok elvesznek, ha vannak az átmeneti hálózati vagy kiszolgálói problémák. A probléma megoldása van a felhasználó egy helyi mappába a csatorna konfigurálhatja az alább látható módon.

```csharp
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
