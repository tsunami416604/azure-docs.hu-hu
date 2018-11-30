---
title: ApplicationInsights.config referencia – Azure |} A Microsoft Docs
description: Engedélyezze vagy tiltsa le az adatgyűjtő modulok adatok, és adja hozzá a teljesítményszámlálók és más paramétereket.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 6e397752-c086-46e9-8648-a1196e8078c2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: b8e9a5591c33368698172d996d5c8ac699ad4602
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52335197"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Az Application Insights SDK konfigurálása az ApplicationInsights.config vagy .xml használatával
Az Application Insights .NET SDK NuGet-csomagok számos áll. A [core csomag](http://www.nuget.org/packages/Microsoft.ApplicationInsights) az API-t biztosít a telemetria küldését az Application Insights. [További csomagok](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) adja meg a telemetriai adatok *modulok* és *inicializálók* automatikusan nyomon követési telemetria az alkalmazás és a környezetben. A konfigurációs fájl módosításával engedélyezze vagy tiltsa le a telemetriai adatok modulok és az inicializálók, és némelyike paramétereinek megadása.

A konfigurációs fájl neve `ApplicationInsights.config` vagy `ApplicationInsights.xml`az alkalmazás típusától függően. Automatikusan hozzáadódik a projekthez, amikor Ön [telepítheti az SDK legtöbb verzióját][start]. Is megjelenik a webalkalmazás [Állapotfigyelőt az IIS-kiszolgálón történő][redfield], vagy amikor kiválasztja az Application Insights [egy Azure-beli webhelyen vagy a VM-bővítmény](app-insights-azure-web-apps.md).

Egy ezzel egyenértékű fájlt a vezérlő nem létezik a [SDK egy weblapon][client].

Ez a dokumentum ismerteti a szakaszok jelenik meg a konfigurációs fájlt, hogy szabályozzák az SDK összetevői, és mely NuGet-csomagok betöltése összetevőket.

> [!NOTE]
> Csak akkor érvényesíthetők ApplicationInsights.config és .xml utasításokat a .NET Core SDK-t. Egy .NET Core-alkalmazást módosítások általában használjuk az appsettings.json fájlt. Ez egy példát találhat a [Snapshot Debugger dokumentációját.](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications)

## <a name="telemetry-modules-aspnet"></a>Telemetria modulok (ASP.NET)
Minden telemetriai modul egy adott típusú adatokat gyűjt, és a fő API segítségével az adatok küldése. A modulok telepítése különböző NuGet-csomagok, amelyek is hozzáadhat a .config fájlt a szükséges sorok szerint.

Egy csomópont van a konfigurációs fájlban, az egyes modulok. Modul letiltásához törölje a csomópont, vagy megjegyzéssé alakíthatja ki.

### <a name="dependency-tracking"></a>Függőségi nyomkövetés
[Függőségi nyomkövetés](app-insights-asp-net-dependencies.md) gyűjt telemetriai adatokat gyűjthessen az alkalmazás lehetővé teszi az adatbázisok és a külső szolgáltatások és az adatbázisok hívások. Ahhoz, hogy ez a modul egy IIS-kiszolgálón működik, kell [telepítse az Állapotfigyelőt][redfield]. Az Azure web apps vagy a virtuális gépek, a használandó [válassza ki az Application Insights bővítményt](app-insights-azure-web-apps.md).

A saját függőségi nyomkövetés a kódot is írhat a [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet-csomagot.

### <a name="performance-collector"></a>Teljesítmény-gyűjtő
[Gyűjti a rendszerteljesítmény-számlálók](app-insights-performance-counters.md) például a CPU, memória- és betölteni az IIS telepítését. Megadhat számlálók összegyűjtése, többek között a teljesítményszámlálók állított be saját magának.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet-csomagot.

### <a name="application-insights-diagnostics-telemetry"></a>Application Insights diagnosztikai Telemetria
A `DiagnosticsTelemetryModule` jelenti a hibákat a kódban az Application Insights instrumentation magát. Például ha a kódot a teljesítményszámlálók nem férhet hozzá, vagy ha egy `ITelemetryInitializer` kivételt jelez. Ez a modul által nyomon követett híváslánc-telemetria megjelenik a [diagnosztikai keresés][diagnostic]. Diagnosztikai adatokat küld a dc.services.vsallin.net.

* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet-csomagot. Ha csak telepíteni ezt a csomagot, az ApplicationInsights.config fájlban nem jön automatikusan létre.

### <a name="developer-mode"></a>Fejlesztői mód
`DeveloperModeWithDebuggerAttachedTelemetryModule` arra kényszeríti az Application Insights `TelemetryChannel` is küldhet adatokat, azonnal egyszerre, amikor egy hibakereső kapcsolódik az alkalmazás folyamatának egyik telemetriaelemhez. Ez csökkenti a pillanattól idő, amikor az alkalmazás telemetriai nyomon követi, és az Application Insights portálon megjelenő. A Processzor és a hálózati sávszélesség jelentős többletterhelést okoz.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet-csomag

### <a name="web-request-tracking"></a>Webes kérelmek nyomon követése
Jelentések a [idő és az eredmény válaszkód](app-insights-asp-net.md) a HTTP-kérések.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet-csomag

### <a name="exception-tracking"></a>Kivétel követése
`ExceptionTrackingTelemetryModule` a webalkalmazás előforduló kezeletlen kivételek nyomon követi. Lásd: [hibák és kivételek][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet-csomag
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` -számok [feladat kivételek észrevétlen](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` -a feldolgozói szerepkörök, a windows-szolgáltatások és a konzol alkalmazások nem kezelt kivételek nyomon követi.
* [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet-csomagot.

### <a name="eventsource-tracking"></a>EventSource nyomon követése
`EventSourceTelemetryModule` az Application Insights nyomkövetésként küldendő EventSource események konfigurálását teszi lehetővé. A követési események EventSource információkért lásd: [EventSource események használatával](app-insights-asp-net-trace-logs.md#using-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>ETW-események követése
`EtwCollectorTelemetryModule` az Application Insights nyomkövetésként küldendő ETW-szolgáltató eseményei konfigurálását teszi lehetővé. ETW-események nyomon követésével kapcsolatos információkért lásd: [ETW-események használatával](app-insights-asp-net-trace-logs.md#using-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
A Microsoft.ApplicationInsights csomagot biztosít a [core API](https://msdn.microsoft.com/library/mt420197.aspx) SDK. A telemetriai adatok modulok használja, és is [segítségével határozza meg a saját telemetriája](app-insights-api-custom-events-metrics.md).

* Nem tartozik bejegyzés az applicationinsights.config fájlban.
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet-csomagot. Csak a NuGet telepítése esetén nem .config fájl jön létre.

## <a name="telemetry-channel"></a>Telemetria-csatorna
A telemetriai adatok csatorna pufferelés és továbbítását a telemetria az Application Insights szolgáltatás kezeli.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` a szolgáltatások az alapértelmezett csatornán. Adatok a memóriában puffereli azt.
* `Microsoft.ApplicationInsights.PersistenceChannel` a alternatívája a konzolalkalmazást. Azt mentheti unflushed adatok állandó tárolókba, amikor az alkalmazás bezárul, és elküldi azt az alkalmazás indításakor újra.

## <a name="telemetry-initializers-aspnet"></a>Telemetria inicializálók (ASP.NET)
Környezeti tulajdonsága, valamint a telemetria minden eleméhez küldött telemetriai adatok inicializálók megadása

Is [írhat saját inicializálók](app-insights-api-filtering-sampling.md#add-properties) környezeti tulajdonságok beállításához.

A standard szintű inicializálók összes állítottak vagy a Web- vagy WindowsServer NuGet-csomagokat:

* `AccountIdTelemetryInitializer` az AccountId tulajdonságát állítja be.
* `AuthenticatedUserIdTelemetryInitializer` a JavaScript SDK által beállított AuthenticatedUserId tulajdonságát állítja be.
* `AzureRoleEnvironmentTelemetryInitializer` frissítések a `RoleName` és `RoleInstance` tulajdonságait a `Device` környezet összes telemetriai cikkhez az Azure futtatókörnyezet kinyert adatokkal.
* `BuildInfoConfigComponentVersionTelemetryInitializer` frissítések a `Version` tulajdonsága a `Component` környezete összes telemetriai elem kinyert értékkel a `BuildInfo.config` MS Build által előállított fájlt.
* `ClientIpHeaderTelemetryInitializer` frissítések `Ip` tulajdonságát a `Location` összes telemetriai elem kontextusában alapján a `X-Forwarded-For` a kérelem HTTP-fejléc.
* `DeviceTelemetryInitializer` a következő tulajdonságait frissíti a `Device` az összes telemetriai elem környezetével.
  * `Type` "PC" értékre van állítva
  * `Id` értéke a számítógép tartományneve a webalkalmazást futtató kiszolgáló.
  * `OemName` a kinyert értékre van állítva a `Win32_ComputerSystem.Manufacturer` mezőt, a WMI használatával.
  * `Model` a kinyert értékre van állítva a `Win32_ComputerSystem.Model` mezőt, a WMI használatával.
  * `NetworkType` a kinyert értékre van állítva a `NetworkInterface`.
  * `Language` nevére van beállítva a `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer` frissítések a `RoleInstance` tulajdonságát a `Device` környezet a számítógépen, amelyen a webalkalmazás fut, a tartomány nevét az összes telemetriai elemek.
* `OperationNameTelemetryInitializer` frissítések a `Name` tulajdonságát a `RequestTelemetry` és a `Name` tulajdonságát a `Operation` összes telemetriai elem kontextusában a HTTP-metódus, valamint a ASP.NET MVC-vezérlő és a kérelem feldolgozását meghívott művelet neve alapján.
* `OperationIdTelemetryInitializer` vagy `OperationCorrelationTelemetryInitializer` frissítések a `Operation.Id` környezeti tulajdonsága az összes telemetriai elem közben az automatikusan létrehozott kérések nyomon követett `RequestTelemetry.Id`.
* `SessionTelemetryInitializer` frissítések a `Id` tulajdonságát a `Session` kinyert értékkel rendelkező összes telemetriai elem környezetét a `ai_session` cookie-k a felhasználó böngészőjében futó applicationinsights – JavaScript-kialakítási kód által generált.
* `SyntheticTelemetryInitializer` vagy `SyntheticUserAgentTelemetryInitializer` frissítések a `User`, `Session`, és `Operation` környezetek tulajdonságokat az összes telemetriai elem nyomon követni a kérések feldolgozása során a szintetikus forrásból, például egy rendelkezésre állási teszt, vagy keressen motor robot. Alapértelmezés szerint [Metrikaböngésző](app-insights-metrics-explorer.md) szintetikus telemetriai adatok nem jelennek meg.

    A `<Filters>` azonosítási a kérések tulajdonságainak beállítása.
* `UserTelemetryInitializer` frissítések a `Id` és `AcquisitionDate` tulajdonságainak `User` kinyert értékek az összes telemetriai elem környezetét a `ai_user` az Application Insights JavaScript instrumentation kód fut, a felhasználó által létrehozott cookie-k böngésző.
* `WebTestTelemetryInitializer` a felhasználói azonosítóját, a munkamenet-azonosító és a szintetikus adatforrás tulajdonságai beállítása a HTTP-kérések származó [rendelkezésre állási tesztek](app-insights-monitor-web-app-availability.md).
  A `<Filters>` azonosítási a kérések tulajdonságainak beállítása.

A Service Fabric-ban futó .NET-alkalmazásokban, hozzáadhatja a `Microsoft.ApplicationInsights.ServiceFabric` NuGet-csomagot. Ez a csomag tartalmaz egy `FabricTelemetryInitializer`, amely a Service Fabric további tulajdonságokkal bővít telemetriai. További információkért lásd: a [GitHub-oldalon](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) hozzá a NuGet-csomag tulajdonságait.

## <a name="telemetry-processors-aspnet"></a>Telemetria feldolgozók (ASP.NET)
Telemetria processzorok szűrheti és módosíthatja mindegyik telemetriaelemhez, az SDK-ból a portálon való továbbítás előtt.

Is [írhat saját telemetriát processzorok](app-insights-api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Az adaptív mintavételezési telemetriai processzor (a 2.0.0-beta3)
Ez e beállítás alapértelmezés szerint engedélyezve van. Ha az alkalmazása sok telemetriát küld, a processzor, néhány eltávolítja.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

A paraméter a cél elérése érdekében próbálja meg az algoritmus biztosít. Az SDK-t minden egyes példányánál egymástól függetlenül, működik, így ha a kiszolgáló egy fürt több gépet, a telemetriai adatok tényleges mérete szorozva ennek megfelelően kell-e.

[További tudnivalók a mintavételezésről](app-insights-sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Rögzített mintavételi telemetriai processzor (a 2.0.0-beta1)
Emellett van egy standard [telemetriai processzor mintavételi](app-insights-api-filtering-sampling.md) (a 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Csatornán paraméterekkel (Java)
Ezeket a paramétereket befolyásolják, hogyan a Java SDK-t kell tárolni és a gyűjtött telemetriai adatait.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
Az SDK-t a memóriában tárolt tárolható telemetriai elemek száma. Ha eléri ezt a számot, a telemetriai adatok kiürítené –, a telemetriai adatok elemek érkeznek az Application Insights-kiszolgáló.

* Minimum: 1
* Maximális száma: 1000
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
Meghatározza, hogy milyen gyakran a memórián belüli storage-ban tárolt adatok ki kell üríteni (az Application Insightsnak elküldött).

* Minimum: 1
* Maximális száma: 300
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
Meghatározza a maximális mérete (MB), amely a helyi lemezen az állandó tároló számára engedélyezett. Ez a tároló megőrzése telemetriai elemeket, amelyeket nem sikerült az Application Insights-végpont továbbíthatja a Microsoftnak szolgál. A tároló mérete feltétele teljesült, amikor új telemetriai tételek elvesznek.

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

#### <a name="local-forwarder"></a>Helyi továbbító

[Helyi továbbító](https://docs.microsoft.com/azure/application-insights/opencensus-local-forwarder) olyan ügynök, amely gyűjti az Application Insights vagy [OpenCensus](https://opencensus.io/) használati adatok gyűjtése a különböző SDK-k és keretrendszereket és továbbítja azt az Application Insights. Windows és Linux alatt alkalmas állapotban. Amikor az Application Insights Java SDK szolgáltatással párosítva a helyi továbbító teljes mértékben támogatja [élő mérőszámok](app-insights-live-stream.md) és adaptív mintavételezés.

```xml
<Channel type="com.microsoft.applicationinsights.channel.concrete.localforwarder.LocalForwarderTelemetryChannel">
<EndpointAddress><!-- put the hostname:port of your LocalForwarder instance here --></EndpointAddress>

<!-- The properties below are optional. The values shown are the defaults for each property -->

<FlushIntervalInSeconds>5</FlushIntervalInSeconds><!-- must be between [1, 500]. values outside the bound will be rounded to nearest bound -->
<MaxTelemetryBufferCapacity>500</MaxTelemetryBufferCapacity><!-- units=number of telemetry items; must be between [1, 1000] -->
</Channel>
```

SpringBoot alapszintű használja, ha a konfigurációs fájl (application.properties) adja hozzá a következő:

```yml
azure.application-insights.channel.local-forwarder.endpoint-address=<!--put the hostname:port of your LocalForwarder instance here-->
azure.application-insights.channel.local-forwarder.flush-interval-in-seconds=<!--optional-->
azure.application-insights.channel.local-forwarder.max-telemetry-buffer-capacity=<!--optional-->
```

Alapértelmezett értékek azonosak SpringBoot application.properties és applicationinsights.xml konfigurációját.

## <a name="instrumentationkey"></a>InstrumentationKey
Ez határozza meg az Application Insights-erőforrást, amelyben az adatok jelennek meg. Általában létrehozhat egy külön erőforrás külön kulccsal, minden, az alkalmazások.

Ha szeretné állítani a kulcs dinamikusan – például ha szeretne küldeni az eredményeket a különböző erőforrások – az alkalmazásból, hagyja ki a kulcsot a konfigurációs fájlból, és ehelyett beállíthatja a programkódban.

TelemetryClient összes példánya esetén állítsa be a kulcsot, beleértve a normál telemetriai modulok, állítsa a kulcs TelemetryConfiguration.Active. Ehhez egy inicializálási metódust, például a Global.aspx.cs osztályból, egy ASP.NET-szolgáltatásban:

```csharp

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      //...
```

Ha csak át szeretné egy meghatározott készletének eseményeket küldeni egy másik erőforrás, beállíthat egy adott TelemetryClient a kulcs:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Egy új kulcs lekérése [hozzon létre egy új erőforrást az Application Insights portálon][new].



## <a name="applicationid-provider"></a>Alkalmazásazonosító-szolgáltató

_Rendelkezésre álló v2.6.0 kezdődően_

Ez a szolgáltató célja egy alkalmazás azonosítója alapján egy kialakítási kulcsot talált. Az Alkalmazásazonosítót a RequestTelemetry és DependencyTelemetry szerepel, és határozza meg a korrelációs a portálon.

Ez a lehetőség beállításával `TelemetryConfiguration.ApplicationIdProvider` kód vagy a config.

### <a name="interface-iapplicationidprovider"></a>Kapcsolat: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


A két megvalósításokhoz biztosítunk a [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) sdk: `ApplicationInsightsApplicationIdProvider` és `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Ez az a profil API burkolója. Kérelmek és a gyorsítótár eredményeket fogja forgalmának szabályozása.

Ez a szolgáltató hozzáadódik a konfigurációs fájlban vagy telepítésekor [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) vagy [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Ez az osztály tulajdonsága egy nem kötelező `ProfileQueryEndpoint`.
Alapértelmezés szerint a beállított érték `https://dc.services.visualstudio.com/api/profiles/{0}/appId`.
Ha ezt a konfigurációt a proxy konfigurálása van szüksége, javasoljuk, hogy proxyzás alap címet, és többek között "/api/profilok/{0}/appId". Vegye figyelembe, hogy "{0}" változó helyére kérelmenként futásidőben a kialakítási kulcsot.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Példa konfigurációs ApplicationInsights.config keresztül:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Kód példa konfiguráció:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

Ez egy statikus szolgáltató, amely a konfigurált Rendszerállapotkulcs fog támaszkodni a / alkalmazás azonosítója párokat.

Ez az osztály tulajdonsága `Defined`, ez a kialakítási kulcsot Dictionary < karakterlánc, karakterlánc > az Alkalmazásazonosítót párok.

Ez az osztály tulajdonsága egy nem kötelező `Next` konfigurálása egy másik szolgáltatót használja, ha egy kialakítási kulcs van szükség, amely nem létezik a konfigurációban is használható.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Példa konfigurációs ApplicationInsights.config keresztül:
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

#### <a name="example-configuration-via-code"></a>Kód példa konfiguráció:
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
[További információ az API-t][api].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
