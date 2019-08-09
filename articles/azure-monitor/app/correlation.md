---
title: Az Azure Application Insights telemetria korrelációja | Microsoft Docs
description: Application Insights telemetria korreláció
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: 1c6a0ce3e4e8d098d2bc048a331b0ae0cb5c6b13
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881389"
---
# <a name="telemetry-correlation-in-application-insights"></a>Telemetria korreláció a Application Insightsban

A szolgáltatások világában minden logikai művelet végrehajtásához a szolgáltatás különböző összetevőinek kell működniük. Ezeket az összetevőket az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)külön is figyelheti. A webalkalmazás-összetevő a hitelesítési szolgáltató összetevővel kommunikál a felhasználói hitelesítő adatok érvényesítéséhez, valamint az API-összetevővel a vizualizációk adatainak lekérdezéséhez. Az API-összetevő más szolgáltatásokból származó adatok lekérdezésére és a gyorsítótár-szolgáltatói összetevők használatával értesíti az adott hívás számlázási összetevőjét. A Application Insights támogatja az elosztott telemetria korrelációt, amelyet annak észlelésére használ, hogy melyik összetevő felelős a hibákért vagy a teljesítmény romlásához.

Ez a cikk a Application Insights által a több összetevő által elküldett telemetria korrelációt okozó adatmodellt ismerteti. Magában foglalja a kontextus-propagálási technikákat és protokollokat. Emellett ismerteti a korrelációs fogalmak megvalósítását a különböző nyelveken és platformokon.

## <a name="data-model-for-telemetry-correlation"></a>Telemetria korrelációs adatmodell

Application Insights definiál egy [](../../azure-monitor/app/data-model.md) adatmodellt az elosztott telemetria korrelációhoz. A telemetria logikai művelettel való hozzárendeléséhez minden telemetria elemhez tartozik egy nevű `operation_Id`környezeti mező. Ezt az azonosítót az elosztott nyomkövetés minden telemetria-eleme megosztja. Tehát még a telemetria egyetlen rétegből való elvesztése esetén is társíthat más összetevők által jelentett telemetria.

Az elosztott logikai műveletek jellemzően kisebb műveletekből állnak, amelyek az egyik összetevő által feldolgozott kérelmek. Ezeket a műveleteket a [kérelem telemetria](../../azure-monitor/app/data-model-request-telemetry.md)határozza meg. Minden kérelem telemetria rendelkezik `id` , amely egyedileg és globálisan azonosítja azt. Továbbá az ehhez a kérelemhez társított összes telemetria-elemet (például a nyomkövetéseket és a kivételeket) `operation_parentId` a kérelem `id`értékére kell állítani.

Minden kimenő művelet, például egy HTTP-hívás egy másik összetevőhöz, a [függőségi telemetria](../../azure-monitor/app/data-model-dependency-telemetry.md)szerint jelenik meg. A függőségi telemetria a saját `id` globálisan egyedi állapotát is meghatározza. A függőségi hívás által kezdeményezett telemetria kérelme ezt `id` `operation_parentId`használja.

Az elosztott logikai művelet `operation_Id`nézetét a, `operation_parentId`a és `request.id` a `dependency.id`használatával is létrehozhatja. Ezek a mezők a telemetria-hívások oksági sorrendjét is meghatározzák.

A Service-környezetekben az összetevőkből származó nyomkövetési adatok különböző tárolóhelyekre léphetnek. Minden összetevő rendelkezhet saját kialakítási kulccsal Application Insightsban. A logikai művelet telemetria beszerzéséhez a Application Insights UX minden tárolóeszközről lekérdezi az összes elemet. Ha a tárolási elemek száma hatalmas, szüksége lesz egy tippre, ahol a következőt kell megkeresnie. A Application Insights adatmodell két mezőt definiál a probléma megoldásához: `request.source` és `dependency.target`. Az első mező azonosítja a függőségi kérelmet kezdeményező összetevőt, a második pedig azt, hogy melyik összetevő adta vissza a függőségi hívás válaszát.

## <a name="example"></a>Példa

Vegyük például a részvényárfolyamok nevű alkalmazást, amely egy készlet aktuális piaci árát mutatja egy külső API `Stock`használatával. A tőzsdei díjszabási alkalmazáshoz egy `Stock page` lap jelenik meg, amelyet az ügyfél webböngészője nyit meg a használatával. `GET /Home/Stock` Az alkalmazás http- `Stock` hívás `GET /api/stock/value`használatával kérdezi le az API-t.

Az eredményül kapott telemetria a következő lekérdezés futtatásával elemezheti:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Az eredmények között vegye figyelembe, hogy az összes telemetria-elem `operation_Id`megosztja a gyökeret. Ha egy AJAX-hívást végeznek az oldalról, a rendszer új egyedi`qJSXU`azonosítót () rendel a függőségi telemetria, és az oldalmegtekintés azonosítóját `operation_ParentId`használja. A kiszolgálói kérelem ezután az Ajax ID-t `operation_ParentId`használja.

| itemType   | name                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock lap                |              | STYz               | STYz         |
| függőség | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | Kezdőlap/készlet letöltése            | KqKwlrSt9PA= | qJSXU              | STYz         |
| függőség | /API/Stock/Value beolvasása      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Ha a hívás `GET /api/stock/value` külső szolgáltatásra történik, ismernie kell az adott kiszolgáló identitását, hogy megfelelően be tudja állítani a `dependency.target` mezőt. Ha a külső szolgáltatás nem támogatja a figyelést, `target` a a szolgáltatás állomásneve ( `stock-prices-api.com`például) értékre van állítva. Ha azonban a szolgáltatás egy előre meghatározott HTTP-fejléc visszaadásával azonosítja `target` magát, az tartalmazza azt a szolgáltatás-identitást, amely lehetővé teszi, hogy a Application Insights elosztott nyomkövetést hozzon létre a szolgáltatásból származó telemetria lekérdezésével.

## <a name="correlation-headers"></a>Korrelációs fejlécek

A [korrelációs http protokollra](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)vonatkozó RFC-javaslaton dolgozunk. Ez a javaslat két fejlécet határoz meg:

- `Request-Id`: A hívás globálisan egyedi AZONOSÍTÓját is végrehajtja.
- `Correlation-Context`: Az elosztott nyomkövetési tulajdonságok név-érték párok összegyűjtését végzi.

A standard a következő két sémát is `Request-Id` meghatározza a létrehozáshoz: Flat és hierarchikus. Az egyszerű sémával jól ismert `Id` kulcs van definiálva a `Correlation-Context` gyűjteményhez.

Application Insights a korrelációs HTTP protokoll [bővítményét](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) határozza meg. Név- `Request-Context` érték párokat használ a közvetlen hívó vagy a hívott által használt tulajdonságok gyűjteményének propagálásához. A Application Insights SDK ezt a fejlécet használja `dependency.target` a `request.source` beállításhoz és a mezőkhöz.

### <a name="w3c-distributed-tracing"></a>W3C elosztott nyomkövetés

Áttérünk a [W3C elosztott nyomkövetési formátumára](https://w3c.github.io/trace-context/). Ez a következőket határozza meg:

- `traceparent`: A hívás globálisan egyedi műveleti AZONOSÍTÓját és egyedi azonosítóját is végrehajtja.
- `tracestate`: A rendszerspecifikus környezet nyomon követését végzi.

#### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>A W3C elosztott nyomkövetési támogatásának engedélyezése klasszikus ASP.NET-alkalmazásokhoz

Ez a funkció a és `Microsoft.ApplicationInsights.Web` `Microsoft.ApplicationInsights.DependencyCollector` a csomagokban érhető el a 2.8.0-béta verziótól kezdődően.
Alapértelmezés szerint le van tiltva. Az engedélyezéséhez módosítsa `ApplicationInsights.config`a következőt:

- A `RequestTrackingTelemetryModule`területen adja hozzá `EnableW3CHeadersExtraction` az `true`elemet a következő értékkel:.
- A `DependencyTrackingTelemetryModule`területen adja hozzá `EnableW3CHeadersInjection` az `true`elemet a következő értékkel:.
- Hozzáadás `W3COperationCorrelationTelemetryInitializer` a`TelemetryInitializers` következőhöz hasonló 

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers> 
```

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>A W3C elosztott nyomkövetési támogatásának engedélyezése ASP.NET Core alkalmazások számára

Ez a szolgáltatás a `Microsoft.ApplicationInsights.AspNetCore` 2.5.0-béta és a `Microsoft.ApplicationInsights.DependencyCollector` 2.8.0-bétaverzió verzióban érhető el.
Alapértelmezés szerint le van tiltva. Az engedélyezéséhez állítsa `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` a `true`következőre:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

#### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>A W3C elosztott nyomkövetési támogatásának engedélyezése Java-alkalmazásokhoz

- **Bejövő konfiguráció**

  - Java EE-alkalmazások esetén adja hozzá a következőt `<TelemetryModules>` a címkéhez a ApplicationInsights. xml fájlban:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
  - A Spring boot-alkalmazások esetében adja hozzá a következő tulajdonságokat:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Kimenő konfiguráció**

  Adja hozzá a következőt a AI-Agent. xml fájlhoz:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > A visszafelé kompatibilitási mód alapértelmezés szerint engedélyezve van, és `enableW3CBackCompat` a paraméter nem kötelező. Csak akkor használja, ha a visszafelé való kompatibilitást ki szeretné kapcsolni.
  >
  > Ideális esetben ezt ki kell kapcsolni, ha az összes szolgáltatás frissítve lett a W3C protokollt támogató SDK-k újabb verzióira. Javasoljuk, hogy a lehető leghamarabb váltson át ezekre az újabb SDK-kat.

> [!IMPORTANT]
> Győződjön meg arról, hogy a bejövő és a kimenő konfigurációk is pontosan azonosak.

#### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>A W3C elosztott nyomkövetési támogatásának engedélyezése a Web Apps számára

Ez a szolgáltatás a `Microsoft.ApplicationInsights.JavaScript`-ben érhető el. Alapértelmezés szerint le van tiltva. Az engedélyezéséhez használja `distributedTracingMode` a config parancsot. A AI_AND_W3C a visszamenőleges kompatibilitást biztosít bármely örökölt Application Insights által biztosított szolgáltatással:

- **NPM telepítője (Mellőzés a kódrészletek beállításakor)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...Other Configuration Options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Kódrészlet beállítása (figyelmen kívül hagyva, ha a NPM-telepítőt használja)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...Other Configuration Options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing és Application Insights

A [OpenTracing adatmodell](https://opentracing.io/) -specifikációja és Application Insights adatmodell-leképezése a következő módon történik:

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span`a`span.kind = server`                  |
| `Dependency`                          | `Span`a`span.kind = client`                  |
| `Id``Request` és`Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference`típus `ChildOf` (a szülő span)   |

További információ: [Application Insights telemetria adatmodell](../../azure-monitor/app/data-model.md). 

A OpenTracing-fogalmak definícióit lásd: OpenTracing- [specifikáció](https://github.com/opentracing/specification/blob/master/specification.md) és [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-net"></a>Telemetria korreláció a .NET-ben

Az idő múlásával a .NET számos módszert definiált a telemetria és diagnosztikai naplók összekapcsolásához:

- `System.Diagnostics.CorrelationManager`lehetővé teszi a [LogicalOperationStack és a tevékenységazonosító](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx)nyomon követését. 
- `System.Diagnostics.Tracing.EventSource`és Windows esemény-nyomkövetés (ETW) határozza meg a [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) metódust.
- `ILogger`a [naplózási hatóköröket](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes)használja. 
- A Windows Communication Foundation (WCF) és a HTTP-átvitel "aktuális" kontextus-propagálást használ.

Ezek a módszerek azonban nem engedélyezték az automatikus elosztott nyomkövetés támogatását. `DiagnosticSource`lehetővé teszik az automatikus gépek közötti korrelációt. A .NET-kódtárak támogatják a "DiagnosticSource", és lehetővé teszik a korrelációs környezet automatikus, a szállításon keresztüli propagálását, például a HTTP-t.

A [tevékenységek](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) `DiagnosticSource` útmutatója ismerteti a tevékenységek követésének alapjait.

A ASP.NET Core 2,0 támogatja a HTTP-fejlécek kinyerését és új tevékenység indítását.

`System.Net.HttpClient`a 4.1.0 verziótól kezdődően a a korrelációs HTTP-fejlécek automatikus befecskendezését és a HTTP-hívás tevékenységként való nyomon követését támogatja.

Létezik egy új HTTP-modul, a [Microsoft. AspNet. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/)a klasszikus ASP.net. Ez a modul telemetria korrelációt valósít meg `DiagnosticSource`a használatával. Elindít egy tevékenységet a bejövő kérelmek fejlécei alapján. Emellett a kérelmek feldolgozásának különböző szakaszaiból is összefügg a telemetria, még akkor is, ha a Internet Information Services (IIS) feldolgozásának minden fázisa egy másik felügyelt szálon fut.

Az Application Insights SDK, a 2.4.0-béta verziótól kezdődően `DiagnosticSource` , `Activity` a telemetria és az aktuális tevékenységhez társítja az adatokat.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Telemetria korreláció a Java SDK-ban

A [Javához készült Application INSIGHTS SDK](../../azure-monitor/app/java-get-started.md) támogatja a telemetria-es verziótól kezdődően a 2.0.0 automatikus korrelációját. A `operation_id` szolgáltatás automatikusan kitölti az összes telemetria (például a nyomkövetéseket, a kivételeket és az egyéni eseményeket) a kérelem hatókörén belül. Emellett gondoskodik a szolgáltatások közötti hívásokról HTTP-n keresztül történő, a korrelációs fejlécek propagálásáról (lásd fentebb), ha a [Java SDK-ügynök](../../azure-monitor/app/java-agent.md) konfigurálva van.

> [!NOTE]
> A korrelációs szolgáltatás csak az Apache HTTPClient-on keresztül kezdeményezett hívásokat támogatja. Ha Spring RestTemplate-t vagy kitalál-t használ, mindkettőt használhatja az Apache HTTPClient a motorháztető alatt.

Az üzenetkezelési technológiák (például a Kafka, a RabbitMQ vagy a Azure Service Bus) közötti automatikus környezet-propagálás jelenleg nem támogatott. Előfordulhat azonban, hogy az ilyen forgatókönyveket manuálisan, a és az `trackDependency` API `trackRequest` -k használatával is felhasználhatja. Ezekben az API-kban a függőségi telemetria egy gyártó által várólistán lévő üzenetet jelöl, és a kérelem a fogyasztó által feldolgozott üzenetet jelöli. Ebben az esetben mindkettőt `operation_id` `operation_parentId` propagálni kell az üzenet tulajdonságai között.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>Telemetria korreláció az aszinkron Java-alkalmazásokban

A telemetria az aszinkron rugós rendszerindítási alkalmazásban való összekapcsolásához kövesse [ezt](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) a részletes cikket. Útmutatást nyújt a Spring [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) és a [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html)kialakításához. 


<a name="java-role-name"></a>
## <a name="role-name"></a>Szerepkörnév

Időnként előfordulhat, hogy testre szeretné szabni, hogy az összetevők nevei hogyan jelenjenek meg az [alkalmazás](../../azure-monitor/app/app-map.md)-hozzárendelésben. Ehhez manuálisan is beállíthatja a `cloud_RoleName` t a következők valamelyikével:

- Ha a Spring Boott a Application Insights Spring boot Starter használatával használja, az egyetlen szükséges módosítás az alkalmazás egyéni nevének beállítása az Application. properties fájlban.

  `spring.application.name=<name-of-app>`

  A Spring boot Starter automatikusan hozzárendeli `cloudRoleName` a `spring.application.name` tulajdonsághoz megadott értéket.

- Ha a `WebRequestTrackingFilter`t használja, akkor a `WebAppNameContextInitializer` automatikusan beállítja az alkalmazás nevét. Adja hozzá a következőt a konfigurációs fájlhoz (ApplicationInsights. xml):

  ```XML
  <ContextInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
  </ContextInitializers>
  ```

- Ha a felhő környezeti osztályát használja:

  ```Java
  telemetryClient.getContext().getCloud().setRole("My Component Name");
  ```

## <a name="next-steps"></a>További lépések

- [Egyéni telemetria](../../azure-monitor/app/api-custom-events-metrics.md)írása.
- További információ a [cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) más SDK-k számára történő beállításáról.
- A Application Insights összes összetevőjének bevezetését. Tekintse meg a [támogatott platformokat](../../azure-monitor/app/platforms.md).
- Tekintse [](../../azure-monitor/app/data-model.md) meg Application Insights típusok adatmodelljét.
- Ismerje meg [, hogyan bővítheti és szűrheti a telemetria](../../azure-monitor/app/api-filtering-sampling.md).
- Tekintse át a [Application Insights konfigurációs referenciát](configuration-with-applicationinsights-config.md).
