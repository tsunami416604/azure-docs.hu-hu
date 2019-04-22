---
title: Az Azure Application Insights telemetriai korreláció |} A Microsoft Docs
description: Application Insights telemetriai korreláció
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/14/2019
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: 565f08f0c69aef393a9296f3cce90570a3f0bc2c
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683019"
---
# <a name="telemetry-correlation-in-application-insights"></a>Az Application Insights telemetriai korreláció

A világon, mikroszolgáltatások minden logikai művelet a szolgáltatás különböző összetevői az elvégezni kívánt munka szükséges. Egyes összetevők által külön-külön figyelhető [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). A webalkalmazás-összetevő kommunikál a hitelesítési szolgáltató összetevő felhasználói fiók hitelesítő adatainak érvényesítéséhez, és az API összetevővel való adatbeolvasás a vizualizációt. Az API-összetevő is lekérhet adatokat más szolgáltatásokból és gyorsítótár-szolgáltató összetevőinek használatával értesítheti a a hívás a számlázási összetevő. Application Insights támogatja az elosztott telemetriai korreláció összetevő észleléséhez használt hibák vagy a teljesítmény romlását felelős.

Ez a cikk ismerteti az adatmodellbe, több összetevő által küldött telemetriai adatainak az Application Insights segítségével. Ez fedezi a környezet-propagálás módszerek és protokollok. Emellett ismerteti a különböző nyelveken és platformokon korrelációs fogalmak megvalósítását.

## <a name="data-model-for-telemetry-correlation"></a>Adatmodell az telemetriai korreláció

Az Application Insights meghatározása egy [adatmodell](../../azure-monitor/app/data-model.md) elosztott telemetriai korreláció. Telemetriai társítani a logikai művelettel, minden szereplő telemetriai elem környezet mezője rendelkezik `operation_Id`. Ez az azonosító az elosztott nyomkövetést minden szereplő telemetriai elem által megosztott. Így még egy rétegben származó telemetriai adatok elvesztését, és továbbra is társíthat más összetevők által küldött telemetriát.

Számos kisebb műveletet, amelyek egy-egy összetevőjét által feldolgozott kérelmek általában tartalmaz egy elosztott logikai művelet. Ezek a műveletek által meghatározott [telemetriai kérelem](../../azure-monitor/app/data-model-request-telemetry.md). Minden kéréstelemetria rendelkezik a saját `id` , amely azonosítja egyedileg és globálisan. (Például a hívásláncokat és kivételeket) minden olyan telemetriai elem ehhez a kérelemhez társított kell beállítania, és a `operation_parentId` értékével a kérés `id`.

Minden kimenő művelet, például egy másik összetevő, HTTP-hívás által jelölt [– függőségi telemetria](../../azure-monitor/app/data-model-dependency-telemetry.md). Függőségek telemetriáját is meghatározza a saját `id` , amely globálisan egyedi. Ezt használja a telemetriához, a függőségi hívás által kezdeményezett kérelem `id` , annak `operation_parentId`.

Az elosztott logikai művelet nézet használatával hozhat létre `operation_Id`, `operation_parentId`, és `request.id` a `dependency.id`. Ezek a mezők is meghatározhat, a telemetriai adatok hívások okozati sorrendjét.

A mikroszolgáltatás-alapú környezetben összetevők hívásláncait különböző tárolási elemek ugorhat. Minden összetevő az Application Insights a saját kialakítási kulcsot is lehet. Telemetriai adatok beolvasása a logikai művelethez, minden tároló adott elemére adatokat kérdezhet le. Ha hatalmas tárolási elemek száma, szüksége lesz egy érhető el, és tekintse meg a következő tárolási helyét. Az Application Insights adatmodell határozza meg a probléma megoldásához két mező: `request.source` és `dependency.target`. Az első mező azonosítja az a komponens, amely a függőségi kérelem kezdeményezett, és a második azonosítja, hogy mely összetevőt adja vissza a válasz a függőségi hívás.

## <a name="example"></a>Példa

Vegyünk egy példát egy készlet árak, amely megjeleníti egy készlet aktuális piaci ára nevű egy külső API-val nevű alkalmazás `Stock`. A készlet árak alkalmazásnak nevű `Stock page` , amely az ügyfél webböngészőjével nyílik `GET /Home/Stock`. Az alkalmazás-lekérdezéseket a `Stock` API HTTP-hívás használatával `GET /api/stock/value`.

Elemezheti az eredményül kapott telemetriai adatokat egy lekérdezés futtatásával:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Az eredmények között, vegye figyelembe, hogy az összes telemetriai elem megosztani a legfelső szintű `operation_Id`. Ha egy Ajax-hívás érkezett az oldal, egy új egyedi azonosító (`qJSXU`) van rendelve a függőségi telemetria és az azonosítója, a oldalmegtekintés `operation_ParentId`. A kiszolgálói kérelem majd használja az Ajax azonosítója `operation_ParentId`.

| itemType   | név                      | ID (Azonosító)           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Tőzsdei lap                |              | STYz               | STYz         |
| függőség | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| kérelem    | GET Home/készlet            | KqKwlrSt9PA= | qJSXU              | STYz         |
| függőség | /Api/stock/value beolvasása      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Ha a hívás `GET /api/stock/value` kérés érkezett egy külső szolgáltatásnak, érdemes figyelembe venni, hogy a kiszolgáló identitását, tehát beállíthatja a `dependency.target` mezőt megfelelően. Ha a külső szolgáltatás nem támogatja a figyelés, `target` beállítása a szolgáltatás a gazdagép nevét (például `stock-prices-api.com`). Azonban, ha a szolgáltatás egy előre meghatározott HTTP-fejléc felismerésével azonosítja magát `target` tartalmazza a felügyeltszolgáltatás-identitás, amely lehetővé teszi, hogy az Application Insights hozhat létre egy elosztott nyomkövetést ehhez lekérdezi a telemetriát a szolgáltatástól.

## <a name="correlation-headers"></a>Korrelációs fejlécek

Dolgozunk az RFC javaslatot a [korrelációs HTTP-protokoll](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Ez a javaslat két fejlécek határozza meg:

- `Request-Id`: A globálisan egyedi azonosító a hívás végzi.
- `Correlation-Context`: Az elosztott nyomkövetési tulajdonságai név-érték párok gyűjteménye végzi.

A standard is határozza meg a két sémák `Request-Id` generációs: és a hierarchikus. Az egybesimított, egy ismert sémájú `Id` definiál kulcsot az `Correlation-Context` gyűjtemény.

Az Application Insights határozza meg a [bővítmény](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) a korrelációs HTTP-protokoll esetében. Használ `Request-Context` név-érték párok propagálása a közvetlen hívó megjelölése vagy a hívott fél által használt tulajdonságok gyűjteménye. Az Application Insights SDK-t használ a fejléc beállítása `dependency.target` és `request.source` mezőket.

### <a name="w3c-distributed-tracing"></a>W3C elosztott nyomkövetést

Mi vagyunk való [W3C elosztott nyomkövetést formátum](https://w3c.github.io/trace-context/). Azt határozza meg:

- `traceparent`: Végzi a globálisan egyedi művelet azonosítója és a hívás egyedi azonosítója.
- `tracestate`: Nyomkövetés rendszerspecifikus környezetben végzi.

#### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Klasszikus ASP.NET-alkalmazások elosztott W3C-nyomkövetés támogatásának engedélyezése

Ez a funkció érhető el a `Microsoft.ApplicationInsights.Web` és `Microsoft.ApplicationInsights.DependencyCollector` csomagok verzió 2.8.0-beta1 kezdve.
Alapértelmezés szerint le van tiltva. Annak engedélyezéséhez módosítsa `ApplicationInsights.config`:

- A `RequestTrackingTelemetryModule`, adja hozzá a `EnableW3CHeadersExtraction` értékre állított elem `true`.
- A `DependencyTrackingTelemetryModule`, adja hozzá a `EnableW3CHeadersInjection` értékre állított elem `true`.

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>ASP.NET Core-alkalmazások elosztott W3C-nyomkövetés támogatásának engedélyezése

Ez a funkció szerepel `Microsoft.ApplicationInsights.AspNetCore` verzió 2.5.0-beta1 és a `Microsoft.ApplicationInsights.DependencyCollector` verzió 2.8.0-beta1.
Alapértelmezés szerint le van tiltva. Annak engedélyezéséhez állítsa `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` való `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

#### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Java-alkalmazások elosztott W3C-nyomkövetés támogatásának engedélyezése

- **A bejövő configuration**

  - A Java EE-alapú alkalmazások esetén adja hozzá a következőt a `<TelemetryModules>` ApplicationInsights.xml belüli címkét:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
  - Spring Boot-alkalmazások esetén adja hozzá a következő tulajdonságokkal:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Kimenő konfiguráció**

  Adja hozzá a következő AI-Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > Előző verziókkal való kompatibilitási mód alapértelmezés szerint engedélyezve van, és a `enableW3CBackCompat` paramétert nem kötelező megadni. Használja azt, csak a visszamenőleges kompatibilitás érdekében kapcsolja ki a kívánt esetén.
  >
  > Ideális esetben tenné kikapcsolása ez mikor minden szolgáltatás frissítve lett-e az SDK-k, amelyek támogatják a W3C-protokoll újabb verziójára. Kifejezetten ajánljuk, hogy áthelyezi ezeket újabb SDK-k minél hamarabb.

> [!IMPORTANT]
> Győződjön meg arról, hogy a bejövő és kimenő konfigurációk pontosan ugyanaz.

## <a name="opentracing-and-application-insights"></a>OpenTracing és az Application Insights

A [OpenTracing adatmodell-specifikáció](https://opentracing.io/) és Application Insights-adatmodellek képezze le a következő módon:

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` a `span.kind = server`                  |
| `Dependency`                          | `Span` a `span.kind = client`                  |
| `Id` a `Request` és `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` típusú `ChildOf` (a szülő span)   |

További információkért lásd: a [Application Insights telemetria adatmodell](../../azure-monitor/app/data-model.md). 

OpenTracing fogalmak definícióját, tekintse meg a OpenTracing [specifikáció](https://github.com/opentracing/specification/blob/master/specification.md) és [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-net"></a>Telemetriai korreláció a .NET-ben

Az idő múlásával .NET definiált korrelációját, telemetriai adatok és a diagnosztikai naplók többféle módon:

- `System.Diagnostics.CorrelationManager` lehetővé teszi, hogy nyomon követheti [LogicalOperationStack és tevékenységazonosító](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). 
- `System.Diagnostics.Tracing.EventSource` Esemény-nyomkövetése Windows (ETW) határozza meg, és a [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) metódust.
- `ILogger` használja a [Log hatókörök](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- Windows Communication Foundation (WCF) és a HTTP átviteli "aktuális" környezeti propagálás fel.

Azonban azokat a módszereket nem engedélyezte az automatikus elosztott nyomkövetést támogatása. `DiagnosticSource` támogatja az automatikus cross-gép korrelációs módja a. .NET-kódtárakra támogatja a "DiagnosticSource", és lehetővé teszi a szállítási, például HTTP-n keresztül a korrelációs környezet automatikus cross-gép propagálása.

A [útmutató a tevékenységek](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) a `DiagnosticSource` tevékenységek nyomon követése alapjait ismerteti.

Az ASP.NET Core 2.0 támogatja a HTTP-fejléceket és a egy új tevékenység indítása.

`System.Net.HttpClient`, a korrelációs HTTP-fejlécek és nyomon követése a HTTP-hívás tevékenységként automatikus injektálási 4.1.0-s, kezdve támogatja.

Van egy új HTTP-modulja [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), klasszikus ASP.NET-hez. Ez a modul használatával valósítja meg telemetriai korreláció `DiagnosticSource`. Elindít egy tevékenységet, a bejövő kérelem fejlécek alapján. Ezenkívül összehasonlítja a használati adatok gyűjtése a különböző szakaszai a kérelmek feldolgozását, akár az esetekben, amikor egy másik felügyelt szálon fut az Internet Information Services (IIS) feldolgozási minden szint esetében.

Használja az Application Insights SDK verziója 2.4.0-beta1, kezdve `DiagnosticSource` és `Activity` telemetriai adatok gyűjtésére, és társíthatja azt az aktuális tevékenység.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Telemetriai korreláció a Java SDK-ban

A [Javához készült Application Insights SDK](../../azure-monitor/app/java-get-started.md) támogatja az automatikus korrelációs telemetriai kezdetének 2.0.0-s verziójával. Automatikusan feltölti `operation_id` az összes telemetriai adat (például a nyomkövetések, kivételek és egyéni események) kiadott kérelem hatókörén belül. Azt is gondoskodik propagálása a korrelációs fejléceinek (lásd a korábbiakban) szolgáltatások közötti hívások HTTP-n keresztül, ha a [Java SDK ügynök](../../azure-monitor/app/java-agent.md) van konfigurálva.

> [!NOTE]
> A korrelációs funkció csak az Apache HTTPClient-n keresztül végzett hívások esetén támogatottak. Spring RestTemplate vagy Feign használja, ha mindkét használható az Apache HTTPClient technikai részletek.

Több üzenetkezelési technológiák (például Kafka, RabbitMQ vagy Azure Service Bus) környezet automatikus propagálás jelenleg nem támogatott. Azonban az ilyen esetekben manuális code használatával lehetőség a `trackDependency` és `trackRequest` API-k. Az API-k egy függőségi telemetria egy üzenet, folyamatban lévő MIF-fájl a gyártó által, és a kérelem egy fogyasztó által feldolgozott üzenet jelenti. Ebben az esetben is `operation_id` és `operation_parentId` kell propagálására, az üzenet tulajdonságai.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>Aszinkron Java-alkalmazás a telemetriai korreláció

Aszinkron Spring Boot-alkalmazás a telemetriai adatok összefüggéseinek kövesse [ez](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) részletes cikket. A Spring a rendszerállapot-figyelésére vonatkozó útmutatással [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) , valamint [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html). 


<a name="java-role-name"></a>
## <a name="role-name"></a>Szerepkörnév

Időnként érdemes testre is szabhatja összetevők nevéhez megjelennek a [Alkalmazástérkép](../../azure-monitor/app/app-map.md). Ehhez manuálisan állíthatja a `cloud_RoleName` tegye a következők egyikét:

- Spring Boot az Application Insights Spring Boot starter használja, az egyetlen szükséges változás-e az egyéni nevet az alkalmazás beállítása a application.properties fájlban.

  `spring.application.name=<name-of-app>`

  Automatikusan hozzárendeli a Spring Boot starter `cloudRoleName` , az érték azt adja meg a `spring.application.name` tulajdonság.

- Ha használja a `WebRequestTrackingFilter`, a `WebAppNameContextInitializer` automatikusan beállítja az alkalmazás nevét. A konfigurációs fájl (applicationinsights.xml fájlt), adja hozzá a következő:

  ```XML
  <ContextInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
  </ContextInitializers>
  ```

- Ha a felhőalapú környezet osztály:

  ```Java
  telemetryClient.getContext().getCloud().setRole("My Component Name");
  ```

## <a name="next-steps"></a>További lépések

- Írási [egyéni telemetriát](../../azure-monitor/app/api-custom-events-metrics.md).
- Tudjon meg többet [cloud_RoleName beállítás](../../azure-monitor/app/app-map.md#set-cloud-role-name) más SDK-k esetében.
- Előkészítheti az Application Insights a mikroszolgáltatási összes összetevőjét. Tekintse meg a [által támogatott platformok](../../azure-monitor/app/platforms.md).
- Tekintse meg a [adatmodell](../../azure-monitor/app/data-model.md) Application Insights-típusokhoz.
- Ismerje meg, hogyan [bővítése és szűrőtelemetria](../../azure-monitor/app/api-filtering-sampling.md).
- Tekintse át a [Application Insights konfigurációs hivatkozás](configuration-with-applicationinsights-config.md).
