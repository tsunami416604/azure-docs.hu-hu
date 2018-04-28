---
title: ApplicationInsights.config referencia - Azure |} Microsoft Docs
description: Engedélyezze vagy tiltsa le az adatok gyűjtése modulok, és adja hozzá a teljesítményszámlálók és más paramétereket.
services: application-insights
documentationcenter: ''
author: OlegAnaniev-MSFT
editor: mrbullwinkle
manager: carmonm
ms.assetid: 6e397752-c086-46e9-8648-a1196e8078c2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: 94b6864bec157694e0192597c0fecfa0d3e407ec
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Az Application Insights SDK konfigurálása az ApplicationInsights.config vagy .xml használatával
Az Application Insights .NET SDK NuGet-csomagok számos áll. A [core csomag](http://www.nuget.org/packages/Microsoft.ApplicationInsights) telemetriai adatok küldése az Application Insights az API-t biztosít. [További csomagok](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) adja meg a telemetriai adatok *modulok* és *inicializálók* automatikusan nyomon követése a telemetriai adatok az alkalmazás és a környezetben. A konfigurációs fájl módosításával engedélyezze vagy tiltsa le a telemetria-modulokat és az inicializálók, és némelyikük paramétereinek megadása.

A konfigurációs fájl neve `ApplicationInsights.config` vagy `ApplicationInsights.xml`, az alkalmazás típusától függően. Az automatikusan hozzáadódik a projekt amikor Ön [telepítse az SDK legtöbb verzióit][start]. Is hozzáadódik a webes alkalmazások [állapotfigyelő egy IIS-kiszolgálón][redfield], vagy ha bejelöli az Application Insights [bővítmény, az Azure webhelyén vagy a virtuális gép](app-insights-azure-web-apps.md).

Nincs a vezérlőhöz egy egyenértékű fájlt a [SDK-t egy weblap][client].

Ez a dokumentum ismerteti a szakaszok látható, a konfigurációs fájlban, hogyan azok szabályozza, hogy az SDK összetevői és mely NuGet-csomagok betölteni az összetevőket.

> [!NOTE]
> ApplicationInsights.config és .xml utasítások nem vonatkoznak a .NET Core SDK-val. A .NET Core végrehajtott módosításokat a általában használjuk a appsettings.json fájlt. Például az itt található: a [pillanatkép hibakereső dokumentációját.](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications)

## <a name="telemetry-modules-aspnet"></a>Telemetria modulok (ASP.NET)
Minden telemetriai modul egy adott típusú adatokat gyűjt, és a core API segítségével küldheti az adatokat. A modulok különböző NuGet-csomagok, amelyek is vegye fel a szükséges sorok .config fájl telepíti.

Nincs a csomópont minden modul a konfigurációs fájlban. Modul letiltásához törölje a csomópont, vagy hozzászólási ki.

### <a name="dependency-tracking"></a>A függőségi nyomon követése
[Követés függőségi](app-insights-asp-net-dependencies.md) telemetriai adatainak az alkalmazás hajt végre, és külső szolgáltatások és adatbázisait hívások gyűjti. Ez a modul fog működni az IIS-kiszolgáló engedélyezéséhez kell [Állapotmonitor telepítése][redfield]. A használatára az Azure-webalkalmazásokban vagy a virtuális gépek, [jelölje ki az Application Insights bővítményt](app-insights-azure-web-apps.md).

A saját függőségi nyomkövetési kód használatával is írhat a [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet-csomagot.

### <a name="performance-collector"></a>Teljesítmény-gyűjtő
[Gyűjti a rendszerteljesítmény-számlálók](app-insights-performance-counters.md) például CPU és memória- és hálózati betöltése az IIS telepítése. Megadhat számlálók gyűjthet, többek között a saját kezűleg beállított teljesítményszámlálók.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet-csomagot.

### <a name="application-insights-diagnostics-telemetry"></a>Application Insights diagnosztika Telemetria
A `DiagnosticsTelemetryModule` magát az Application Insights instrumentation kódban hibát jelez. Például ha a kód nem fér hozzá a teljesítményszámlálókat, vagy ha egy `ITelemetryInitializer` kivételt jelez. Ez a modul követik – nyomkövetési telemetria megjelenik a [diagnosztikai keresési][diagnostic]. Diagnosztikai adatokat küld a dc.services.vsallin.net.

* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet-csomagot. Ha csak telepíteni ezt a csomagot, az ApplicationInsights.config fájl nem automatikusan létrejön.

### <a name="developer-mode"></a>Fejlesztői mód
`DeveloperModeWithDebuggerAttachedTelemetryModule` arra kényszeríti az Application Insights `TelemetryChannel` küldendő adatok azonnal, több telemetriai tétel egyszerre, ha van csatolva hibakereső az alkalmazás folyamatának. Ez csökkenti a közötti idő, amikor az alkalmazás telemetriai nyomon követi, és úgy tűnik, az Application Insights portál. A Processzor- és hálózati sávszélesség jelentős terhelést okoz.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet-csomag

### <a name="web-request-tracking"></a>Webes kérelem nyomon követése
Jelentések a [időt és az eredmény válaszkód](app-insights-asp-net.md) a HTTP-kérések.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet-csomag

### <a name="exception-tracking"></a>Kivétel követése
`ExceptionTrackingTelemetryModule` a webalkalmazás kezeletlen kivételek nyomon követi. Lásd: [hibákat és kivételeket][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet-csomag
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` -számok [feladat kivételek észrevétlen](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` -a feldolgozói szerepköröket, a központi windows-szolgáltatások és a konzol alkalmazások nem kezelt kivételek nyomon követi.
* [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet-csomagot.

### <a name="eventsource-tracking"></a>EventSource nyomon követése
`EventSourceTelemetryModule` az Application Insights nyomkövetési adatokat, küldendő EventSource események konfigurálását teszi lehetővé. Információ az EventSource nyomon követés: [használatával EventSource események](app-insights-asp-net-trace-logs.md#using-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>ETW-események követése
`EtwCollectorTelemetryModule` az Application Insights nyomkövetési adatokat, küldendő ETW-szolgáltatóktól származó események konfigurálását teszi lehetővé. Információ az ETW-események nyomon követése: [ETW-esemény használatával](app-insights-asp-net-trace-logs.md#using-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
A Microsoft.ApplicationInsights csomag biztosítja a [API alapvető](https://msdn.microsoft.com/library/mt420197.aspx) SDK. Ezt az egyéb telemetriai modulok használják, és is [segítségével határozza meg a saját telemetriai](app-insights-api-custom-events-metrics.md).

* Nem találhatók bejegyzések applicationinsights.config.
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet-csomagot. Ha most telepíti a NuGet, nem .config fájl jön létre.

## <a name="telemetry-channel"></a>Telemetria csatorna
A telemetria-csatorna a pufferelés és az Application Insights szolgáltatáshoz telemetriai adatok továbbítása kezeli.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` az alapértelmezett csatornán szolgáltatások van. Adatok a memóriában puffereli azt.
* `Microsoft.ApplicationInsights.PersistenceChannel` a konzol alkalmazások alternatív van. Azt is unflushed adatok mentése az állandó tároló az alkalmazás bezárása után, és elküldi azt az alkalmazás indításakor újra.

## <a name="telemetry-initializers-aspnet"></a>Telemetria inicializálók (ASP.NET)
Telemetria inicializálók tulajdonságainak környezetben küldött telemetriai minden elem mellett.

Is [saját inicializálók írási](app-insights-api-filtering-sampling.md#add-properties) környezet tulajdonságainak beállítása.

A szabványos inicializálók be vannak állítva vagy a Web vagy WindowsServer NuGet-csomagot:

* `AccountIdTelemetryInitializer` Beállítja a AccountId tulajdonságot.
* `AuthenticatedUserIdTelemetryInitializer` a AuthenticatedUserId tulajdonság beállítása a JavaScript SDK által beállított.
* `AzureRoleEnvironmentTelemetryInitializer` frissítések a `RoleName` és `RoleInstance` tulajdonságainak a `Device` környezet az Azure futtatókörnyezetben kinyert adatokkal az összes telemetriai adat.
* `BuildInfoConfigComponentVersionTelemetryInitializer` frissítések a `Version` tulajdonsága a `Component` kinyert értékét az összes telemetriai környezetben a `BuildInfo.config` MS Build által.
* `ClientIpHeaderTelemetryInitializer` frissítések `Ip` tulajdonsága a `Location` környezetben az összes telemetriai elem alapján a `X-Forwarded-For` a kérelem HTTP-fejléc.
* `DeviceTelemetryInitializer` következő tulajdonságait a `Device` környezetben az összes telemetriai adat.
  * `Type` "PC" értékre van állítva
  * `Id` értéke a számítógép tartománynevét, amelyen fut a webes alkalmazás.
  * `OemName` kinyert értékre van állítva a `Win32_ComputerSystem.Manufacturer` mezőben a WMI segítségével.
  * `Model` kinyert értékre van állítva a `Win32_ComputerSystem.Model` mezőben a WMI segítségével.
  * `NetworkType` kinyert értékre van állítva a `NetworkInterface`.
  * `Language` a névre van beállítva a `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer` frissítések a `RoleInstance` tulajdonsága a `Device` az összes telemetriai adatokat a tartomány nevét a számítógép, amelyen a webalkalmazás fut a környezetben.
* `OperationNameTelemetryInitializer` frissítések a `Name` tulajdonsága a `RequestTelemetry` és a `Name` tulajdonsága a `Operation` összes telemetriai elem kontextusában a HTTP-metódus, valamint a ASP.NET MVC-vezérlő és feldolgozni a kérelmet meghívott művelet neve alapján.
* `OperationIdTelemetryInitializer` vagy `OperationCorrelationTelemetryInitializer` frissítések a `Operation.Id` context tulajdonság az összes telemetriai elem nyomon követheti az automatikusan létrehozott kérelem kezelése közben `RequestTelemetry.Id`.
* `SessionTelemetryInitializer` frissítések a `Id` tulajdonsága a `Session` kinyert érték az összes telemetriai környezetben a `ai_session` cookie-k jönnek létre a felhasználó böngészőben futó ApplicationInsights JavaScript instrumentation kóddal.
* `SyntheticTelemetryInitializer` vagy `SyntheticUserAgentTelemetryInitializer` frissítések a `User`, `Session` és `Operation` összes telemetriai elemek környezetek tulajdonságainak nyomon követ, egy kérelem egy szintetikus forrásból kezelésekor, például a rendelkezésre állási tesztelése, vagy végezzen keresést a motor botot. Alapértelmezés szerint [Metrikaböngésző](app-insights-metrics-explorer.md) szintetikus telemetriai adatok nem jelennek meg.

    A `<Filters>` azonosítása a kérelem tulajdonságok beállítása.
* `UserTelemetryInitializer` frissítések a `Id` és `AcquisitionDate` tulajdonságainak `User` kinyert értékekkel az összes telemetriai környezetben a `ai_user` az Application Insights JavaScript instrumentation kód fut, a felhasználó által létrehozott cookie-k böngésző.
* `WebTestTelemetryInitializer` a felhasználói azonosítóját, a munkamenet-azonosító és a szintetikus adatforrások tulajdonságainak beállítása a HTTP-kérelmek származó [rendelkezésreállás figyelésére szolgáló tesztek](app-insights-monitor-web-app-availability.md).
  A `<Filters>` azonosítása a kérelem tulajdonságok beállítása.

A Service Fabric-beli .NET-alkalmazásokban, megadhatja a `Microsoft.ApplicationInsights.ServiceFabric` NuGet-csomagot. Ez a csomag tartalmaz egy `FabricTelemetryInitializer`, amely a Service Fabric további tulajdonságokkal bővít telemetriai elemek. További információkért lásd: a [GitHub-oldalon](https://go.microsoft.com/fwlink/?linkid=848457) vett fel a NuGet csomag tulajdonságait.

## <a name="telemetry-processors-aspnet"></a>Telemetria processzor (ASP.NET)
Telemetria processzorok szűréséhez, és minden telemetriai cikk módosítása, közvetlenül az SDK-ból a portálon való továbbítás előtt.

Is [saját telemetriai processzorok írási](app-insights-api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Adaptív mintavételi telemetriai processzor (a 2.0.0-beta3)
Ez e beállítás alapértelmezés szerint engedélyezve van. Az alkalmazás nagy mennyiségű telemetriai adatokat küld, ha a processzor eltávolítja bizonyos része.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

A paraméter a cél eléréséhez, próbálja meg az algoritmus biztosít. Az SDK-példányokhoz egymástól függetlenül, működik, így ha a kiszolgáló egy fürt több gépek, a telemetriai adatok tényleges mennyiségét és ennek megfelelően kell-e.

[További információ a mintavételi](app-insights-sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Rögzített mintavételi telemetriai processzor (a 2.0.0-beta1)
Szerepel továbbá egy szabványos [telemetriai processzor mintavételi](app-insights-api-filtering-sampling.md) (a 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>A csatornaparaméterek (Java)
Ezek a paraméterek befolyásolják, hogyan a Java SDK kell tárolni, valamint a telemetriai adatait, amely összegyűjti az.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
A telemetriai adatok elemek száma, amelyek az SDK-val memórián belüli tároló tárolhatja. A számnak az elérésekor, a telemetria puffer ki van ürítve, – ez azt jelenti, hogy a telemetriai adatok elemek az Application Insights-kiszolgálónak küldött.

* Minimum: 1
* Maximális: 1000
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
Meghatározza, hogy milyen gyakran az adatok tárolása a memóriában tárolt kell kiürítése (Application insights szolgáltatásnak elküldött).

* Minimum: 1
* Maximális: 300
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
Meghatározza a maximális mérete (MB), amely számára engedélyezett az állandó tároló a helyi lemezen való. Ez a tároló nem sikerült továbbítani az Application Insights végpont tárolásakor telemetriai elemekre szolgál. A tároló mérete teljesülésekor új telemetriai elemek elvesznek.

* Minimum: 1
* Maximum: 100
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
Ez határozza meg az Application Insights-erőforrást, amelyen az adatok megjelenik. Általában létrehozhat egy különálló erőforrás külön kulccsal, minden, az alkalmazások.

Ha be szeretné állítani a kulcs dinamikusan – például ha az eredmények elküldi a különböző erőforrások – az alkalmazásból hagyja ki ezt a kulcsot a konfigurációs fájlból, és helyette állítsa a kódban.

A kulcs beállítása TelemetryClient összes példánya esetén, beleértve a szabványos telemetriai modulok, kulcsát állítsa a TelemetryConfiguration.Active a. Egy inicializálási metódust, például egy ASP.NET-szolgáltatásban Global.aspx.cs osztályból tegye a következőket:

```csharp

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Ha csak egy meghatározott események küldése egy másik erőforráscsoportban, a kulcs az egy adott TelemetryClient állíthatja be:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Egy új kulcs beszerzése [hozzon létre egy új erőforrást az Application Insights portáljáról][new].



## <a name="applicationid-provider"></a>ApplicationId szolgáltató

_Rendelkezésre álló v2.6.0 kezdődően_

Ez a szolgáltató célja egy Instrumentation kulcs alapján azonosítóját használva. Az alkalmazásazonosító RequestTelemetry és DependencyTelemetry szerepel, és határozza meg a korrelációs a portálon.

Akkor érhető el, úgy, hogy `TelemetryConfiguration.ApplicationIdProvider` vagy kódot, vagy a konfigurációban.

### <a name="interface-iapplicationidprovider"></a>Felület: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


A két megvalósítások nyújtunk a [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) sdk: `ApplicationInsightsApplicationIdProvider` és `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Ez az a profil Api csomagolásának. Azt fogja adatátviteli kérések és a gyorsítótár eredmények.

A konfigurációs fájlhoz hozzá a szolgáltatót, vagy telepítésekor [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) vagy [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Ez az osztály tulajdonsága egy nem kötelező `ProfileQueryEndpoint`.
Alapértelmezés szerint a beállított érték `https://dc.services.visualstudio.com/api/profiles/{0}/appId`.
Ha ez a konfiguráció esetén a proxy konfigurálása van szüksége, ajánlott proxyhasználat alapszintű címéből és többek között a következőket "/ api/profilok / {0} / appId". Vegye figyelembe, hogy a(z) "{0}" változó helyére kérelmenként futásidőben a Instrumentation kulcsot.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Példa Configuration ApplicationInsights.config keresztül:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Példa Configuration kód használatával:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

Ez az olyan statikus szolgáltatót, amely a konfigurált Instrumentation kulcs hagyatkoznak / alkalmazás azonosítója párokat.

Ez az osztály tulajdonsága `Defined` ez < karakterlánc, karakterlánc > Instrumentation kulcs Dictionary az alkalmazásazonosító párokat.

Ez az osztály tulajdonsága egy nem kötelező `Next` a konfigurációt egy másik szolgáltató használatára, ha egy Instrumentation kulcs van szükség, amely nem létezik konfigurálásához használható.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Példa Configuration ApplicationInsights.config keresztül:
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

#### <a name="example-configuration-via-code"></a>Példa Configuration kód használatával:
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
[További tudnivalók az API-t][api].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
