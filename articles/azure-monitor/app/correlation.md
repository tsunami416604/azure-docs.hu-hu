---
title: Azure Application Insights telemetriai korreláció | Microsoft dokumentumok
description: Az Application Insights telemetriai korrelációja
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 2e862410e2bf12e09e1a6388bbb6f7105b5b2edf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405267"
---
# <a name="telemetry-correlation-in-application-insights"></a>Telemetriai korreláció az Application Insightsban

A mikroszolgáltatások világában minden logikai művelethez a szolgáltatás különböző összetevőiben kell elvégezni a munkát. Ezen összetevők mindegyikét külön-külön is figyelheti az [Application Insights](../../azure-monitor/app/app-insights-overview.md)használatával. Az Application Insights támogatja az elosztott telemetriai korrelációt, amely segítségével észleli, hogy melyik összetevő felelős a hibákért vagy a teljesítménycsökkenésért.

Ez a cikk ismerteti az Application Insights által több összetevő által küldött telemetriai adatok korrelációját ismerteti. Ez magában foglalja a környezet-propagálás technikák és protokollok. Ugyancsak kiterjed a korrelációs taktikák különböző nyelveken és platformokon történő végrehajtására.

## <a name="data-model-for-telemetry-correlation"></a>Adatmodell telemetriai korrelációhoz

Az Application Insights egy [adatmodellt](../../azure-monitor/app/data-model.md) határoz meg az elosztott telemetriai korrelációhoz. A telemetria logikai művelethez való társításához minden `operation_Id`telemetriai elemhez van egy környezetmező, amelynek neve . Ezt az azonosítót az elosztott nyomkövetés minden telemetriai eleme megosztja. Így még akkor is, ha elveszíti a telemetriai adatokat egy rétegről, továbbra is társíthatja a más összetevők által jelentett telemetriai adatokat.

Az elosztott logikai művelet általában kisebb műveletekből áll, amelyeket az egyik összetevő dolgoz fel. Ezeket a műveleteket [a kérelemtelemetria](../../azure-monitor/app/data-model-request-telemetry.md)határozza meg. Minden kérelem telemetriai `id` elem saját, amely azonosítja azt egyedileg és globálisan. És a kérelemhez társított összes telemetriai elemnek (például nyomkövetéseknek és `id`kivételeknek) a kérelem értékére kell beállítania. `operation_parentId`

Minden kimenő műveletet, például egy másik összetevőHTTP-hívását [függőségi telemetriai adatok](../../azure-monitor/app/data-model-dependency-telemetry.md)jelölik. Függőségi telemetriai adatok `id` is meghatározza a saját, amely globálisan egyedi. A függőségi hívás által kezdeményezett kérelemtelemetria `id` ezt `operation_parentId`használja a .

Az elosztott logikai művelet nézetét `operation_Id`a `operation_parentId`használatával `request.id` `dependency.id`, a használatával és a használatával hozhatlétre létre. Ezek a mezők is meghatározzák a telemetriai hívások ok-okozati sorrendjét.

Mikroszolgáltatási környezetben az összetevők nyomkövetései különböző tárolóelemekhez vezethetnek. Minden összetevő rendelkezhet saját instrumentation kulccsal az Application Insightsban. A logikai művelet telemetriai adatainak lekérnie minden tárolási elem adatokat. Ha a tárolóelemek száma nagy, szüksége lesz egy tippre, hogy hol keresse meg legközelebb. Az Application Insights adatmodell két mezőt `request.source` határoz `dependency.target`meg a probléma megoldásához: és . Az első mező azonosítja a függőségi kérelmet kezdeményező összetevőt. A második mező azonosítja, hogy melyik összetevő adta vissza a függőségi hívás válaszát.

## <a name="example"></a>Példa

Lássunk erre egy példát. A Stock Prices nevű alkalmazás egy részvény aktuális piaci árát mutatja egy Stock nevű külső API használatával. A Tőzsdei árak alkalmazás egy oldal hívott Stock oldal, `GET /Home/Stock`hogy az ügyfél böngésző megnyitja segítségével. Az alkalmazás a HTTP-hívás használatával `GET /api/stock/value`kérdezi le a Stock API-t.

Az eredményül kapott telemetriai adatokat egy lekérdezés futtatásával elemezheti:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Az eredmények ben vegye figyelembe, hogy az `operation_Id`összes telemetriai elem osztozik a gyökér . Amikor az oldalról Ajax-hívás történik, a függőségi telemetriai adatokhoz új egyedi azonosítót (`qJSXU`) rendela `operation_ParentId`rendszer, és a pageView azonosítóját használja a rendszer. A szerver kérelem, majd használja `operation_ParentId`az Ajax ID a .

| Itemtype   | név                      | ID (Azonosító)           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| lapnézet   | Stock oldal                |              | STYz között               | STYz között         |
| Függőség | GET /Home/Raktár           | qJSXU        | STYz között               | STYz között         |
| Kérés    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz között         |
| Függőség | GET /api/készlet/érték      | bBrf2L7mm2g = | KqKwlrSt9PA=       | STYz között         |

Amikor a `GET /api/stock/value` hívás egy külső szolgáltatáshoz történik, ismernie kell a kiszolgáló `dependency.target` identitását, hogy megfelelően állíthassa be a mezőt. Ha a külső szolgáltatás nem `target` támogatja a figyelést, a szolgáltatás `stock-prices-api.com`állomásnevére van beállítva (például ). De ha a szolgáltatás azonosítja magát egy előre `target` definiált HTTP-fejléc visszaadásával, tartalmazza a szolgáltatás identitását, amely lehetővé teszi az Application Insights számára, hogy egy elosztott nyomkövetést hozzon létre az adott szolgáltatástelemetria lekérdezésével.

## <a name="correlation-headers"></a>Korrelációs fejlécek

Az Application Insights áttér a [W3C trace-context-re,](https://w3c.github.io/trace-context/)amely a következőket határozza meg:

- `traceparent`: A hívás globálisan egyedi műveletazonosítóját és egyedi azonosítóját hordozza.
- `tracestate`: Rendszerspecifikus nyomkövetési környezetet hordoz.

Az Application Insights SDK legújabb verziója támogatja a Trace-Context protokollt, de előfordulhat, hogy engedélyeznie kell azt. (Az Application Insights SDK által támogatott korábbi korrelációs protokollal való visszamenőleges kompatibilitás megmarad.)

A [korrelációs HTTP protokoll, más néven Request-Id](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), elavult. Ez a protokoll két fejlécet határoz meg:

- `Request-Id`: A hívás globálisan egyedi azonosítóját hordozza.
- `Correlation-Context`: Az elosztott nyomkövetési tulajdonságok név-érték párok gyűjteményét hordozza.

Az Application Insights is meghatározza a korrelációs HTTP protokoll [kiterjesztését.](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) Név-érték párokat használ `Request-Context` a közvetlen hívó vagy a hívó által használt tulajdonságok gyűjteményének propagálásához. Az Application Insights SDK ezt `dependency.target` a `request.source` fejlécet használja a mezők és mezők beállításához.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>A W3C elosztott nyomkövetési támogatásának engedélyezése klasszikus ASP.NET alkalmazásokhoz
 
  > [!NOTE]
  >  `Microsoft.ApplicationInsights.Web` Kezdve, `Microsoft.ApplicationInsights.DependencyCollector`és , nincs szükség konfigurációra.

A W3C Trace-Context támogatása visszafelé kompatibilis módon történik. A korreláció várhatóan működni fog az SDK korábbi verzióival (W3C támogatás nélkül) instrumentált alkalmazásokkal.

Ha továbbra is használni `Request-Id` szeretné az örökölt protokollt, ezzel a konfigurációval letilthatja a Trace-Context protokollt:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Ha az SDK egy régebbi verzióját futtatja, javasoljuk, hogy frissítse azt, vagy alkalmazza a következő konfigurációt a Trace-Context engedélyezéséhez.
Ez a funkció `Microsoft.ApplicationInsights.Web` a `Microsoft.ApplicationInsights.DependencyCollector` 2.8.0-beta1 verziótól kezdve érhető el a és a csomagokban.
Alapértelmezés szerint le van tiltva. Az engedélyezéshez hajtsa végre a következő módosításokat: `ApplicationInsights.config`

- A `RequestTrackingTelemetryModule`területen `EnableW3CHeadersExtraction` adja hozzá az `true`elemet, és állítsa az értékét a értékére.
- A `DependencyTrackingTelemetryModule`területen `EnableW3CHeadersInjection` adja hozzá az `true`elemet, és állítsa az értékét a értékére.
- `W3COperationCorrelationTelemetryInitializer` Hozzáadás `TelemetryInitializers`a csoportban. Úgy fog kinézni, hasonló erre a példára:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>A W3C által terjesztett nyomkövetési támogatás engedélyezése ASP.NET Core alkalmazásokhoz

 > [!NOTE]
  > A `Microsoft.ApplicationInsights.AspNetCore` 2.8.0-s verziótól kezdve nincs szükség konfigurációra.
 
A W3C Trace-Context támogatása visszafelé kompatibilis módon történik. A korreláció várhatóan működni fog az SDK korábbi verzióival (W3C támogatás nélkül) instrumentált alkalmazásokkal.

Ha továbbra is használni `Request-Id` szeretné az örökölt protokollt, ezzel a konfigurációval letilthatja a Trace-Context protokollt:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Ha az SDK egy régebbi verzióját futtatja, javasoljuk, hogy frissítse azt, vagy alkalmazza a következő konfigurációt a Trace-Context engedélyezéséhez.

Ez a `Microsoft.ApplicationInsights.AspNetCore` funkció a 2.5.0-beta1 verzióban és a 2.8.0-beta1 verzióban `Microsoft.ApplicationInsights.DependencyCollector` található.
Alapértelmezés szerint le van tiltva. Az engedélyezéshez `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` `true`állítsa a következőt:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>A W3C elosztott nyomkövetési támogatásának engedélyezése Java-alkalmazásokhoz

#### <a name="java-30-agent"></a>Java 3.0 ügynök

  A Java 3.0 ügynök támogatja a W3C-t a dobozból, és nincs szükség további konfigurációra. 

#### <a name="java-sdk"></a>Java SDK
- **Bejövő konfiguráció**

  - Java EE-alkalmazások esetén adja `<TelemetryModules>` hozzá a következőket az ApplicationInsights.xml fájl címkéjéhez:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - A Tavaszi rendszerindítási alkalmazások esetén adja hozzá az alábbi tulajdonságokat:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Kimenő konfiguráció**

  Adja hozzá az alábbiakat az AI-Agent.xml fájlhoz:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > A visszamenőleges kompatibilitási mód alapértelmezés `enableW3CBackCompat` szerint engedélyezve van, és a paraméter megadása nem kötelező. Csak akkor használja, ha ki szeretné kapcsolni a visszamenőleges kompatibilitást.
  >
  > Ideális esetben ezt akkor kapcsolja ki, ha az összes szolgáltatás frissítve lett a W3C protokollt támogató SDK-k újabb verzióira. Javasoljuk, hogy a lehető leghamarabb helyezze át ezeket az újabb SDK-kat.

> [!IMPORTANT]
> Győződjön meg arról, hogy a bejövő és a kimenő konfigurációk pontosan azonosak.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Webalkalmazások W3C-alapú elosztott nyomkövetési támogatásának engedélyezése

Ez a `Microsoft.ApplicationInsights.JavaScript`funkció a . Alapértelmezés szerint le van tiltva. Az engedélyezéshez `distributedTracingMode` használja a konfigurációt. AI_AND_W3C az Application Insights által instrumentált örökölt szolgáltatásokkal való visszamenőleges kompatibilitásérdekében érhető el.

- **npm beállítás (figyelmen kívül hagyás, ha a kódrészlet beállítását használja)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Kódrészlet beállítása (figyelmen kívül hagyás, ha npm beállítást használ)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...other configuration options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing és Application Insights

Az [OpenTracing adatmodell specifikációja](https://opentracing.io/) és az Application Insights adatmodellek a következőképpen vannak leképezve:

| Application Insights                   | OpenTracing (OpenTracing)                                        |
|------------------------------------    |-------------------------------------------------    |
| `Request`, `PageView`                  | `Span`a`span.kind = server`                    |
| `Dependency`                           | `Span`a`span.kind = client`                    |
| `Id`a `Request` és a`Dependency`     | `SpanId`                                            |
| `Operation_Id`                         | `TraceId`                                           |
| `Operation_ParentId`                   | `Reference`típusú `ChildOf` (a szülő span)     |

További információ: [Application Insights telemetriai adatmodell.](../../azure-monitor/app/data-model.md)

Az OpenTracing fogalmak meghatározását lásd az OpenTracing [specifikációban](https://github.com/opentracing/specification/blob/master/specification.md) és [a szemantikai konvenciókban.](https://github.com/opentracing/specification/blob/master/semantic_conventions.md)

## <a name="telemetry-correlation-in-opencensus-python"></a>Telemetriai korreláció az OpenCensus Pythonban

OpenCensus Python követi `OpenTracing` az adatmodell specifikációk korábban vázolt. Támogatja a [W3C Trace-Context-t](https://w3c.github.io/trace-context/) is anélkül, hogy bármilyen konfigurációt igényelne.

### <a name="incoming-request-correlation"></a>Bejövő kérelem korrelációja

OpenCensus Python korrelálja W3C Trace-Context fejlécek a bejövő kérelmek a spans, amelyek maguk a kérelmek ből generált. OpenCensus ezt automatikusan integrációk ezen népszerű webes alkalmazás keretek: Flask, Django, és piramis. Csak meg kell, hogy feltölti a W3C Trace-Context fejlécek a [megfelelő formátumban,](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) és küldje el őket a kérést. Itt egy minta Flask alkalmazás, amely bemutatja ezt:

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Ez a kód fut egy minta Flask `8080`alkalmazás a helyi gépen, hallgatva port . A nyomkövetési környezet korrelációhoz kérést kell küldenie a végpontnak. Ebben a példában a `curl` következő parancsokat használhatja:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
A [Trace-Context fejlécformátumból](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)a következő információkat vezetheti:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Ha megnézi az Azure Monitornak küldött kérelembejegyzést, láthatja a nyomkövetési fejlécadatokkal feltöltött mezőket. Ezeket az adatokat a Naplók (Analytics) az Azure Monitor Application Insights erőforrásban található.

![Telemetriakérése a naplókban (Analytics)](./media/opencensus-python/0011-correlation.png)

A `id` mező formátuma, `<trace-id>.<span-id>`ahol `trace-id` a program a kérelemben átadott nyomkövetési fejlécből származik, és a `span-id` program egy generált 8 bájtos tömb ehhez a tartományhoz.

A `operation_ParentId` mező formátuma, `<trace-id>.<parent-id>`ahol `trace-id` mind `parent-id` a, mind a program a kérelemben átadott nyomkövetési fejlécből származik.

### <a name="log-correlation"></a>Napló-összefüggések

OpenCensus Python lehetővé teszi, hogy korrelál naplók hozzáadásával nyomkövetési azonosító, egy span-azonosító, és a mintavételi jelző rekordok naplózásához. Ezeket az attribútumokat az OpenCensus [naplózási integráció](https://pypi.org/project/opencensus-ext-logging/)telepítésével adja hozzá. A `LogRecord` python-objektumokhoz a következő `traceId` `spanId`attribútumok `traceSampled`lesznek hozzáadva: , , és . Vegye figyelembe, hogy ez csak az integráció után létrehozott naplózók esetében lép érvénybe.

Íme egy mintaalkalmazás, amely bemutatja ezt:

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
Amikor ez a kód fut, a következő nyomatok jelennek meg a konzolon:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Figyelje meg, `spanId` hogy van egy jelen a naplóüzenet, amely a span. Ez ugyanaz, `spanId` hogy tartozik a `hello`span nevű .

A naplóadatokat a `AzureLogHandler`használatával exportálhatja. További információt [ebben a cikkben](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs)talál.

## <a name="telemetry-correlation-in-net"></a>Telemetriai korreláció a .NET-ben

Az idő múlásával a .NET számos módot határozott meg a telemetriai és diagnosztikai naplók korrelációjára:

- `System.Diagnostics.CorrelationManager`lehetővé teszi a [LogicalOperationStack és activityId nyomon követését.](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx)
- `System.Diagnostics.Tracing.EventSource`és az Event Tracing for Windows (ETW) határozza meg a [SetCurrentThreadActivityId metódust.](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx)
- `ILogger`[naplóhatót](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes)használ.
- A Windows kommunikációs alaprendszer (WCF) és a HTTP fel -a "current" környezetpropagálást.

De ezek a módszerek nem engedélyezték az automatikus elosztott nyomkövetési támogatást. `DiagnosticSource`támogatja az automatikus gépközi korrelációt. A .NET `DiagnosticSource` függvénytárak támogatják és lehetővé teszik a korrelációs környezet automatikus, gépek közötti terjesztését az átvitelen keresztül, például http.

A [tevékenységfelhasználói](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) `DiagnosticSource` útmutató ismerteti a nyomon követési tevékenységek alapjait.

ASP.NET Core 2.0 támogatja a HTTP-fejlécek kinyerését és az új tevékenységek megkezdését.

`System.Net.Http.HttpClient`, kezdve a 4.1.0-s verzióval, támogatja a korrelációs HTTP-fejlécek automatikus befecskendezését és a HTTP-hívások tevékenységekként történő nyomon követését.

Van egy új HTTP-modul, [a Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), a klasszikus ASP.NET. Ez a modul telemetriai `DiagnosticSource`korrelációt valósít meg a használatával. A bejövő kérelemfejlécek alapján indít el egy tevékenységet. Azt is korrelálja a telemetriai adatok at a kérelmek feldolgozásának különböző szakaszaiban, még akkor is, ha az Internet Information Services (IIS) feldolgozásának minden fázisa egy másik felügyelt szálon fut.

Az Application Insights SDK, kezdve a 2.4.0-béta1-es verzióval, használja, `DiagnosticSource` és `Activity` telemetriai adatok gyűjtése és társítja azt az aktuális tevékenységhez.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>Telemetriai korreláció Java-ban

[Java-ügynök,](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) valamint a [Java SDK](../../azure-monitor/app/java-get-started.md) 2.0.0-s vagy újabb verziója támogatja a telemetriai adatok automatikus korrelációját. Automatikusan feltölti `operation_id` az összes telemetriai adatok (például a nyomkövetések, kivételek és egyéni események) a kérelem hatókörén belül kiadott. A http-n keresztüli szolgáltatás-szolgáltatás hívásokkor is propagálja a korrelációs fejléceket (korábban leírtak szerint), ha a [Java SDK-ügynök](../../azure-monitor/app/java-agent.md) konfigurálva van.

> [!NOTE]
> Az Application Insights Java-ügynök automatikusan gyűjti a JMS, a Kafka, a Netty/Webflux és más kérelmeket és függőségeket. Java SDK esetén csak az Apache HttpClient-en keresztül kezdeményezett hívások támogatottak a korrelációs funkcióhoz. Az automatikus környezetpropagálás az üzenetkezelési technológiák (például a Kafka, a RabbitMQ és az Azure Service Bus) között nem támogatott az SDK-ban. 

> [!NOTE]
> Egyéni telemetriai adatok gyűjtéséhez kell az alkalmazás a Java 2.6 SDK. 

### <a name="role-names"></a>Szerepkörnevek

Előfordulhat, hogy testre szeretné szabni az összetevőnevek megjelenítésének módját az [alkalmazástérképen.](../../azure-monitor/app/app-map.md) Ehhez manuálisan is beállíthatja `cloud_RoleName` az alábbi műveletek egyikét:

- Az Application Insights Java Agent 3.0 esetén állítsa be a felhőbeli szerepkör nevét a következőképpen:

    ```json
    {
      "instrumentationSettings": {
        "preview": {
          "roleName": "my cloud role name"
        }
      }
    }
    ```
    A felhőbeli szerepkör nevét a környezeti `APPLICATIONINSIGHTS_ROLE_NAME`változó használatával is beállíthatja.

- Az Application Insights Java SDK 2.5.0-s `cloud_RoleName` és `<RoleName>` újabb verzióival megadhatja az ApplicationInsights.xml fájlhoz való hozzáadással:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Ha a Spring Boot az Application Insights tavaszi boot starter, csak be kell állítania az egyéni nevét az alkalmazás az application.properties fájlban:

  `spring.application.name=<name-of-app>`

  A Tavaszi boot starter `cloudRoleName` automatikusan hozzárendeli `spring.application.name` a tulajdonsághoz megadott értéket.

## <a name="next-steps"></a>További lépések

- [Írjon egyéni telemetriai adatokat.](../../azure-monitor/app/api-custom-events-metrics.md)
- A Core és ASP.NET ASP.NET speciális korrelációs forgatókönyveit az Egyéni műveletek nyomon követése című [témakörben megtalálja.](custom-operations-tracking.md)
- További információ a más SDK-k [cloud_RoleName beállításáról.](../../azure-monitor/app/app-map.md#set-cloud-role-name)
- A mikroszolgáltatás összes összetevőjének az Application Insights on. Nézze meg a [támogatott platformokat.](../../azure-monitor/app/platforms.md)
- Tekintse meg az Application Insights-típusok [adatmodelljét.](../../azure-monitor/app/data-model.md)
- További információ a [telemetria meghosszabbításáról és szűréséről.](../../azure-monitor/app/api-filtering-sampling.md)
- Tekintse át az [Application Insights konfigurációs hivatkozását.](configuration-with-applicationinsights-config.md)
