---
title: Az Azure Application Insights telemetria korrelációja | Microsoft Docs
description: Application Insights telemetria korreláció
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 06897fffda490cdfcbb2a9cf6f55c7945e8afda0
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672055"
---
# <a name="telemetry-correlation-in-application-insights"></a>Telemetria korreláció a Application Insightsban

A szolgáltatások világában minden logikai művelet végrehajtásához a szolgáltatás különböző összetevőinek kell működniük. Ezeket az összetevőket külön is figyelheti [Application Insights](../../azure-monitor/app/app-insights-overview.md)használatával. A Application Insights támogatja az elosztott telemetria korrelációt, amelyet annak észlelésére használ, hogy melyik összetevő felelős a hibákért vagy a teljesítmény romlásához.

Ez a cikk a Application Insights által a több összetevő által elküldett telemetria korrelációt okozó adatmodellt ismerteti. Magában foglalja a kontextus-propagálási technikákat és protokollokat. Emellett a korrelációs taktikák megvalósítását is magában foglalja a különböző nyelveken és platformokon.

## <a name="data-model-for-telemetry-correlation"></a>Telemetria korrelációs adatmodell

Application Insights definiál egy [adatmodellt](../../azure-monitor/app/data-model.md) az elosztott telemetria korrelációhoz. A telemetria logikai művelettel való hozzárendeléséhez minden telemetria-elemhez tartozik egy `operation_Id`nevű környezeti mező. Ezt az azonosítót az elosztott nyomkövetés minden telemetria-eleme megosztja. Így még akkor is, ha egyetlen rétegből elveszíti a telemetria, a többi összetevő által jelentett telemetria is hozzárendelheti.

Az elosztott logikai műveletek jellemzően olyan kisebb műveletekből állnak, amelyek az egyik összetevő által feldolgozott kérelmek. Ezeket a műveleteket a [kérelem telemetria](../../azure-monitor/app/data-model-request-telemetry.md)határozza meg. Minden kérelem telemetria-eleme saját `id`, amely egyedileg és globálisan azonosítja azt. Továbbá a kérelemhez társított összes telemetria-elemnek (például a nyomkövetéseknek és kivételeknek) a kérelem `id`értékére kell állítania a `operation_parentId`.

Minden kimenő művelet, például egy HTTP-hívás egy másik összetevőhöz, a [függőségi telemetria](../../azure-monitor/app/data-model-dependency-telemetry.md)szerint jelenik meg. A függőségi telemetria a globálisan egyedi `id` is meghatározza. A függőségi hívás által kezdeményezett telemetria kérelme ezt a `id` használja `operation_parentId`ként.

Az elosztott logikai művelet nézetét `operation_Id`, `operation_parentId`és `request.id` használatával hozhatja létre `dependency.id`. Ezek a mezők a telemetria-hívások oksági sorrendjét is meghatározzák.

A Service-környezetekben az összetevőkből származó nyomkövetési adatok különböző tárolóhelyekre léphetnek. Minden összetevő rendelkezhet saját kialakítási kulccsal Application Insightsban. A logikai művelet telemetria lekéréséhez Application Insights minden tárolási elemről lekérdezi az adatait. Ha a tárolási elemek száma nagy, szüksége lesz egy tippre, ahol a következőt kell megkeresnie. A Application Insights adatmodell két mezőt határoz meg a probléma megoldásához: `request.source` és `dependency.target`. Az első mező azonosítja a függőségi kérelmet kezdeményező összetevőt. A második mező azonosítja, hogy melyik összetevő adta vissza a függőségi hívás válaszát.

## <a name="example"></a>Példa

Lássunk erre egy példát. A tőzsdei díjak nevű alkalmazás a készlet aktuális piaci árát jeleníti meg egy Stock nevű külső API használatával. A tőzsdei díjszabási alkalmazás rendelkezik egy Stock oldal nevű oldallal, amelyet az ügyfél webböngésző `GET /Home/Stock`használatával nyit meg. Az alkalmazás a HTTP-hívás `GET /api/stock/value`használatával kérdezi le a Stock API-t.

Az eredményül kapott telemetria a következő lekérdezés futtatásával elemezheti:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Az eredmények között vegye figyelembe, hogy az összes telemetria-elem megosztja a legfelső `operation_Id`. Amikor az oldalról AJAX-hívást kezdeményeznek, a rendszer új egyedi azonosítót (`qJSXU`) rendel a függőségi telemetria, és az oldalmegtekintés AZONOSÍTÓját használja `operation_ParentId`. A kiszolgálói kérelem ezután az Ajax ID-t használja `operation_ParentId`ként.

| ItemType   | név                      | ID (Azonosító)           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock lap                |              | STYz               | STYz         |
| függőségi | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| kérés    | Kezdőlap/készlet letöltése            | KqKwlrSt9PA= | qJSXU              | STYz         |
| függőségi | /API/Stock/Value beolvasása      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Ha egy külső szolgáltatás hívása `GET /api/stock/value`, ismernie kell a kiszolgáló identitását, hogy megfelelően beállítsa a `dependency.target` mezőt. Ha a külső szolgáltatás nem támogatja a figyelést, `target` a szolgáltatás állomásneve (például `stock-prices-api.com`) értékre van állítva. Ha azonban a szolgáltatás egy előre meghatározott HTTP-fejléc visszaadásával azonosítja magát, `target` tartalmazza a szolgáltatás identitását, amely lehetővé teszi, hogy a Application Insights elosztott nyomkövetést hozzon létre a szolgáltatásból származó telemetria lekérdezésével.

## <a name="correlation-headers"></a>Korrelációs fejlécek

Application Insights a [W3C nyomkövetési kontextusra](https://w3c.github.io/trace-context/)vált, amely az alábbiakat határozza meg:

- `traceparent`: a hívás globálisan egyedi műveleti AZONOSÍTÓját és egyedi azonosítóját végzi.
- `tracestate`: rendszerspecifikus nyomkövetési környezetet végez.

A Application Insights SDK legújabb verziója támogatja a nyomkövetési környezet protokollját, de előfordulhat, hogy be kell jelentkeznie. (A Application Insights SDK által támogatott korábbi korrelációs protokoll visszamenőleges kompatibilitása továbbra is fennáll.)

A [korrelációs HTTP protokoll, más néven a Request-ID is](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)elavult. Ez a protokoll két fejlécet definiál:

- `Request-Id`: a hívás globálisan egyedi AZONOSÍTÓját végzi.
- `Correlation-Context`: az elosztott nyomkövetési tulajdonságok név-érték párok összegyűjtését végzi.

A Application Insights a korrelációs HTTP protokoll [bővítményét](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) is meghatározza. `Request-Context` név-érték párokat használ a közvetlen hívó vagy a hívott által használt tulajdonságok gyűjteményének propagálásához. A Application Insights SDK ezt a fejlécet használja a `dependency.target` és a `request.source` mezők beállításához.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>A W3C elosztott nyomkövetési támogatásának engedélyezése klasszikus ASP.NET-alkalmazásokhoz
 
  > [!NOTE]
  >  `Microsoft.ApplicationInsights.Web` és `Microsoft.ApplicationInsights.DependencyCollector`tól kezdve nincs szükség konfigurációra.

A W3C nyomkövetési környezet támogatása visszamenőlegesen kompatibilis módon valósítható meg. A korreláció várhatóan az SDK korábbi verzióival (W3C-támogatás nélkül) működő alkalmazásokkal fog működni.

Ha továbbra is az örökölt `Request-Id` protokollt szeretné használni, letilthatja a nyomkövetési környezetet a következő konfiguráció használatával:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Ha az SDK egy régebbi verzióját futtatja, javasoljuk, hogy frissítse, vagy alkalmazza a következő konfigurációt a nyomkövetési környezet engedélyezéséhez.
Ez a funkció a `Microsoft.ApplicationInsights.Web` és `Microsoft.ApplicationInsights.DependencyCollector` csomagokban érhető el, a 2.8.0-béta verziótól kezdődően.
Alapértelmezés szerint le van tiltva. Ennek engedélyezéséhez végezze el a következő módosításokat `ApplicationInsights.config`:

- A `RequestTrackingTelemetryModule`területen adja hozzá a `EnableW3CHeadersExtraction` elemet, és állítsa az értékét `true`értékre.
- A `DependencyTrackingTelemetryModule`területen adja hozzá a `EnableW3CHeadersInjection` elemet, és állítsa az értékét `true`értékre.
- `W3COperationCorrelationTelemetryInitializer` hozzáadása a `TelemetryInitializers`alatt. Ez a következő példához hasonlóan fog kinézni:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>A W3C elosztott nyomkövetési támogatásának engedélyezése ASP.NET Core alkalmazások számára

 > [!NOTE]
  > A `Microsoft.ApplicationInsights.AspNetCore` 2.8.0 verziótól kezdődően nincs szükség konfigurációra.
 
A W3C nyomkövetési környezet támogatása visszamenőlegesen kompatibilis módon valósítható meg. A korreláció várhatóan az SDK korábbi verzióival (W3C-támogatás nélkül) működő alkalmazásokkal fog működni.

Ha továbbra is az örökölt `Request-Id` protokollt szeretné használni, letilthatja a nyomkövetési környezetet a következő konfiguráció használatával:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Ha az SDK egy régebbi verzióját futtatja, javasoljuk, hogy frissítse, vagy alkalmazza a következő konfigurációt a nyomkövetési környezet engedélyezéséhez.

Ez a szolgáltatás a 2.5.0-bétaverzió `Microsoft.ApplicationInsights.AspNetCore` verziójában, illetve a `Microsoft.ApplicationInsights.DependencyCollector` a 2.8.0-béta verzióban érhető el.
Alapértelmezés szerint le van tiltva. Az engedélyezéshez állítsa be `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` `true`:

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

  - Java EE-alkalmazások esetén adja hozzá a következőt a ApplicationInsights. xml fájl `<TelemetryModules>` címkéjéhez:

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
> Győződjön meg arról, hogy a bejövő és a kimenő konfigurációk pontosan ugyanazok.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>A W3C elosztott nyomkövetési támogatásának engedélyezése a Web Apps számára

Ez a funkció `Microsoft.ApplicationInsights.JavaScript`. Alapértelmezés szerint le van tiltva. Az engedélyezéséhez használja a `distributedTracingMode` config. A AI_AND_W3C a Application Insights által kiállított örökölt szolgáltatásokkal való visszamenőleges kompatibilitás érdekében van megadva.

- **NPM telepítője (Mellőzés a kódrészletek beállításakor)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
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
      /* ...other configuration options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing és Application Insights

A [OpenTracing adatmodell-specifikációja](https://opentracing.io/) és Application Insights adatmodell-leképezése a következő módon történik:

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` a `span.kind = server`                  |
| `Dependency`                          | `Span` a `span.kind = client`                  |
| `Request` és `Dependency` `Id`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `ChildOf` típusú `Reference` (a szülő span)   |

További információ: [Application Insights telemetria adatmodell](../../azure-monitor/app/data-model.md).

A OpenTracing-fogalmak definícióit lásd: OpenTracing- [specifikáció](https://github.com/opentracing/specification/blob/master/specification.md) és [szemantikai konvenciók](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-opencensus-python"></a>Telemetria korreláció a OpenCensus Pythonban

A OpenCensus Python a korábban ismertetett `OpenTracing` adatmodell-specifikációkat követi. Emellett a [W3C nyomkövetési környezetét](https://w3c.github.io/trace-context/) is támogatja anélkül, hogy konfigurációt kellene igényelni.

### <a name="incoming-request-correlation"></a>Bejövő kérelmek korrelációja

A OpenCensus Python a beérkező kérelmektől a beérkező kérések által a kérelmekből generált felöleli a W3C nyomkövetési kontextus fejléceit. A OpenCensus ezt a népszerű webalkalmazás-keretrendszer integrációs szolgáltatásával automatikusan elvégzi: a lombik, a Django és a piramis. Csak a W3C nyomkövetési környezet fejléceit kell megadnia a [megfelelő formátumban](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) , és el kell küldenie őket a kérelemmel. Íme egy példa a lombik alkalmazásra, amely a következőket mutatja be:

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

Ez a kód egy minta lombik-alkalmazást futtat a helyi gépen, és figyeli a `8080`portját. A nyomkövetési kontextus összekapcsolásához egy kérést küld a végpontnak. Ebben a példában a `curl` parancsot használhatja:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
A [nyomkövetési kontextus fejlécének formátuma](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)alapján a következő információk származhatnak:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Ha megtekinti a Azure Monitorba küldendő kérelem bejegyzést, a nyomkövetési fejléc információit tartalmazó mezőket láthat. Ezeket az adatnaplókat (Analitika) a Azure Monitor Application Insights erőforrásban találja.

![Telemetria kérése a naplókban (Analitika)](./media/opencensus-python/0011-correlation.png)

A `id` mező formátuma `<trace-id>.<span-id>`, ahol a `trace-id` a rendszer a kérelemben átadott nyomkövetési fejlécből veszi át, a `span-id` pedig a span 8 bájtos tömbje.

A `operation_ParentId` mező formátuma `<trace-id>.<parent-id>`, ahol a `trace-id` és a `parent-id` is a kérelemben átadott nyomkövetési fejlécből származik.

### <a name="log-correlation"></a>Napló korrelációja

A OpenCensus Python lehetővé teszi a naplók összekapcsolását egy nyomkövetési azonosító, egy span-azonosító és egy mintavételi jelző hozzáadásával a rekordok naplózásához. Ezeket az attribútumokat a OpenCensus [naplózási integrációjának](https://pypi.org/project/opencensus-ext-logging/)telepítésével adhatja hozzá. A következő attribútumok lesznek hozzáadva a Python `LogRecord` objektumokhoz: `traceId`, `spanId`és `traceSampled`. Vegye figyelembe, hogy ez csak az integráció után létrehozott naplózók esetén lép életbe.

Íme egy példa az alkalmazásra, amely a következőket mutatja be:

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
A kód futtatásakor a következő nyomatok jelennek meg a konzolon:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Figyelje meg, hogy a tartományon belül van egy `spanId` a naplózási üzenethez. Ez ugyanaz a `spanId`, amely a `hello`nevű tartományhoz tartozik.

A napló adatai a `AzureLogHandler`használatával exportálhatók. További információkért tekintse meg [ezt a cikket](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs).

## <a name="telemetry-correlation-in-net"></a>Telemetria korreláció a .NET-ben

Az idő múlásával a .NET számos módszert definiált a telemetria és diagnosztikai naplók összekapcsolásához:

- `System.Diagnostics.CorrelationManager` lehetővé teszi a [LogicalOperationStack és a tevékenységazonosító](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx)nyomon követését.
- a `System.Diagnostics.Tracing.EventSource` és a Windows esemény-nyomkövetés (ETW) határozza meg a [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) metódust.
- `ILogger` a [naplózási hatóköröket](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes)használja.
- A Windows Communication Foundation (WCF) és a HTTP-átvitel "aktuális" kontextus-propagálást használ.

Ezek a módszerek azonban nem engedélyezték az automatikus elosztott nyomkövetés támogatását. a `DiagnosticSource` támogatja az automatikus gépek közötti korrelációt. A .NET-kódtárak támogatják a `DiagnosticSource` és lehetővé teszik a korrelációs környezet automatikus, a szállításon keresztüli propagálását, például a HTTP-t.

A `DiagnosticSource` [tevékenység felhasználói útmutatója](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) ismerteti a tevékenységek nyomon követésének alapjait.

A ASP.NET Core 2,0 támogatja a HTTP-fejlécek kinyerését és az új tevékenységek indítását.

a 4.1.0 verziótól kezdődően a `System.Net.Http.HttpClient`támogatja a korrelációs HTTP-fejlécek automatikus befecskendezését és a HTTP-hívások tevékenységként való nyomon követését.

Létezik egy új HTTP-modul, a [Microsoft. AspNet. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/)a klasszikus ASP.net. Ez a modul `DiagnosticSource`használatával valósítja meg a telemetria korrelációját. Elindít egy tevékenységet a bejövő kérelmek fejlécei alapján. Emellett a kérelmek feldolgozásának különböző szakaszaiból is összefügg a telemetria, még akkor is, ha a Internet Information Services (IIS) feldolgozásának minden fázisa egy másik felügyelt szálon fut.

A 2.4.0-béta verziótól kezdődően a Application Insights SDK a `DiagnosticSource` és a `Activity` használatával gyűjti össze a telemetria, és társítja az aktuális tevékenységhez.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Telemetria korreláció a Java SDK-ban

A Java-2.0.0 vagy újabb verzióhoz készült [Application INSIGHTS SDK](../../azure-monitor/app/java-get-started.md) támogatja a telemetria automatikus korrelációját. Automatikusan kitölti a `operation_id` az összes telemetria (például a Nyomkövetések, a kivételek és az egyéni események esetében), amelyek egy kérelem hatókörén belül lettek kiállítva. Emellett a HTTP protokollon keresztül terjeszti a korrelációs fejléceket (lásd fentebb) a szolgáltatások közötti hívásokhoz, ha a [Java SDK-ügynök](../../azure-monitor/app/java-agent.md) konfigurálva van.

> [!NOTE]
> A korrelációs szolgáltatás csak az Apache HttpClient-on keresztül kezdeményezett hívásokat támogatja. A Spring RestTemplate és a kitalál is használható az Apache HttpClient a motorháztető alatt.

Az üzenetkezelési technológiák (például a Kafka, a RabbitMQ és a Azure Service Bus) közötti automatikus környezet-propagálás jelenleg nem támogatott. Az ilyen forgatókönyvek az `trackDependency` és az `trackRequest` metódusok használatával manuálisan is felhasználhatók. Ezekben a módszerekben a függőségi telemetria egy gyártó által várólistán lévő üzenetet jelöl. A kérelem a fogyasztó által feldolgozott üzenetet jelöli. Ebben az esetben a `operation_id` és az `operation_parentId` is propagálni kell az üzenet tulajdonságai között.

### <a name="telemetry-correlation-in-asynchronous-java-applications"></a>Telemetria korreláció az aszinkron Java-alkalmazásokban

Ha szeretné megtudni, hogyan korrelál a telemetria egy aszinkron rugós rendszerindítási alkalmazásban, tekintse meg [az elosztott nyomkövetés aszinkron Java-alkalmazásokban](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications)című témakört. Ez a cikk útmutatást nyújt a Spring [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) és [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html)kialakításához.


<a name="java-role-name"></a>
## <a name="role-name"></a>Szerepkörnév

Érdemes lehet testreszabni, ahogy az összetevők nevei megjelennek az alkalmazás- [hozzárendelésben](../../azure-monitor/app/app-map.md). Ehhez manuálisan állíthatja be a `cloud_RoleName`t a következő műveletek egyikének kiszámításával:

- A Application Insights Java SDK 2.5.0-es és újabb verzióiban megadhatja a `cloud_RoleName` a ApplicationInsights. xml fájlhoz `<RoleName>` hozzáadásával:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Ha rugós rendszerindítást használ a Application Insights Spring boot Starter-vel, csak be kell állítania az alkalmazás egyéni nevét az Application. properties fájlban:

  `spring.application.name=<name-of-app>`

  A Spring boot Starter automatikusan `cloudRoleName` rendel hozzá a `spring.application.name` tulajdonsághoz megadott értékhez.

## <a name="next-steps"></a>Következő lépések

- [Egyéni telemetria](../../azure-monitor/app/api-custom-events-metrics.md)írása.
- A ASP.NET Core-és ASP.NET speciális korrelációs forgatókönyvei: [Egyéni műveletek nyomon követése](custom-operations-tracking.md).
- További információ a más SDK-k [cloud_RoleName beállításáról](../../azure-monitor/app/app-map.md#set-cloud-role-name) .
- A Application Insights összes összetevőjének bevezetését. Tekintse meg a [támogatott platformokat](../../azure-monitor/app/platforms.md).
- Tekintse meg Application Insights típusok [adatmodelljét](../../azure-monitor/app/data-model.md) .
- Ismerje meg [, hogyan bővítheti és szűrheti a telemetria](../../azure-monitor/app/api-filtering-sampling.md).
- Tekintse át a [Application Insights konfigurációs referenciát](configuration-with-applicationinsights-config.md).
