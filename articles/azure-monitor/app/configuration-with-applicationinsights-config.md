---
title: ApplicationInsights.config hivatkozás - Azure | Microsoft dokumentumok
description: Az adatgyűjtési modulok engedélyezése vagy letiltása, valamint teljesítményszámlálók és egyéb paraméterek hozzáadása.
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: olegan
ms.openlocfilehash: 3375c24739da8663aa6a40deeb53e02e65d1f9bf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537559"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Az Application Insights SDK konfigurálása az ApplicationInsights.config vagy .xml használatával
Az Application Insights .NET SDK számos NuGet-csomagból áll. Az [alapcsomag](https://www.nuget.org/packages/Microsoft.ApplicationInsights) biztosítja az API-t az Application Insights telemetriai adatok küldéséhez. [További csomagok](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) telemetriai *modulokat* és *inicializálókat* biztosít az alkalmazásból és annak környezetéből származó telemetriai adatok automatikus nyomon követéséhez. A konfigurációs fájl módosításával engedélyezheti vagy letilthatja a telemetriai modulokat és inicializálókat, és paramétereket állíthat be néhányhoz.

A konfigurációs `ApplicationInsights.config` fájl `ApplicationInsights.xml`neve vagy , az alkalmazás típusától függően. Az [SDK legtöbb verziójának telepítésekor][start]a program automatikusan hozzáadja a projekthez. Alapértelmezés szerint az ApplicationInsights.config fájl a projekt gyökérmappájában jön létre, és a rendszer a kiszolgálómappába másolja a visual Studio-sablonprojektek automatikus élményét, amely támogatja az **> Hozzáadása >.** Az [IIS-kiszolgáló állapotfigyelője][redfield]is hozzáadódik egy webalkalmazáshoz. A konfigurációs fájl figyelmen kívül hagyja, ha [az Azure-webhely vagy](azure-web-apps.md) [az Azure virtuális gép bővítménye és a virtuális gép méretezési csoport](azure-vm-vmss-apps.md) használata.

Nincs egyenértékű fájl az [SDK szabályozására egy weboldalon][client].

Ez a dokumentum ismerteti a konfigurációs fájlban látható szakaszokat, az SDK összetevőinek vezérlését, és azt, hogy mely NuGet csomagok töltik be ezeket az összetevőket.

> [!NOTE]
> Az ApplicationInsights.config és a .xml utasítások nem vonatkoznak a .NET Core SDK-ra. A .NET Core alkalmazások konfigurálásához kövesse [ezt az](../../azure-monitor/app/asp-net-core.md) útmutatót.

## <a name="telemetry-modules-aspnet"></a>Telemetriai modulok (ASP.NET)
Minden telemetriai modul egy adott típusú adatokat gyűjt, és az alapvető API-t használja az adatok küldéséhez. A modulokat különböző NuGet csomagok telepítik, amelyek a szükséges sorokat is hozzáadják a .config fájlhoz.

Minden modulhoz van egy csomópont a konfigurációs fájlban. Modul letiltásához törölje a csomópontot, vagy fűzzön hozzá megjegyzést.

### <a name="dependency-tracking"></a>Függőség követése
[A függőség-követés](../../azure-monitor/app/asp-net-dependencies.md) telemetriát gyűjt az alkalmazás által az adatbázisokba, külső szolgáltatásokba és adatbázisokba intézett hívásokról. Ahhoz, hogy ez a modul működjön egy IIS-kiszolgálón, telepítenie kell az [Állapotfigyelőt.][redfield]

Saját függőségkövető kódot is írhat a [TrackDependency API használatával.](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet csomag.

A függőségek automatikusan gyűjthetők anélkül, hogy módosítanák a kódot ügynökalapú (kód nélküli) csatolással. Az Azure-webalkalmazásokban való használathoz engedélyezze az [Application Insights-bővítményt.](azure-web-apps.md) Az Azure VM-ben vagy az Azure virtuálisgép-méretezési csoportban való használatához engedélyezze az [alkalmazásfigyelésbővítményt a virtuális géphez és a virtuális gép méretezési készletéhez.](azure-vm-vmss-apps.md)

### <a name="performance-collector"></a>Teljesítmény-kollektor
[Az IIS-telepítésekből rendszerteljesítmény-számlálókat,](../../azure-monitor/app/performance-counters.md) például processzor-, memória- és hálózati terhelést gyűjt. Megadhatja, hogy mely számlálókat kell begyűjtenie, beleértve a saját maga által beállított teljesítményszámlálókat is.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet csomag.

### <a name="application-insights-diagnostics-telemetry"></a>Application Insights diagnosztikai telemetriai adatok
Az `DiagnosticsTelemetryModule` Application Insights instrumentation kód hibáit jelenti. Ha például a kód nem fér hozzá `ITelemetryInitializer` a teljesítményszámlálókhoz, vagy ha egy kivételt okoz. A modul által nyomon követett nyomkövetési telemetria megjelenik a [Diagnosztikai keresés ben.][diagnostic]

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>Fejlesztői mód
`DeveloperModeWithDebuggerAttachedTelemetryModule`kényszeríti az `TelemetryChannel` Application Insights adatok azonnali küldésére, egy telemetriai elem egy időben, amikor egy hibakereső csatlakozik az alkalmazásfolyamathoz. Ez csökkenti az idő közötti, amikor az alkalmazás nyomon követi a telemetriai adatokat, és amikor megjelenik az Application Insights portálon. Jelentős terhelést okoz a PROCESSZOR és a hálózati sávszélesség.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet csomag

### <a name="web-request-tracking"></a>Webes kérelmek nyomon követése
A HTTP-kérelmek [válaszidejét és eredménykódját](../../azure-monitor/app/asp-net.md) jelenti.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet csomag

### <a name="exception-tracking"></a>Kivételkövetés
`ExceptionTrackingTelemetryModule`nyomon követi a nem kezelt kivételeket a webalkalmazásban. Lásd: [Hibák és kivételek][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet csomag
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`- nyomon követi [a nem megfigyelt feladatkivételeket.](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx)
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`- nyomon követi a feldolgozói szerepkörök, a Windows-szolgáltatások és a konzolalkalmazások nem kezelt kivételeit.
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet csomagot.

### <a name="eventsource-tracking"></a>EventSource-követés
`EventSourceTelemetryModule`Lehetővé teszi, hogy konfigurálja EventSource események et kell küldeni az Application Insights nyomkövetésként. Az EventSource-események nyomon követéséről az [EventSource-események használata című témakörben](../../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events)talál további információt.

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>ETW eseménykövetés
`EtwCollectorTelemetryModule`Lehetővé teszi, hogy konfigurálja az ETW-szolgáltatók eseményeit az Application Insights nak nyomkövetésként elküldendő. Az ETW-események nyomon követésével kapcsolatos további tudnivalókért olvassa el [az ETW-események használata című témakört.](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events)

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
A Microsoft.ApplicationInsights csomag biztosítja az SDK [alapvető API-ját.](https://msdn.microsoft.com/library/mt420197.aspx) A többi telemetriai modulok ezt használják, és azt is [használhatja, hogy meghatározza a saját telemetriai adatokat.](../../azure-monitor/app/api-custom-events-metrics.md)

* Nincs bejegyzés az ApplicationInsights.config fájlba.
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet csomag. Ha csak telepíti ezt a NuGet, nem .config fájl jön létre.

## <a name="telemetry-channel"></a>Telemetriai csatorna
A [telemetriai csatorna](telemetry-channels.md) kezeli a pufferelés és a telemetriai adatok átvitelét az Application Insights szolgáltatásba.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`a webes alkalmazások alapértelmezett csatornája. Puffereli az adatokat a memóriában, és újrapróbálkozási mechanizmusokat és helyi lemeztárolót alkalmaz a megbízhatóbb telemetriai kézbesítés érdekében.
* `Microsoft.ApplicationInsights.InMemoryChannel`egy könnyű telemetriai csatorna, amely akkor használatos, ha nincs más csatorna konfigurálva. 

## <a name="telemetry-initializers-aspnet"></a>Telemetriai inicializálók (ASP.NET)
Telemetriai initializerek beállít környezet tulajdonságait, amelyek a telemetriai adatok minden elemével együtt küldött.

Saját [inicializálókat írhat](../../azure-monitor/app/api-filtering-sampling.md#add-properties) a környezet tulajdonságainak beállításához.

A szabványos inicializálókat a webes vagy a WindowsServer NuGet csomagok határozzák meg:

* `AccountIdTelemetryInitializer`beállítja a AccountId tulajdonságot.
* `AuthenticatedUserIdTelemetryInitializer`A AuthenticatedUserId tulajdonságot a JavaScript SDK által beállítottnak állítja be.
* `AzureRoleEnvironmentTelemetryInitializer`frissíti `RoleName` a `RoleInstance` környezet `Device` és a környezet tulajdonságai az összes telemetriai elemek az Azure futásidejű környezetből kinyert információkat.
* `BuildInfoConfigComponentVersionTelemetryInitializer`frissíti `Version` a környezet `Component` tulajdonságát az összes telemetriai elemhez az MS Build által létrehozott `BuildInfo.config` fájlból kinyert értékkel.
* `ClientIpHeaderTelemetryInitializer`frissíti `Ip` az `Location` összes telemetriai elem környezetének tulajdonságát a `X-Forwarded-For` kérelem HTTP-fejléce alapján.
* `DeviceTelemetryInitializer`frissíti a környezet `Device` következő tulajdonságait az összes telemetriai elemhez.
  * `Type`"PC"
  * `Id`annak a számítógépnek a tartománynevére van beállítva, amelyen a webalkalmazás fut.
  * `OemName`a WMI használatával a `Win32_ComputerSystem.Manufacturer` mezőből kinyert értékre van beállítva.
  * `Model`a WMI használatával a `Win32_ComputerSystem.Model` mezőből kinyert értékre van beállítva.
  * `NetworkType`értéke a. `NetworkInterface`
  * `Language`a t. `CurrentCulture`
* `DomainNameRoleInstanceTelemetryInitializer`frissíti `RoleInstance` a környezet `Device` tulajdonságát az összes telemetriai elem hez annak a számítógépnek a tartománynevével, amelyen a webalkalmazás fut.
* `OperationNameTelemetryInitializer`frissíti `Name` a HTTP-metóduson alapuló `Operation` összes telemetriai elem környezetének `RequestTelemetry` `Name` tulajdonságát, valamint a ASP.NET MVC-vezérlő és a kérés feldolgozásához meghívott művelet nevét.
* `OperationIdTelemetryInitializer`vagy `OperationCorrelationTelemetryInitializer` frissíti `Operation.Id` az összes nyomon követett telemetriai elem környezeti tulajdonságát, miközben a kérést az automatikusan generált `RequestTelemetry.Id`.
* `SessionTelemetryInitializer`frissíti `Id` a környezet `Session` tulajdonságát az összes telemetriai `ai_session` elemhez, a felhasználó böngészőjében futó ApplicationInsights JavaScript instrumentation kód által létrehozott cookie-ból kinyert értékkel.
* `SyntheticTelemetryInitializer`vagy `SyntheticUserAgentTelemetryInitializer` frissíti `User` `Session`a `Operation` , és környezetek tulajdonságait az összes telemetriai elemek nyomon követett egy kérelmet egy szintetikus forrásból, például egy rendelkezésre állási teszt vagy keresőmotor bot. Alapértelmezés szerint [a Metrika kezelő](../../azure-monitor/platform/metrics-charts.md) nem jeleníti meg a szintetikus telemetriai adatokat.

    A `<Filters>` kérelmek tulajdonságait azonosító készlet.
* `UserTelemetryInitializer`frissíti `Id` az `AcquisitionDate` összes `User` telemetriai elem környezetének és `ai_user` tulajdonságainak tulajdonságait az Application Insights JavaScript instrumentation kód által a felhasználó böngészőjében futó cookie-ból kinyert értékekkel.
* `WebTestTelemetryInitializer`beállítja a [rendelkezésre állási tesztekből](../../azure-monitor/app/monitor-web-app-availability.md)származó HTTP-kérelmek felhasználói azonosítóját, munkamenet-azonosítóját és szintetikus forrástulajdonságait.
  A `<Filters>` kérelmek tulajdonságait azonosító készlet.

A Service Fabric `Microsoft.ApplicationInsights.ServiceFabric` ben futó . Ez a `FabricTelemetryInitializer`csomag tartalmaz egy , amely hozzáadja a Service Fabric-tulajdonságok telemetriai elemekhez. További információkért tekintse meg a [GitHub-lapot](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) a NuGet-csomag által hozzáadott tulajdonságokról.

## <a name="telemetry-processors-aspnet"></a>Telemetriai processzorok (ASP.NET)
Telemetriai processzorok szűrheti és módosíthatja az egyes telemetriai elemek et közvetlenül az SDK-ból a portálra küldött.

Írhat [saját telemetriai processzorokat.](../../azure-monitor/app/api-filtering-sampling.md#filtering)

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Adaptív mintavételi telemetriai processzor (2.0.0-beta3-tól)
Ez e beállítás alapértelmezés szerint engedélyezve van. Ha az alkalmazás sok telemetriai adatokat küld, a processzor eltávolítja egy részét.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

A paraméter biztosítja azt a célt, amelyet az algoritmus megpróbál elérni. Az SDK minden példánya egymástól függetlenül működik, így ha a kiszolgáló több gép fürtje, a telemetria tényleges mennyisége ennek megfelelően megszorozzuk.

[További információ a mintavételezésről.](../../azure-monitor/app/sampling.md)

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Rögzített sebességű mintavételi telemetriai processzor (2.0.0-beta1-től)
Van is egy szabványos [mintavételi telemetriai processzor](../../azure-monitor/app/api-filtering-sampling.md) (2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```

## <a name="instrumentationkey"></a>InstrumentationKey (Műszerezési kulcs)
Ez határozza meg az Application Insights erőforrást, amelyben az adatok megjelennek. Általában hozzon létre egy külön erőforrást, külön kulccsal, az egyes alkalmazások.

Ha dinamikusan szeretné beállítani a kulcsot – például ha az alkalmazás eredményeit különböző erőforrásoknak szeretné elküldeni –, kihagyhatja a kulcsot a konfigurációs fájlból, és kódolva is beállíthatja.

A kulcs beállítása a Telemetriai-ügyfél összes példányához, beleértve a szabványos telemetriai modulokat is. Ehhez egy inicializálási módszer, például global.aspx.cs egy ASP.NET szolgáltatás:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights;

    protected void Application_Start()
    {
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
        var telemetryClient = new TelemetryClient(configuration);
   
```

Ha csak egy adott eseménykészletet szeretne küldeni egy másik erőforrásnak, beállíthatja a kulcsot egy adott TelemettryClient számára:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Új kulcs beszerezéséhez [hozzon létre egy új erőforrást az Application Insights portálon.][new]



## <a name="applicationid-provider"></a>Alkalmazásazonosító-szolgáltató

_A 2.6.0-s verziótól kapható_

Ennek a szolgáltatónak az a célja, hogy instrumentation kulcs alapján keressen egy alkalmazásazonosítót. Az alkalmazásazonosító szerepel a RequestTelemetria és a dependencyTelemetry, és a portálkorreláció meghatározására használt.

Ez a kód `TelemetryConfiguration.ApplicationIdProvider` vagy a konfiguráció beállításával érhető el.

### <a name="interface-iapplicationidprovider"></a>Kapcsolat: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Két implementációt biztosítunk a [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) `ApplicationInsightsApplicationIdProvider` `DictionaryApplicationIdProvider`sdk-ben: és .

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Ez egy wrapper körül a profil API.This is a wrapper around our Profile API. Ez szabályozza a kérelmeket és a gyorsítótár eredményeit.

Ez a szolgáltató a [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) vagy a [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) telepítésekor hozzáadja a konfigurációs fájlt a konfigurációs fájlhoz.

Ez az osztály `ProfileQueryEndpoint`választható tulajdonsággal rendelkezik.
Alapértelmezés szerint ez `https://dc.services.visualstudio.com/api/profiles/{0}/appId`a beállítás a értékre van állítva.
Ha ehhez a konfigurációhoz proxyt kell konfigurálnia, javasoljuk, hogy proxyzza meg az alapcímet, és tartalmazza az "/api/profiles/{0}/appId" kapcsolót. Vegye figyelembe, hogy a '{0}' a " a instrumentation kulccsal van helyettesítve.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Példa Konfiguráció ra az ApplicationInsights.config fájlon keresztül:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Példa Konfiguráció kódon keresztül:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>SzótárApplicationIdProvider

Ez egy statikus szolgáltató, amely a konfigurált Instrumentation Kulcs / Alkalmazásazonosító párokra támaszkodik.

Ez az osztály `Defined`rendelkezik egy tulajdonsággal , amely egy szótár<karakterlánc, string> instrumentation kulcs alkalmazásazonosító párok.

Ez az osztály `Next` egy választható tulajdonsággal rendelkezik, amely egy másik szolgáltató konfigurálására használható, ha olyan instrumentation kulcsot kér, amely nem létezik a konfigurációban.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Példa Konfiguráció ra az ApplicationInsights.config fájlon keresztül:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.DictionaryApplicationIdProvider, Microsoft.ApplicationInsights">
        <Defined>
            <Type key="InstrumentationKey_1" value="ApplicationId_1"/>
            <Type key="InstrumentationKey_2" value="ApplicationId_2"/>
        </Defined>
        <Next Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Példa Konfiguráció kódon keresztül:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>További lépések
[További információ az API-ról.][api]

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: ../../azure-monitor/app/asp-net-exceptions.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[new]: ../../azure-monitor/app/create-new-resource.md 
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
