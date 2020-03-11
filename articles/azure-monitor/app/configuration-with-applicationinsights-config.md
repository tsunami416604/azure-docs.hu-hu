---
title: ApplicationInsights. config – útmutató – Azure | Microsoft Docs
description: Az adatgyűjtési modulok engedélyezése vagy letiltása, valamint teljesítményszámlálók és egyéb paraméterek hozzáadása.
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: olegan
ms.openlocfilehash: b2c407036277b17c0f8c08f3261c932a6dc66624
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361987"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Az Application Insights SDK konfigurálása az ApplicationInsights.config vagy .xml használatával
A Application Insights .NET SDK számos NuGet-csomagot tartalmaz. Az [alapcsomag](https://www.nuget.org/packages/Microsoft.ApplicationInsights) biztosítja az API-t, amely telemetria küld a Application Insightsnak. A [további csomagok](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) telemetria *modulokat* és *inicializáló* csomagokat biztosítanak az alkalmazás és a környezet telemetria automatikus nyomon követéséhez. A konfigurációs fájl módosításával engedélyezheti vagy letilthatja a telemetria-modulokat és-inicializálók beállításait, és beállíthat paramétereket.

A konfigurációs fájl neve `ApplicationInsights.config` vagy `ApplicationInsights.xml`, az alkalmazás típusától függően. A rendszer automatikusan hozzáadja a projekthez [az SDK legtöbb verziójának telepítésekor][start]. Alapértelmezés szerint, ha a Visual Studio-sablon olyan projektjeinek automatikus használatát támogatja, amelyek támogatják a **> Application Insights telemetria hozzáadását**, a ApplicationInsights. config fájl a projekt gyökérkönyvtárában jön létre, és amikor a rendszer bemásolja a fájlt a bin mappába. Emellett egy webalkalmazáshoz is hozzá lesz adva [Állapotmonitor egy IIS-kiszolgálón][redfield]. A konfigurációs fájlt a rendszer figyelmen kívül hagyja, ha az Azure-beli virtuális [gép és a virtuálisgép-méretezési csoport](azure-vm-vmss-apps.md) [bővítményét](azure-web-apps.md) használja.

Nem található megfelelő fájl az [SDK-nak egy weblapon][client]való vezérléséhez.

Ez a dokumentum a konfigurációs fájlban látható szakaszokat ismerteti, hogyan szabályozzák az SDK összetevőit, és hogy mely NuGet-csomagok töltődnek be ezek az összetevők.

> [!NOTE]
> A ApplicationInsights. config és az. XML utasítások nem vonatkoznak a .NET Core SDKra. A .NET Core-alkalmazások konfigurálásához kövesse [ezt az](../../azure-monitor/app/asp-net-core.md) útmutatót.

## <a name="telemetry-modules-aspnet"></a>Telemetria modulok (ASP.NET)
Az egyes telemetria-modulok adott típusú adatokat gyűjtenek, és az alapszintű API használatával küldik el az adatokat. A modulokat különböző NuGet-csomagok telepítik, és a szükséges sorokat is hozzáadja a. config fájlhoz.

Minden modulhoz van egy csomópont a konfigurációs fájlban. A modul letiltásához törölje a csomópontot, vagy véleményezze azt.

### <a name="dependency-tracking"></a>Függőségek nyomon követése
A [függőségek követése](../../azure-monitor/app/asp-net-dependencies.md) telemetria gyűjt az alkalmazás által az adatbázisokra és a külső szolgáltatásokra és adatbázisokra irányuló hívásokról. A modul IIS-kiszolgálón való működésének engedélyezéséhez [telepítenie kell Állapotmonitor][redfield].

A [TRACKDEPENDENCY API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)használatával is írhat saját függőségi követési kódot.

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet-csomag.

A függőségek automatikusan gyűjthetők a kód módosítása nélkül, ügynök-alapú (kód nélküli) csatolással. Ha az Azure Web Apps szolgáltatásban szeretné használni, engedélyezze a [Application Insights-bővítményt](azure-web-apps.md). Ha az Azure-beli virtuális gépen vagy az Azure virtuálisgép-méretezési csoporton szeretné használni, engedélyezze a virtuális gép [és a virtuálisgép-méretezési csoport alkalmazás-figyelési bővítményét](azure-vm-vmss-apps.md).

### <a name="performance-collector"></a>Teljesítmény gyűjtője
A [rendszerteljesítmény-számlálókat](../../azure-monitor/app/performance-counters.md) , például a processzort, a memóriát és a hálózati terhelést az IIS-telepítésből gyűjti. Megadhatja, hogy mely számlálókat szeretné összegyűjteni, beleértve a saját maga beállított teljesítményszámlálók adatait is.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet-csomag.

### <a name="application-insights-diagnostics-telemetry"></a>Application Insights diagnosztikai telemetria
A `DiagnosticsTelemetryModule` a Application Insights Instrumentation-kódban hibákat jelez. Ha például a kód nem fér hozzá a teljesítményszámlálók számára, vagy ha egy `ITelemetryInitializer` kivételt jelez. A modul által követett nyomkövetési telemetria a [diagnosztikai keresésben][diagnostic]jelenik meg.

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>Fejlesztői mód
`DeveloperModeWithDebuggerAttachedTelemetryModule` kényszeríti a Application Insights `TelemetryChannel`, hogy azonnal küldje el az adatküldés egy telemetria-elemét, amikor egy hibakereső csatlakozik az alkalmazási folyamathoz. Ez csökkenti azt az időtartamot, ameddig az alkalmazás nyomon követi a telemetria, és amikor megjelenik a Application Insights-portálon. Jelentős terhelést okoz a processzor-és a hálózati sávszélességben.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet-csomag

### <a name="web-request-tracking"></a>Webes kérelmek nyomon követése
A HTTP [-kérések válaszideje és eredmény-kódjának](../../azure-monitor/app/asp-net.md) jelentése.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet csomag

### <a name="exception-tracking"></a>Kivétel követése
`ExceptionTrackingTelemetryModule` nyomon követi a nem kezelt kivételeket a webalkalmazásban. Lásd: [hibák és kivételek][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet csomag
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` – nyomon követi a nem [figyelt feladatok kivételeit](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` – nyomon követi a feldolgozói szerepkörök, a Windows-szolgáltatások és a konzolos alkalmazások kezeletlen kivételeit.
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet-csomag.

### <a name="eventsource-tracking"></a>EventSource követése
`EventSourceTelemetryModule` lehetővé teszi, hogy EventSource-eseményeket küldjön a Application Insights nyomkövetésként. További információ a EventSource-események nyomon követéséről: [EventSource-események használata](../../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft. ApplicationInsights. EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>ETW események követése
`EtwCollectorTelemetryModule` lehetővé teszi, hogy a ETW-szolgáltatóktól származó eseményeket a rendszer nyomkövetésként Application Insights küldje el. További információ a ETW-események nyomon követéséről: [ETW-események használata](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft. ApplicationInsights. EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
A Microsoft. ApplicationInsights csomag biztosítja az SDK [alapvető API](https://msdn.microsoft.com/library/mt420197.aspx) -ját. A többi telemetria-modul ezt használja, és azt is [használhatja a saját telemetria definiálásához](../../azure-monitor/app/api-custom-events-metrics.md).

* Nincs bejegyzés a ApplicationInsights. config fájlban.
* [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet-csomag. Ha most telepíti ezt a NuGet, a rendszer nem hoz létre. config fájlt.

## <a name="telemetry-channel"></a>Telemetria-csatorna
A [telemetria-csatorna](telemetry-channels.md) kezeli a telemetria pufferelését és továbbítását a Application Insights szolgáltatás számára.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` a webalkalmazások alapértelmezett csatornája. A szolgáltatás a memóriában puffert használ, és újrapróbálkozási mechanizmusokat és helyi lemezes tárolást alkalmaz a megbízhatóbb telemetria-kézbesítés érdekében.
* `Microsoft.ApplicationInsights.InMemoryChannel` egy egyszerűsített telemetria-csatorna, amely akkor használható, ha nincs más csatorna konfigurálva. 

## <a name="telemetry-initializers-aspnet"></a>Telemetria inicializálók (ASP.NET)
A telemetria inicializálók a telemetria minden eleme mellett eljuttatott környezeti tulajdonságokat határozzák meg.

[Saját inicializálók is megírhatók](../../azure-monitor/app/api-filtering-sampling.md#add-properties) a környezeti tulajdonságok beállításához.

A szabványos inicializálók mind a web-, mind a WindowsServer NuGet-csomagok szerint vannak beállítva:

* `AccountIdTelemetryInitializer` beállítja a AccountId tulajdonságot.
* `AuthenticatedUserIdTelemetryInitializer` a JavaScript SDK által beállított AuthenticatedUserId tulajdonságot állítja be.
* `AzureRoleEnvironmentTelemetryInitializer` frissíti az `Device` környezet `RoleName` és `RoleInstance` tulajdonságait az Azure Runtime environmentből kinyert adatokat tartalmazó összes telemetria elemhez.
* `BuildInfoConfigComponentVersionTelemetryInitializer` frissíti az `Component` környezet `Version` tulajdonságát az összes telemetria elemnél az MS Build által létrehozott `BuildInfo.config` fájlból kinyert értékkel.
* `ClientIpHeaderTelemetryInitializer` frissíti az összes telemetria `Location` környezetének `Ip` tulajdonságát a kérelem `X-Forwarded-For` HTTP-fejléce alapján.
* `DeviceTelemetryInitializer` frissíti a `Device` környezet következő tulajdonságait az összes telemetria elemre vonatkozóan.
  * `Type` a "PC" értékre van állítva
  * a `Id` annak a számítógépnek a tartománynevére van beállítva, amelyen a webalkalmazás fut.
  * a `OemName` a `Win32_ComputerSystem.Manufacturer` mezőből a WMI használatával kinyert értékre van állítva.
  * a `Model` a `Win32_ComputerSystem.Model` mezőből a WMI használatával kinyert értékre van állítva.
  * a `NetworkType` a `NetworkInterface`kinyert értékre van állítva.
  * a `Language` a `CurrentCulture`nevére van beállítva.
* `DomainNameRoleInstanceTelemetryInitializer` frissíti az `Device` környezet `RoleInstance` tulajdonságát az összes telemetria elemnél annak a számítógépnek a tartománynevével, amelyen a webalkalmazás fut.
* `OperationNameTelemetryInitializer` frissíti a `RequestTelemetry` `Name` tulajdonságát, valamint a HTTP metódus alapján a telemetria összes elemének `Operation` környezetének `Name` tulajdonságát, valamint a ASP.NET MVC-vezérlő és a kérelem feldolgozására meghívott művelet nevét.
* `OperationIdTelemetryInitializer` vagy `OperationCorrelationTelemetryInitializer` frissíti az összes olyan telemetria `Operation.Id` Context tulajdonságát, amelyet az automatikusan generált `RequestTelemetry.Id`rendelkező kérések feldolgozása során követett.
* `SessionTelemetryInitializer` frissíti a `Session` környezet `Id` tulajdonságát az összes olyan telemetria elemnél, amely a felhasználó böngészőjében futó ApplicationInsights JavaScript Instrumentation-kód által generált `ai_session` cookie-ból kinyert értékkel rendelkezik.
* a `SyntheticTelemetryInitializer` vagy `SyntheticUserAgentTelemetryInitializer` frissíti a szintetikus forrásból érkező kérések (például egy rendelkezésre állási teszt vagy a keresőmotor robot) alapján nyomon követett összes telemetria-elem `User`, `Session`és `Operation` környezetek tulajdonságait. Alapértelmezés szerint a [Metrikaböngésző](../../azure-monitor/app/metrics-explorer.md) nem jeleníti meg a szintetikus telemetria.

    A `<Filters>` a kérések tulajdonságainak azonosítására szolgáló készletet.
* `UserTelemetryInitializer` frissíti az `User` környezet `Id` és `AcquisitionDate` tulajdonságait minden olyan telemetria elemnél, amely a felhasználó böngészőjében futó `ai_user` JavaScript-rendszerállapot-kód által generált Application Insights-cookie-ból kinyert értékekkel rendelkezik.
* `WebTestTelemetryInitializer` a [rendelkezésre állási tesztekből](../../azure-monitor/app/monitor-web-app-availability.md)származó HTTP-kérelmek felhasználói azonosítóját, munkamenet-azonosítóját és szintetikus forrás tulajdonságait állítja be.
  A `<Filters>` a kérések tulajdonságainak azonosítására szolgáló készletet.

Service Fabricban futó .NET-alkalmazások esetén a `Microsoft.ApplicationInsights.ServiceFabric` NuGet csomagot is használhatja. Ez a csomag egy `FabricTelemetryInitializer`tartalmaz, amely Service Fabric tulajdonságokat tesz elérhetővé telemetria elemekhez. További információt a NuGet-csomag által hozzáadott tulajdonságokkal kapcsolatos [GitHub-lapon](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) talál.

## <a name="telemetry-processors-aspnet"></a>Telemetria processzorok (ASP.NET)
A telemetria-processzorok csak az SDK-ból a portálra történő elküldése előtt szűrhetik és módosíthatják az egyes telemetria elemeket.

[Írhat saját telemetria-processzorokat](../../azure-monitor/app/api-filtering-sampling.md#filtering)is.

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Adaptív mintavételi telemetria processzor (a 2.0.0-beta3)
Ez e beállítás alapértelmezés szerint engedélyezve van. Ha az alkalmazás sok telemetria küld, a processzor eltávolítja azt.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

A paraméter biztosítja azt a célt, amelyet az algoritmus megpróbál elérni. Az SDK minden példánya egymástól függetlenül működik, így ha a kiszolgáló több gép fürtje, a rendszer ennek megfelelően megszorozza a telemetria tényleges mennyiségét.

[További információ a mintavételezésről](../../azure-monitor/app/sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Rögzített arányú mintavételi telemetria processzor (a 2.0.0-béta-ről)
Létezik egy standard [mintavételi telemetria processzor](../../azure-monitor/app/api-filtering-sampling.md) is (az 2.0.1-ből):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Csatorna paramétereinek (Java)
Ezek a paraméterek befolyásolják, hogy a Java SDK hogyan tárolja és ürítse ki a gyűjtött telemetria-adatokat.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
Az SDK memóriában tárolt tárolójában tárolható telemetria-elemek száma. Ha eléri ezt a számot, a rendszer kiüríti a telemetria-puffert – vagyis a telemetria-elemeket a rendszer elküldi a Application Insights kiszolgálónak.

* Perc: 1
* Max.: 1000
* Alapértelmezett: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds
Meghatározza, hogy a memóriában tárolt tárolóban tárolt adatmennyiséget milyen gyakran kell kiüríteni (Application Insights).

* Perc: 1
* Max.: 300
* Alapértelmezett: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB
Meghatározza a helyi lemezen lévő állandó tárterületre kiosztott MB maximális méretét. Ez a tároló a Application Insights végpontnak nem továbbítandó telemetria elemek megőrzésére szolgál. Ha a tárolási méret teljesül, a rendszer elveti az új telemetria elemeket.

* Perc: 1
* Max.: 100
* Alapértelmezett: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```

## <a name="instrumentationkey"></a>InstrumentationKey
Ez határozza meg azt a Application Insights-erőforrást, amelyben az adatai megjelennek. Jellemzően külön erőforrást hoz létre minden egyes alkalmazáshoz, külön kulccsal.

Ha dinamikusan szeretné beállítani a kulcsot – például ha az alkalmazásból különböző erőforrásokhoz szeretne eredményeket küldeni –, kihagyhatja a kulcsot a konfigurációs fájlból, és beállíthatja azt kód helyett.

A TelemetryClient összes példánya kulcsának beállítása, beleértve a standard szintű telemetria-modulokat. Ezt egy inicializálási metódusban kell végrehajtani, például global.aspx.cs egy ASP.NET-szolgáltatásban:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights;

    protected void Application_Start()
    {
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
        var telemetryClient = new TelemetryClient(configuration);
   
```

Ha csak egy adott eseményt szeretne elküldeni egy másik erőforrásba, beállíthatja egy adott TelemetryClient kulcsát:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Új kulcs beszerzéséhez [hozzon létre egy új erőforrást a Application Insights-portálon][new].



## <a name="applicationid-provider"></a>ApplicationId-szolgáltató

_Elérhető a v 2.6.0-től_

Ennek a szolgáltatónak a célja egy alkalmazás AZONOSÍTÓjának megkeresése a kialakítási kulcs alapján. Az alkalmazás azonosítója a RequestTelemetry és a DependencyTelemetry része, és a portál korrelációjának meghatározására szolgál.

Ezt a kód vagy a konfiguráció `TelemetryConfiguration.ApplicationIdProvider` beállításával érheti el.

### <a name="interface-iapplicationidprovider"></a>Illesztő: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Két implementációt biztosítunk a [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) SDK-ban: `ApplicationInsightsApplicationIdProvider` és `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Ez egy burkoló a profil API-ról. A kérelmeket és a gyorsítótár eredményeit fogja szabályozni.

Ezt a szolgáltatót a rendszer a [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) vagy a [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) telepítésekor adja hozzá a konfigurációs fájlhoz.

Ez az osztály nem kötelező tulajdonsággal rendelkezik `ProfileQueryEndpoint`.
Alapértelmezés szerint ez `https://dc.services.visualstudio.com/api/profiles/{0}/appId`értékre van állítva.
Ha proxyt kell konfigurálnia ehhez a konfigurációhoz, javasoljuk, hogy az alapcímet proxyként adja meg, beleértve a "/API/Profiles/{0}/appId". Vegye figyelembe, hogy a (z) "{0}" a rendszerállapot-kulccsal való futtatáskor, egy kérelemnél.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Példa konfiguráció a ApplicationInsights. config használatával:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Példa konfiguráció kód használatával:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

Ez egy statikus szolgáltató, amely a konfigurált kialakítási kulcs/alkalmazás-azonosító párokra támaszkodik.

Ennek az osztálynak van egy `Defined`tulajdonsága, amely egy szótár < karakterlánc, karakterlánc > a kialakítási kulcs az alkalmazás-azonosító párokhoz.

Ez az osztály nem kötelező tulajdonsággal rendelkezik, `Next` amely a konfigurációban nem létező rendszerállapot-kulcs igényléséhez használható.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Példa konfiguráció a ApplicationInsights. config használatával:
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

#### <a name="example-configuration-via-code"></a>Példa konfiguráció kód használatával:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>Következő lépések
[További információ az API-ról][api].

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: ../../azure-monitor/app/asp-net-exceptions.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[new]: ../../azure-monitor/app/create-new-resource.md 
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
