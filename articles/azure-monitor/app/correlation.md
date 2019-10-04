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
ms.openlocfilehash: fe52fe51b347b232e03bad943906413b90c853c0
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338174"
---
# <a name="telemetry-correlation-in-application-insights"></a>Telemetria korreláció a Application Insightsban

A szolgáltatások világában minden logikai művelet végrehajtásához a szolgáltatás különböző összetevőinek kell működniük. Ezeket az összetevőket az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)külön is figyelheti. A webalkalmazás-összetevő a hitelesítési szolgáltató összetevővel kommunikál a felhasználói hitelesítő adatok érvényesítéséhez, valamint az API-összetevővel a vizualizációk adatainak lekérdezéséhez. Az API-összetevő más szolgáltatásokból származó adatok lekérdezésére és a gyorsítótár-szolgáltatói összetevők használatával értesíti az adott hívás számlázási összetevőjét. A Application Insights támogatja az elosztott telemetria korrelációt, amelyet annak észlelésére használ, hogy melyik összetevő felelős a hibákért vagy a teljesítmény romlásához.

Ez a cikk a Application Insights által a több összetevő által elküldett telemetria korrelációt okozó adatmodellt ismerteti. Magában foglalja a kontextus-propagálási technikákat és protokollokat. Emellett ismerteti a korrelációs fogalmak megvalósítását a különböző nyelveken és platformokon.

## <a name="data-model-for-telemetry-correlation"></a>Telemetria korrelációs adatmodell

Application Insights definiál egy [adatmodellt](../../azure-monitor/app/data-model.md) az elosztott telemetria korrelációhoz. A telemetria logikai művelettel való hozzárendeléséhez minden telemetria-elemhez tartozik egy `operation_Id` nevű környezeti mező. Ezt az azonosítót az elosztott nyomkövetés minden telemetria-eleme megosztja. Tehát még a telemetria egyetlen rétegből való elvesztése esetén is társíthat más összetevők által jelentett telemetria.

Az elosztott logikai műveletek jellemzően kisebb műveletekből állnak, amelyek az egyik összetevő által feldolgozott kérelmek. Ezeket a műveleteket a [kérelem telemetria](../../azure-monitor/app/data-model-request-telemetry.md)határozza meg. Minden kérelem telemetria rendelkezik a saját `id` azonosítóval, amely egyedileg és globálisan azonosítja azt. Továbbá az ehhez a kérelemhez társított összes telemetria-elemet (például a nyomkövetéseket és a kivételeket) a `operation_parentId` értékre kell állítani a kérelem `id`.

Minden kimenő művelet, például egy HTTP-hívás egy másik összetevőhöz, a [függőségi telemetria](../../azure-monitor/app/data-model-dependency-telemetry.md)szerint jelenik meg. A függőségi telemetria a globálisan egyedi `id` értéket is meghatározza. A függőségi hívás által kezdeményezett telemetria kérelme ezt a `id` `operation_parentId` értéket használja.

Az elosztott logikai művelet nézetét `operation_Id`, `operation_parentId` és `request.id` segítségével hozhatja létre `dependency.id` használatával. Ezek a mezők a telemetria-hívások oksági sorrendjét is meghatározzák.

A Service-környezetekben az összetevőkből származó nyomkövetési adatok különböző tárolóhelyekre léphetnek. Minden összetevő rendelkezhet saját kialakítási kulccsal Application Insightsban. A logikai művelet telemetria beszerzéséhez a Application Insights UX minden tárolóeszközről lekérdezi az összes elemet. Ha a tárolási elemek száma hatalmas, szüksége lesz egy tippre, ahol a következőt kell megkeresnie. A Application Insights adatmodell két mezőt határoz meg a probléma megoldásához: `request.source` és `dependency.target`. Az első mező azonosítja a függőségi kérelmet kezdeményező összetevőt, a második pedig azt, hogy melyik összetevő adta vissza a függőségi hívás válaszát.

## <a name="example"></a>Példa

Vegyük például a tőzsdei árak nevű alkalmazást, amely egy `Stock` nevű külső API használatával mutatja be a készlet aktuális piaci árát. A tőzsdei díjszabási alkalmazáshoz `Stock page` nevű oldal tartozik, amelyet az ügyfél webböngészője a `GET /Home/Stock` használatával nyit meg. Az alkalmazás lekérdezi a `Stock` API-t egy HTTP-hívás `GET /api/stock/value` használatával.

Az eredményül kapott telemetria a következő lekérdezés futtatásával elemezheti:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Az eredmények között vegye figyelembe, hogy az összes telemetria-elem megosztja a `operation_Id` gyökerét. Ha a lapon Ajax-hívás történik, akkor a függőségi telemetria új egyedi azonosító (`qJSXU`) lesz hozzárendelve, és az oldalmegtekintés azonosítója `operation_ParentId` lesz. A kiszolgálói kérelem ezután az Ajax ID-t használja `operation_ParentId` értékként.

| itemType   | name                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock lap                |              | STYz               | STYz         |
| függőség | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | Kezdőlap/készlet letöltése            | KqKwlrSt9PA= | qJSXU              | STYz         |
| függőség | /API/Stock/Value beolvasása      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Ha a Call `GET /api/stock/value` egy külső szolgáltatáshoz kapcsolódik, ismernie kell a kiszolgáló identitását, hogy megfelelően beállítsa a `dependency.target` mezőt. Ha a külső szolgáltatás nem támogatja a figyelést, a `target` értéke a szolgáltatás állomásneve (például `stock-prices-api.com`). Ha azonban a szolgáltatás egy előre definiált HTTP-fejléc visszaadásával azonosítja magát, a `target` tartalmazza a szolgáltatás identitását, amely lehetővé teszi, hogy a Application Insights elosztott nyomkövetést hozzon létre a szolgáltatásból származó telemetria lekérdezésével.

## <a name="correlation-headers"></a>Korrelációs fejlécek

A [W3C nyomkövetési kontextusra](https://w3c.github.io/trace-context/) áttérünk, amely az alábbiakat határozza meg:

- `traceparent`: A hívás globálisan egyedi műveleti AZONOSÍTÓját és egyedi azonosítóját is végrehajtja.
- `tracestate`: A rendszerspecifikus környezet nyomon követését végzi.

A Application Insights SDK-k legújabb verziói támogatják a nyomkövetési kontextus protokoll használatát, de előfordulhat, hogy a (ApplicationInsights SDK által támogatott régi korrelációs protokollal visszamenőleges kompatibilitást kell tartania).

A [korrelációs HTTP-protokoll más néven a Request-ID](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) az elavult útvonalon van. Ez a protokoll két fejlécet definiál:

- `Request-Id`: A hívás globálisan egyedi AZONOSÍTÓját is végrehajtja.
- `Correlation-Context`: Az elosztott nyomkövetési tulajdonságok név-érték párok összegyűjtését végzi.

A Application Insights a korrelációs HTTP protokoll [bővítményét](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) is meghatározza. @No__t-0 név-érték párokat használ a közvetlen hívó vagy a hívott által használt tulajdonságok gyűjteményének propagálásához. A Application Insights SDK ezt a fejlécet használja a `dependency.target` és a `request.source` mezők beállításához.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>A W3C elosztott nyomkövetési támogatásának engedélyezése klasszikus ASP.NET-alkalmazásokhoz
 
  > [!NOTE]
  > Nincs szükség konfigurációra a `Microsoft.ApplicationInsights.Web` és a `Microsoft.ApplicationInsights.DependencyCollector` értéktől kezdődően. 

A W3C nyomkövetési környezet támogatása a visszamenőlegesen kompatibilis módon történik, és a korreláció várhatóan az SDK korábbi verzióival (W3C-támogatás nélkül) kialakított alkalmazásokkal fog működni. 

Ha bármilyen okból szeretné megőrizni az örökölt @no__t – 0 protokollt, *letilthatja* a nyomkövetési környezetet a következő konfigurációval

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Ha az SDK régebbi verzióját futtatja, javasoljuk, hogy frissítse, vagy alkalmazza a következő konfigurációt a nyomkövetési környezet engedélyezéséhez.
Ez a funkció `Microsoft.ApplicationInsights.Web` és `Microsoft.ApplicationInsights.DependencyCollector` csomagokban érhető el a 2.8.0-béta verziótól kezdődően.
Alapértelmezés szerint le van tiltva. Ennek engedélyezéséhez módosítsa a `ApplicationInsights.config` értéket:

- A `RequestTrackingTelemetryModule` területen adja hozzá a `EnableW3CHeadersExtraction` elemet a következő értékkel: `true`.
- A `DependencyTrackingTelemetryModule` területen adja hozzá a `EnableW3CHeadersInjection` elemet a következő értékkel: `true`.
- Adja hozzá a `W3COperationCorrelationTelemetryInitializer` értéket a `TelemetryInitializers` alatt, a következőhöz hasonló: 

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers> 
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>A W3C elosztott nyomkövetési támogatásának engedélyezése ASP.NET Core alkalmazások számára

 > [!NOTE]
  > Nincs szükség konfigurációra a `Microsoft.ApplicationInsights.AspNetCore` verziótól kezdődően.
 
A W3C nyomkövetési környezet támogatása a visszamenőlegesen kompatibilis módon történik, és a korreláció várhatóan az SDK korábbi verzióival (W3C-támogatás nélkül) kialakított alkalmazásokkal fog működni. 

Ha bármilyen okból szeretné megőrizni az örökölt @no__t – 0 protokollt, *letilthatja* a nyomkövetési környezetet a következő konfigurációval

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Ha az SDK régebbi verzióját futtatja, javasoljuk, hogy frissítse, vagy alkalmazza a következő konfigurációt a nyomkövetési környezet engedélyezéséhez.

Ez a funkció `Microsoft.ApplicationInsights.AspNetCore`, 2.5.0-béta és `Microsoft.ApplicationInsights.DependencyCollector` verziójú 2.8.0-béta verzióban érhető el.
Alapértelmezés szerint le van tiltva. Az engedélyezéséhez állítsa a `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` értéket `true` értékre:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>A W3C elosztott nyomkövetési támogatásának engedélyezése Java-alkalmazásokhoz

- **Bejövő konfiguráció**

  - Java EE-alkalmazások esetén adja hozzá a következőt a ApplicationInsights. xml fájlban található `<TelemetryModules>` címkéhez:

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
  > A visszafelé kompatibilitási mód alapértelmezés szerint engedélyezve van, és a `enableW3CBackCompat` paraméter nem kötelező. Csak akkor használja, ha a visszafelé való kompatibilitást ki szeretné kapcsolni.
  >
  > Ideális esetben ezt ki kell kapcsolni, ha az összes szolgáltatás frissítve lett a W3C protokollt támogató SDK-k újabb verzióira. Javasoljuk, hogy a lehető leghamarabb váltson át ezekre az újabb SDK-kat.

> [!IMPORTANT]
> Győződjön meg arról, hogy a bejövő és a kimenő konfigurációk is pontosan azonosak.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>A W3C elosztott nyomkövetési támogatásának engedélyezése a Web Apps számára

Ez a funkció `Microsoft.ApplicationInsights.JavaScript`. Alapértelmezés szerint le van tiltva. Az engedélyezéshez használja a `distributedTracingMode` konfigurációt. A AI_AND_W3C a visszamenőleges kompatibilitást biztosít bármely örökölt Application Insights által biztosított szolgáltatással:

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

A [OpenTracing adatmodell-specifikációja](https://opentracing.io/) és Application Insights adatmodell-leképezése a következő módon történik:

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | @no__t – 0 `span.kind = server`                  |
| `Dependency`                          | @no__t – 0 `span.kind = client`                  |
| @no__t – 0 `Request` és `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | @no__t – 0 típusú `ChildOf` (a szülő span)   |

További információ: [Application Insights telemetria adatmodell](../../azure-monitor/app/data-model.md). 

A OpenTracing-fogalmak definícióit lásd: OpenTracing- [specifikáció](https://github.com/opentracing/specification/blob/master/specification.md) és [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-net"></a>Telemetria korreláció a .NET-ben

Az idő múlásával a .NET számos módszert definiált a telemetria és diagnosztikai naplók összekapcsolásához:

- a `System.Diagnostics.CorrelationManager` lehetővé teszi a [LogicalOperationStack és a tevékenységazonosító](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx)nyomon követését. 
- @no__t – 0 és Windows esemény-nyomkövetés (ETW) a [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) metódust adja meg.
- a `ILogger` [naplózási hatóköröket](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes)használ. 
- A Windows Communication Foundation (WCF) és a HTTP-átvitel "aktuális" kontextus-propagálást használ.

Ezek a módszerek azonban nem engedélyezték az automatikus elosztott nyomkövetés támogatását. a `DiagnosticSource` lehetővé teszik az automatikus gépek közötti korrelációt. A .NET-kódtárak támogatják a "DiagnosticSource", és lehetővé teszik a korrelációs környezet automatikus, a szállításon keresztüli propagálását, például a HTTP-t.

Az `DiagnosticSource` [tevékenységekre vonatkozó útmutató](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) ismerteti a tevékenységek nyomon követésének alapjait.

A ASP.NET Core 2,0 támogatja a HTTP-fejlécek kinyerését és új tevékenység indítását.

@no__t – 0, a 4.1.0 verziótól kezdődően pedig támogatja a korrelációs HTTP-fejlécek automatikus befecskendezését, és tevékenységként nyomon követi a HTTP-hívást.

Létezik egy új HTTP-modul, a [Microsoft. AspNet. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/)a klasszikus ASP.net. Ez a modul `DiagnosticSource` használatával valósítja meg a telemetria korrelációját. Elindít egy tevékenységet a bejövő kérelmek fejlécei alapján. Emellett a kérelmek feldolgozásának különböző szakaszaiból is összefügg a telemetria, még akkor is, ha a Internet Information Services (IIS) feldolgozásának minden fázisa egy másik felügyelt szálon fut.

A 2.4.0-béta verziótól kezdődően a Application Insights SDK a következőt használja: `DiagnosticSource` és `Activity` a telemetria gyűjtéséhez és az aktuális tevékenységhez való hozzárendeléséhez.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Telemetria korreláció a Java SDK-ban

A [Javához készült Application INSIGHTS SDK](../../azure-monitor/app/java-get-started.md) támogatja a telemetria-es verziótól kezdődően a 2.0.0 automatikus korrelációját. Automatikusan feltölti a `operation_id` értéket a kérelem hatókörében kiadott összes telemetria (például a Nyomkövetések, a kivételek és az egyéni események) számára. Emellett gondoskodik a szolgáltatások közötti hívásokról HTTP-n keresztül történő, a korrelációs fejlécek propagálásáról (lásd fentebb), ha a [Java SDK-ügynök](../../azure-monitor/app/java-agent.md) konfigurálva van.

> [!NOTE]
> A korrelációs szolgáltatás csak az Apache HTTPClient-on keresztül kezdeményezett hívásokat támogatja. Ha Spring RestTemplate-t vagy kitalál-t használ, mindkettőt használhatja az Apache HTTPClient a motorháztető alatt.

Az üzenetkezelési technológiák (például a Kafka, a RabbitMQ vagy a Azure Service Bus) közötti automatikus környezet-propagálás jelenleg nem támogatott. Az ilyen forgatókönyvek azonban a `trackDependency` és a `trackRequest` API-k használatával manuálisan is megoldhatók. Ezekben az API-kban a függőségi telemetria egy gyártó által várólistán lévő üzenetet jelöl, és a kérelem a fogyasztó által feldolgozott üzenetet jelöli. Ebben az esetben a `operation_id` és a `operation_parentId` értéket is propagálni kell az üzenet tulajdonságai között.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>Telemetria korreláció az aszinkron Java-alkalmazásokban

A telemetria az aszinkron rugós rendszerindítási alkalmazásban való összekapcsolásához kövesse [ezt](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) a részletes cikket. Útmutatást nyújt a Spring [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) és a [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html)kialakításához. 


<a name="java-role-name"></a>
## <a name="role-name"></a>Szerepkörnév

Időnként előfordulhat, hogy testre szeretné szabni, hogy az összetevők nevei hogyan jelenjenek meg az [alkalmazás-hozzárendelésben](../../azure-monitor/app/app-map.md). Ehhez manuálisan állíthatja be a `cloud_RoleName` értéket a következők egyikével:

- Ha a Spring Boott a Application Insights Spring boot Starter használatával használja, az egyetlen szükséges módosítás az alkalmazás egyéni nevének beállítása az Application. properties fájlban.

  `spring.application.name=<name-of-app>`

  A Spring boot Starter automatikusan hozzárendeli a `cloudRoleName` értéket a `spring.application.name` tulajdonsághoz megadott értékhez.

- Ha a `WebRequestTrackingFilter` értéket használja, a `WebAppNameContextInitializer` automatikusan beállítja az alkalmazás nevét. Adja hozzá a következőt a konfigurációs fájlhoz (ApplicationInsights. xml):

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
- A ASP.NET Core-és ASP.NET kapcsolatos speciális korrelációs forgatókönyvek esetében tekintse meg az [Egyéni műveletek nyomon követése](custom-operations-tracking.md) című cikket.
- További információ a cloud_RoleName más SDK-k számára történő [beállításáról](../../azure-monitor/app/app-map.md#set-cloud-role-name) .
- A Application Insights összes összetevőjének bevezetését. Tekintse meg a [támogatott platformokat](../../azure-monitor/app/platforms.md).
- Tekintse meg Application Insights típusok [adatmodelljét](../../azure-monitor/app/data-model.md) .
- Ismerje meg [, hogyan bővítheti és szűrheti a telemetria](../../azure-monitor/app/api-filtering-sampling.md).
- Tekintse át a [Application Insights konfigurációs referenciát](configuration-with-applicationinsights-config.md).
