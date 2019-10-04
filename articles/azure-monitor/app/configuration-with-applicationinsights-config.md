---
title: ApplicationInsights. config – útmutató – Azure | Microsoft Docs
description: Az adatgyűjtési modulok engedélyezése vagy letiltása, valamint teljesítményszámlálók és egyéb paraméterek hozzáadása.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 6e397752-c086-46e9-8648-a1196e8078c2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: 02ad74e5b1f8b86a0072b413db2a572f8ed92781
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932151"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Az Application Insights SDK konfigurálása az ApplicationInsights.config vagy .xml használatával
A Application Insights .NET SDK számos NuGet-csomagot tartalmaz. Az [alapcsomag](https://www.nuget.org/packages/Microsoft.ApplicationInsights) biztosítja az API-t, amely telemetria küld a Application Insightsnak. A [további csomagok](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) telemetria *modulokat* és *inicializáló* csomagokat biztosítanak az alkalmazás és a környezet telemetria automatikus nyomon követéséhez. A konfigurációs fájl módosításával engedélyezheti vagy letilthatja a telemetria-modulokat és-inicializálók beállításait, és beállíthat paramétereket.

A konfigurációs fájl neve `ApplicationInsights.config` vagy `ApplicationInsights.xml`, az alkalmazás típusától függően. A rendszer automatikusan hozzáadja a projekthez [az SDK legtöbb verziójának telepítésekor][start]. Alapértelmezés szerint, ha a Visual Studio-sablon olyan projektjeiből származó automatizált élményt használ, amelyek támogatják a **> Application Insights telemetria hozzáadását**, akkor a ApplicationInsights. config fájl a projekt gyökérkönyvtárában jön létre, és amikor a rendszer átmásolja a megfelelést a következőre: bin mappa. Emellett egy webalkalmazáshoz is hozzá lesz adva [Állapotmonitor egy IIS-kiszolgálón][redfield]. A konfigurációs fájlt a rendszer figyelmen [](azure-web-apps.md) kívül hagyja, ha az Azure-beli virtuális [gép és a virtuálisgép-méretezési csoport](azure-vm-vmss-apps.md) bővítményét használja.

Nem található megfelelő fájl az [SDK-nak egy weblapon][client]való vezérléséhez.

Ez a dokumentum a konfigurációs fájlban látható szakaszokat ismerteti, hogyan szabályozzák az SDK összetevőit, és hogy mely NuGet-csomagok töltődnek be ezek az összetevők.

> [!NOTE]
> A ApplicationInsights. config és az. XML utasítások nem vonatkoznak a .NET Core SDKra. A .NET Core-alkalmazások konfigurálásához kövesse [ezt az](../../azure-monitor/app/asp-net-core.md) útmutatót.

## <a name="telemetry-modules-aspnet"></a>Telemetria modulok (ASP.NET)
Az egyes telemetria-modulok adott típusú adatokat gyűjtenek, és az alapszintű API használatával küldik el az adatokat. A modulokat különböző NuGet-csomagok telepítik, és a szükséges sorokat is hozzáadja a. config fájlhoz.

Minden modulhoz van egy csomópont a konfigurációs fájlban. A modul letiltásához törölje a csomópontot, vagy véleményezze azt.

### <a name="dependency-tracking"></a>Függőségek nyomon követése
[](../../azure-monitor/app/asp-net-dependencies.md) A függőségek követése telemetria gyűjt az alkalmazás által az adatbázisokra és a külső szolgáltatásokra és adatbázisokra irányuló hívásokról. A modul IIS-kiszolgálón való működésének engedélyezéséhez telepítenie kell [Állapotmonitor][redfield].

A [TRACKDEPENDENCY API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)használatával is írhat saját függőségi követési kódot.

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet-csomag.

A függőségek automatikusan gyűjthetők a kód módosítása nélkül, ügynök-alapú (kód nélküli) csatolással. Ha az Azure Web Apps szolgáltatásban szeretné használni, engedélyezze a [Application Insights](azure-web-apps.md)-bővítményt. Ha az Azure-beli virtuális gépen vagy az Azure virtuálisgép-méretezési csoporton szeretné használni, engedélyezze a virtuális gép [és a virtuálisgép-méretezési csoport alkalmazás](azure-vm-vmss-apps.md)-figyelési bővítményét.

### <a name="performance-collector"></a>Teljesítmény gyűjtője
[](../../azure-monitor/app/performance-counters.md) A rendszerteljesítmény-számlálókat, például a processzort, a memóriát és a hálózati terhelést az IIS-telepítésből gyűjti. Megadhatja, hogy mely számlálókat szeretné összegyűjteni, beleértve a saját maga beállított teljesítményszámlálók adatait is.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet-csomag.

### <a name="application-insights-diagnostics-telemetry"></a>Application Insights diagnosztikai telemetria
A `DiagnosticsTelemetryModule` jelentés hibákat tartalmaz a Application Insights Instrumentation-kódban. Ha például a kód nem fér hozzá a teljesítményszámlálók számára, vagy `ITelemetryInitializer` kivételt jelez. A modul által követett nyomkövetési telemetria a [diagnosztikai keresésben][diagnostic]jelenik meg.

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>Fejlesztői mód
`DeveloperModeWithDebuggerAttachedTelemetryModule`a Application Insights `TelemetryChannel` azonnal küldi el az adatküldést, egy telemetria-elem egyszerre, amikor egy hibakereső csatlakozik az alkalmazási folyamathoz. Ez csökkenti azt az időtartamot, ameddig az alkalmazás nyomon követi a telemetria, és amikor megjelenik a Application Insights-portálon. Jelentős terhelést okoz a processzor-és a hálózati sávszélességben.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet-csomag

### <a name="web-request-tracking"></a>Webes kérelmek nyomon követése
A HTTP [-kérések válaszideje és eredmény-kódjának](../../azure-monitor/app/asp-net.md) jelentése.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet csomag

### <a name="exception-tracking"></a>Kivétel követése
`ExceptionTrackingTelemetryModule`nyomon követi a webalkalmazás kezeletlen kivételeit. Lásd: [hibák és kivételek][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet csomag
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`– nyomon követi a nem [figyelt feladatok kivételeit](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`– nyomon követi a feldolgozói szerepkörök, a Windows-szolgáltatások és a konzolos alkalmazások nem kezelt kivételeit.
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet-csomag.

### <a name="eventsource-tracking"></a>EventSource követése
`EventSourceTelemetryModule`lehetővé teszi, hogy a nyomkövetési Application Insightsként küldendő EventSource eseményeket konfigurálja. További információ a EventSource-események nyomon követéséről: [EventSource-események használata](../../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>ETW események követése
`EtwCollectorTelemetryModule`lehetővé teszi, hogy a ETW-szolgáltatóktól származó eseményeket a nyomkövetési Application Insights küldje el. További információ a ETW-események nyomon követéséről: [ETW-események használata](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
A Microsoft. ApplicationInsights csomag biztosítja az SDK [alapvető API](https://msdn.microsoft.com/library/mt420197.aspx) -ját. A többi telemetria-modul ezt használja, és azt is [használhatja a saját telemetria definiálásához](../../azure-monitor/app/api-custom-events-metrics.md).

* Nincs bejegyzés a ApplicationInsights. config fájlban.
* [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet-csomag. Ha most telepíti ezt a NuGet, a rendszer nem hoz létre. config fájlt.

## <a name="telemetry-channel"></a>Telemetria-csatorna
A [telemetria-csatorna](telemetry-channels.md) kezeli a telemetria pufferelését és továbbítását a Application Insights szolgáltatás számára.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`a webalkalmazások alapértelmezett csatornája. A szolgáltatás a memóriában puffert használ, és újrapróbálkozási mechanizmusokat és helyi lemezes tárolást alkalmaz a megbízhatóbb telemetria-kézbesítés érdekében.
* `Microsoft.ApplicationInsights.InMemoryChannel`egy egyszerűsített telemetria-csatorna, amely akkor használható, ha nincs más csatorna konfigurálva. 

## <a name="telemetry-initializers-aspnet"></a>Telemetria inicializálók (ASP.NET)
A telemetria inicializálók a telemetria minden eleme mellett eljuttatott környezeti tulajdonságokat határozzák meg.

[Saját inicializálók](../../azure-monitor/app/api-filtering-sampling.md#add-properties) is megírhatók a környezeti tulajdonságok beállításához.

A szabványos inicializálók mind a web-, mind a WindowsServer NuGet-csomagok szerint vannak beállítva:

* `AccountIdTelemetryInitializer`beállítja a AccountId tulajdonságot.
* `AuthenticatedUserIdTelemetryInitializer`a JavaScript SDK által beállított AuthenticatedUserId tulajdonságot állítja be.
* `AzureRoleEnvironmentTelemetryInitializer`Az Azure Runtime `RoleInstance` environmentből kinyert információkkal frissíti az összes telemetria `Device`-elem kontextusát és tulajdonságait. `RoleName`
* `BuildInfoConfigComponentVersionTelemetryInitializer`frissíti az összes telemetria- `Component` `BuildInfo.config` elem környezetének tulajdonságátazMSBuildáltallétrehozottfájlbólkinyertértékkel.`Version`
* `ClientIpHeaderTelemetryInitializer`frissíti `Ip` az `Location` összes`X-Forwarded-For` telemetria-elem környezetének tulajdonságát a kérelem http-fejléce alapján.
* `DeviceTelemetryInitializer`frissíti az összes telemetria-elem `Device` környezetének következő tulajdonságait.
  * `Type`a "PC" értékre van állítva
  * `Id`annak a számítógépnek a tartománynevére van beállítva, amelyen a webalkalmazás fut.
  * `OemName`a a `Win32_ComputerSystem.Manufacturer` mezőből a WMI használatával kinyert értékre van állítva.
  * `Model`a a `Win32_ComputerSystem.Model` mezőből a WMI használatával kinyert értékre van állítva.
  * `NetworkType`értéke a következőből `NetworkInterface`kinyert értékre van állítva:.
  * `Language`értéke a (z `CurrentCulture`) neve.
* `DomainNameRoleInstanceTelemetryInitializer`frissíti az összes telemetria- `Device` elem környezetének tulajdonságátannakaszámítógépnekatartománynevével,amelyenawebalkalmazásfut.`RoleInstance`
* `OperationNameTelemetryInitializer`frissíti a (z) `RequestTelemetry` `Name`ésa (z) `Name` tulajdonságát a http-metódus alapján az összes telemetria-elem környezetében,valamintaASP.netMVC-vezérlőésakérelemfeldolgozásárameghívott`Operation` művelet nevét.
* `OperationIdTelemetryInitializer`vagy `OperationCorrelationTelemetryInitializer` frissíti az `Operation.Id` összes olyan telemetria környezeti tulajdonságát, amelyet a rendszer automatikusan generált `RequestTelemetry.Id`a kérések feldolgozása során.
* `SessionTelemetryInitializer`frissíti a `Session` környezet `ai_session` tulajdonságát minden olyan telemetria elemnél, amely a felhasználó böngészőjében futó ApplicationInsights JavaScript-rendszerállapot-kód által generált cookie-ból kinyert értékkel rendelkezik. `Id`
* `SyntheticTelemetryInitializer`a szintetikus forrásból érkező `Session`kérések kezelésekor követett összes telemetria-elem (például egy rendelkezésre állási teszt vagy a keresőmotor robot) esetében a, és `Operation` a `User`környezetek tulajdonságai is frissülnek.`SyntheticUserAgentTelemetryInitializer` Alapértelmezés szerint a [Metrikaböngésző](../../azure-monitor/app/metrics-explorer.md) nem jeleníti meg a szintetikus telemetria.

    A `<Filters>` kérelmek azonosító tulajdonságainak beállítása.
* `UserTelemetryInitializer`frissíti az `Id` `AcquisitionDate` összesolyan`User` telemetria-elem környezetét és tulajdonságait, amely a felhasználó által futtatott Application Insights JavaScript-rendszerállapot-kód által generált cookie-valkinyertértékekkelrendelkezik.`ai_user` böngésző.
* `WebTestTelemetryInitializer`a [rendelkezésre állási tesztekből](../../azure-monitor/app/monitor-web-app-availability.md)származó HTTP-kérelmek felhasználói azonosítójának, munkamenet-azonosítójának és a szintetikus forrás tulajdonságainak beállítása.
  A `<Filters>` kérelmek azonosító tulajdonságainak beállítása.

Service Fabricban futó .NET-alkalmazások esetén a `Microsoft.ApplicationInsights.ServiceFabric` NuGet csomagot is használhatja. Ez a csomag tartalmazza `FabricTelemetryInitializer`a, amely Service Fabric tulajdonságokat adja hozzá a telemetria elemekhez. További információt a NuGet-csomag által hozzáadott tulajdonságokkal kapcsolatos [GitHub-lapon](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) talál.

## <a name="telemetry-processors-aspnet"></a>Telemetria processzorok (ASP.NET)
A telemetria-processzorok csak az SDK-ból a portálra történő elküldése előtt szűrhetik és módosíthatják az egyes telemetria elemeket.

[Írhat saját telemetria](../../azure-monitor/app/api-filtering-sampling.md#filtering)-processzorokat is.

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

* Min 1
* Max: 1000
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

* Min 1
* Max: 300
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

* Min 1
* Max: 100
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

#### <a name="local-forwarder"></a>Helyi továbbító

A [helyi továbbító](opencensus-local-forwarder.md) egy olyan ügynök, amely különböző SDK-k és keretrendszerek alapján gyűjt Application Insights vagy [OpenCensus](https://opencensus.io/) telemetria, és átirányítja a Application Insightsba. Windows és Linux rendszeren is futtatható. Ha a Application Insights Java SDK-val párosul, a helyi továbbító teljes körű támogatást nyújt az [élő metrikák](../../azure-monitor/app/live-stream.md) és az adaptív mintavételezések számára.

```xml
<Channel type="com.microsoft.applicationinsights.channel.concrete.localforwarder.LocalForwarderTelemetryChannel">
<EndpointAddress><!-- put the hostname:port of your LocalForwarder instance here --></EndpointAddress>

<!-- The properties below are optional. The values shown are the defaults for each property -->

<FlushIntervalInSeconds>5</FlushIntervalInSeconds><!-- must be between [1, 500]. values outside the bound will be rounded to nearest bound -->
<MaxTelemetryBufferCapacity>500</MaxTelemetryBufferCapacity><!-- units=number of telemetry items; must be between [1, 1000] -->
</Channel>
```

Ha a SpringBoot Starter-t használja, adja hozzá a következőt a konfigurációs fájlhoz (Application. properties):

```yml
azure.application-insights.channel.local-forwarder.endpoint-address=<!--put the hostname:port of your LocalForwarder instance here-->
azure.application-insights.channel.local-forwarder.flush-interval-in-seconds=<!--optional-->
azure.application-insights.channel.local-forwarder.max-telemetry-buffer-capacity=<!--optional-->
```

Az alapértelmezett értékek megegyeznek a SpringBoot Application. Properties és a applicationinsights. XML konfigurációval.

## <a name="instrumentationkey"></a>InstrumentationKey
Ez határozza meg azt a Application Insights-erőforrást, amelyben az adatai megjelennek. Jellemzően külön erőforrást hoz létre minden egyes alkalmazáshoz, külön kulccsal.

Ha dinamikusan szeretné beállítani a kulcsot – például ha az alkalmazásból különböző erőforrásokhoz szeretne eredményeket küldeni –, kihagyhatja a kulcsot a konfigurációs fájlból, és beállíthatja azt kód helyett.

A TelemetryClient összes példánya kulcsának beállításához, beleértve a standard telemetria-modulokat is, állítsa a kulcsot a TelemetryConfiguration. Active értékre. Ezt egy inicializálási metódusban kell végrehajtani, például global.aspx.cs egy ASP.NET-szolgáltatásban:

```csharp

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      //...
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

Ez a kód vagy a `TelemetryConfiguration.ApplicationIdProvider` konfiguráció beállításban érhető el.

### <a name="interface-iapplicationidprovider"></a>Felület IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Két implementációt biztosítunk a [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) SDK-ban `ApplicationInsightsApplicationIdProvider` : `DictionaryApplicationIdProvider`és.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Ez egy burkoló a profil API-ról. A kérelmeket és a gyorsítótár eredményeit fogja szabályozni.

Ezt a szolgáltatót a rendszer a [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) vagy a [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) telepítésekor adja hozzá a konfigurációs fájlhoz.

Ez az osztály nem kötelező tulajdonsággal `ProfileQueryEndpoint`rendelkezik.
Alapértelmezés szerint ez a következőre `https://dc.services.visualstudio.com/api/profiles/{0}/appId`van beállítva:.
Ha proxyt kell konfigurálnia ehhez a konfigurációhoz, javasoljuk, hogy az alapcímet proxyként adja meg{0}, beleértve a "/API/Profiles//AppID". Vegye figyelembe,{0}hogy a (z) "

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

Ez az osztály egy tulajdonsággal `Defined`rendelkezik, amely egy szótár < karakterlánc, karakterlánc > az alkalmazás-azonosító párokhoz.

Ez az osztály nem kötelező tulajdonsággal `Next` rendelkezik, amely egy másik szolgáltató konfigurálására szolgál, ha olyan rendszerállapot-kulcsot kér, amely nem létezik a konfigurációban.

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




## <a name="next-steps"></a>További lépések
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
