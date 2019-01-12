---
title: Az Azure Application Insights Telemetriai korreláció |} A Microsoft Docs
description: Application Insights telemetriai korreláció
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/10/2019
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: 8b31a85abf1c6034aaff511f23d96fae9ee64561
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54230050"
---
# <a name="telemetry-correlation-in-application-insights"></a>Az Application Insights telemetriai korreláció

Minden logikai művelet mikroszolgáltatásokhoz világában alatt a szolgáltatás különböző összetevői szükségesek. Ezek az összetevők mindegyike külön használatával figyelhető [Application Insights](../../azure-monitor/app/app-insights-overview.md). A webes alkalmazás-összetevő hitelesítési szolgáltató összetevőt felhasználói fiók hitelesítő adatainak érvényesítéséhez, és az API összetevővel való adatbeolvasás a Vizualizáció kommunikál. A kapcsolja be az API-összetevő más szolgáltatások adatokat kérdezhet le és használja a gyorsítótár-szolgáltató összetevőinek és értesítés a hívás a számlázási összetevő. Application Insights támogatja az elosztott telemetriai korreláció. Lehetővé teszi, hogy melyik összetevő felelős hibák vagy a teljesítmény romlását észlelése.

Ez a cikk ismerteti az adatmodellbe, több összetevő által küldött telemetriai adatainak az Application Insights segítségével. Ez fedezi a környezet propagálás módszerek és protokollok. Emellett ismerteti a különböző nyelveken és platformokon korrelációs fogalmak megvalósítását.

## <a name="telemetry-correlation-data-model"></a>Telemetriai korreláció adatmodell

Az Application Insights meghatározása egy [adatmodell](../../azure-monitor/app/data-model.md) elosztott telemetriai korreláció. Telemetriai társítani a logikai művelettel, minden szereplő telemetriai elem környezet mezője rendelkezik `operation_Id`. Ez az azonosító az elosztott nyomkövetést minden szereplő telemetriai elem által megosztott. Egyetlen lejátszóval származó telemetriai adatok elvesztését tehát még az továbbra is társíthat más összetevők által küldött telemetriát.

Elosztott logikai művelet jellemzően kisebb műveletek – a egyik által feldolgozott kérelmek egy készlete áll. Ezek a műveletek által meghatározott [telemetriai kérelem](../../azure-monitor/app/data-model-request-telemetry.md). Minden kéréstelemetria rendelkezik a saját `id` , amely globálisan egyedi módon azonosítja. Minden telemetriai adat - nyomkövetéseket, kivételek, stb. Ehhez a kérelemhez társított kell beállítania, és a `operation_parentId` értékével a kérés `id`.

Minden kimenő művelet (például egy másik összetevő http-hívás) által jelölt [– függőségi telemetria](../../azure-monitor/app/data-model-dependency-telemetry.md). Függőségek telemetriáját is meghatározza a saját `id` , amely globálisan egyedi. Kérelmek telemetriai adatai, a függőségi hívás által kezdeményezett használja, mint `operation_parentId`.

A nézet az elosztott logikai művelet használatával hozhat létre `operation_Id`, `operation_parentId`, és `request.id` a `dependency.id`. Ezek a mezők is meghatározhat, a telemetriai adatok hívások okozati sorrendjét.

A mikroszolgáltatásokhoz környezet összetevők hívásláncait előfordulhat, hogy lépjen a különböző tárolók. Minden összetevő lehet a saját kialakítási kulcsot az Application Insightsban. Telemetriai adatok beolvasása a logikai művelethez, kell adatokat kérdezhet le az összes storage-ból. Ha tárolók száma hatalmas, szüksége lesz egy érhető el a következő megnéznie.

Az Application Insights adatmodell határozza meg a probléma megoldásához két mező: `request.source` és `dependency.target`. Az első mező azonosítja az a komponens, amely a függőségi kérelem kezdeményezett, és a második azonosítja, hogy mely összetevőt adja vissza a válasz a függőségi hívás.


## <a name="example"></a>Példa

Vegyük például egy alkalmazás készlet árak a jelenlegi piaci ár egy készlet a készletek API nevű külső API-val. A készlet árak alkalmazás rendelkezik egy oldal `Stock page` használatával az ügyfél webes böngészőben megnyitott `GET /Home/Stock`. Az alkalmazás a készlet API lekérdezi egy HTTP-hívás használatával `GET /api/stock/value`.

Elemezheti az eredményül kapott telemetriai adatokat, egy lekérdezés futtatása:

```
(requests | union dependencies | union pageViews) 
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Az eredmény megtekintése vegye figyelembe, hogy az összes telemetriai elem megosztani a legfelső szintű a `operation_Id`. Amikor ajax hívás érkezett az lap – új egyedi azonosító `qJSXU` van rendelve a függőségek telemetriáját és oldalmegtekintés a azonosítója `operation_ParentId`. Viszont a kiszolgálói kérelem használja, az ajax-azonosító `operation_ParentId`stb.

| ItemType   | név                      | ID (Azonosító)           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| oldalmegtekintés   | Tőzsdei lap                |              | STYz               | STYz         |
| függőség | GET/Home/készlet           | qJSXU        | STYz               | STYz         |
| kérelem    | GET Home/készlet            | KqKwlrSt9PA= | qJSXU              | STYz         |
| függőség | /Api/stock/value beolvasása      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Mostantól Ha a hívás `GET /api/stock/value` érdemes figyelembe venni, hogy a kiszolgáló identitását egy külső szolgáltatáshoz. Tehát beállíthatja `dependency.target` megfelelő mezőbe. Ha a külső szolgáltatás nem támogatja a figyelés – `target` az állomás neve, mint a szolgáltatás beállítása `stock-prices-api.com`. Azonban ha a szolgáltatás azonosítja magát egy előre meghatározott visszaadó HTTP-fejléc - `target` tartalmazza a felügyeltszolgáltatás-identitás, amely lehetővé teszi, hogy az Application Insights hozhat létre elosztott nyomkövetési ehhez lekérdezi a telemetriát a szolgáltatástól. 

## <a name="correlation-headers"></a>Korrelációs fejlécek

Dolgozunk az RFC javaslatot a [korrelációs HTTP-protokoll](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Ez a javaslat két fejlécek határozza meg:

- `Request-Id` a globálisan egyedi azonosító a hívás végrehajtása
- `Correlation-Context` – a végrehajtott műveletek közül sokat elosztott nyomkövetési tulajdonságainak neve érték párok gyűjteménye

A standard is határozza meg a két sémái `Request-Id` generování - és a hierarchikus. A egybesimított séma, amely egy jól ismert `Id` definiált kulcs a `Correlation-Context` gyűjtemény.

Az Application Insights határozza meg a [bővítmény](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) a korrelációs HTTP-protokoll esetében. Használ `Request-Context` érték párok propagálása a közvetlen hívó megjelölése vagy a hívott fél által használt tulajdonságok a gyűjtemény neve. Application Insights SDK-t használ a fejléc beállítása `dependency.target` és `request.source` mezőket.

### <a name="w3c-distributed-tracing"></a>W3C elosztott nyomkövetést

Mi vagyunk való [W3C elosztott nyomkövetést formátum](https://w3c.github.io/trace-context/). Azt határozza meg:
- `traceparent` – globálisan egyedi művelet azonosítója és a hívás egyedi azonosítója
- `tracestate` -nyomkövetés rendszerspecifikus környezetben végzi.

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-classic-apps"></a>ASP.NET klasszikus alkalmazások elosztott W3C-nyomkövetés támogatásának engedélyezése

Ez a funkció verzió 2.8.0-beta1 kezdve Microsoft.ApplicationInsights.Web és Microsoft.ApplicationInsights.DependencyCollector csomagokban érhető el.
Rendelkezik **ki** alapértelmezés szerint az engedélyezéshez, módosítsa `ApplicationInsights.config`:

* a `RequestTrackingTelemetryModule` hozzáadása `EnableW3CHeadersExtraction` értékre állított elem `true`
* a `DependencyTrackingTelemetryModule` hozzáadása `EnableW3CHeadersInjection` értékre állított elem `true`

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>ASP.NET Core-alkalmazások elosztott W3C-nyomkövetés támogatásának engedélyezése

Ez a funkció a verzió 2.5.0-beta1 és Microsoft.ApplicationInsights.DependencyCollector verzió 2.8.0-beta1 Microsoft.ApplicationInsights.AspNetCore van.
Rendelkezik **ki** alapértelmezés szerint az engedélyezéshez beállítva `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` való `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

#### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Java-alkalmazások elosztott W3C-nyomkövetés támogatásának engedélyezése

Bejövő:

**J2ee-alapú alkalmazások** adja hozzá a következőt a `<TelemetryModules>` belül ApplicationInsights.xml címke

```xml
<Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
   <Param name = "W3CEnabled" value ="true"/>
   <Param name ="enableW3CBackCompat" value = "true" />
</Add>
```

**A Spring Boot-alkalmazások** adja hozzá a következő tulajdonságokkal:

`azure.application-insights.web.enable-W3C=true`
`azure.application-insights.web.enable-W3C-backcompat-mode=true`

Kimenő:

Adja hozzá a következő AI-Agent.xml:

```xml
<Instrumentation>
        <BuiltIn enabled="true">
            <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
        </BuiltIn>
    </Instrumentation>
```

> [!NOTE]
> Előző verziókkal való kompatibilitási mód alapértelmezés szerint engedélyezve van, és a enableW3CBackCompat paraméter nem kötelező, és csak akkor, ha szeretné kikapcsolni használható. 

Az eset ideális esetben ez akkor lehet, ha minden szolgáltatás SDK-k W3C protokollt támogató újabb verzióra frissítve lett-e. Azt javasoljuk, minél hamarabb helyezze át a W3C-támogatással rendelkező SDK-k újabb verzióra. 

Győződjön meg arról, hogy mindkét **bejövő és kimenő konfigurációk esetében pontosan azonos**.

## <a name="open-tracing-and-application-insights"></a>Nyissa meg a nyomkövetés és az Application Insights

A [nyílt nyomkövetés adatmodell specifikáció](https://opentracing.io/) és Application Insights-adatmodellek képezze le a következő módon:

| Application Insights                  | Nyissa meg a nyomkövetés                                      |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` a `span.kind = server`                  |
| `Dependency`                          | `Span` a `span.kind = client`                  |
| `Id` a `Request` és `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` típusú `ChildOf` (a szülő span)   |

Az Application Insights-adatmodellen alapuló további információkért lásd: [adatmodell](../../azure-monitor/app/data-model.md). 

Tekintse meg a nyitott nyomkövetés [specifikáció](https://github.com/opentracing/specification/blob/master/specification.md) és [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) nyílt nyomkövetés fogalmak definícióját.

## <a name="telemetry-correlation-in-net"></a>Telemetriai korreláció a .NET-ben

Idővel .NET definiált számos módon telemetriai és a diagnosztikai naplók korrelációját. Nincs `System.Diagnostics.CorrelationManager` nyomon így [LogicalOperationStack és tevékenységazonosító](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). `System.Diagnostics.Tracing.EventSource` és Windows ETW határozza meg a metódus [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx). `ILogger` használja a [Log hatókörök](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). A WCF és a Http átviteli "aktuális" környezeti propagálás fel.

Azonban azokat a módszereket nem engedélyezte az automatikus elosztott nyomkövetést támogatása. `DiagnosticsSource` egy módja támogatja a gép korrelációs közötti automatikus. .NET-kódtárakra diagnosztikai adatforrás támogatja, és engedélyezze a gép terjesztése a a szállítás, például a http-n keresztül a korrelációs környezet közötti automatikus.

A [útmutató a tevékenységek](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) diagnosztikai forrás tevékenységek nyomon követése alapjait ismerteti. 

Az ASP.NET Core 2.0 támogatja a Http-fejlécek és a egy új tevékenység indítása. 

`System.Net.HttpClient` kiindulási verzió `4.1.0` támogatja a Http-fejlécek korreláció automatikus injektálási, és nyomon követi a http-hívás, mivel a tevékenység.

Van egy új Http-modulja [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/) az ASP.NET klasszikus üzemi modell esetén. Ez a modul telemetriai korreláció DiagnosticsSource használatával valósítja meg. A bejövő kérelem fejlécek alapján tevékenység kezdődik. Ezenkívül összehasonlítja a használati adatok gyűjtése a különböző szakaszai a kérelem feldolgozása. Még a az esetekben, amikor az egy másik kezelés szálak fut az IIS feldolgozó minden szakaszában.

Application Insights SDK-t kiindulási verzió `2.4.0-beta1` DiagnosticsSource és a tevékenység használja a telemetriai adatok gyűjtésére, és társíthatja azt az aktuális tevékenység. 

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Telemetriai korreláció a Java SDK-ban
A [Application Insights Java SDK](../../azure-monitor/app/java-get-started.md) támogatja az automatikus korrelációs telemetriai kezdetének verziójával `2.0.0`. Automatikusan feltölti `operation_id` az összes telemetriai adatok (traces, kivételek, egyéni eseményeket stb.) kiadott kérelem hatókörén belül. Azt is gondoskodik propagálása a korrelációs fejléceket (lásd fent) a szolgáltatások közötti hívások HTTP-n keresztül, ha a [Java SDK ügynök](../../azure-monitor/app/java-agent.md) van konfigurálva. Megjegyzés: a korrelációs funkció csak a HTTP-alapú Apache-n keresztül végzett hívások támogatottak. Spring Rest-sablon vagy Feign használja, ha mindkét használható az Apache HTTP-ügyfél technikai részletek.

Több üzenetkezelési technológiák (például Kafka, RabbitMQ, Azure Service Bus) környezet automatikus propagálás jelenleg nem támogatott. Lehetőség azonban manuálisan hozhatna az ilyen forgatókönyvek például a `trackDependency` és `trackRequest` API-k, amellyel egy függőségi telemetria egy folyamatban lévő MIF-fájl a gyártó által üzenet és a kérelem egy fogyasztó által feldolgozott üzenet jelenti. Ebben az esetben is `operation_id` és `operation_parentId` kell propagálására, az üzenet tulajdonságai.

<a name="java-role-name"></a>
## <a name="role-name"></a>Szerepkör neve

Időnként érdemes testre is szabhatja összetevők nevéhez megjelennek a [Alkalmazástérkép](../../azure-monitor/app/app-map.md). Ehhez manuálisan állíthatja a `cloud_RoleName` tegye a következők egyikét:

Spring Boot az Application Insights Spring Boot starter használja, az egyetlen szükséges változás-e az egyéni nevet az alkalmazás beállítása a application.properties fájlban.

`spring.application.name=<name-of-app>`

A Spring Boot starter cloudRoleName automatikusan hozzárendeli a spring.application.name tulajdonság mezőben.

Ha használja a `WebRequestTrackingFilter`, a `WebAppNameContextInitializer` automatikusan beállítja az alkalmazás nevét. A konfigurációs fájl (applicationinsights.xml fájlt), adja hozzá a következő:
```XML
<ContextInitializers>
  <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
</ContextInitializers>
```

A felhőalapú környezet osztállyal:

```Java
telemetryClient.getContext().getCloud().setRole("My Component Name");
```

## <a name="next-steps"></a>További lépések

- [Egyéni telemetriát írhat](../../azure-monitor/app/api-custom-events-metrics.md)
- [Tudjon meg többet](../../azure-monitor/app/app-map.md#set-cloudrolename) cloud_RoleName más SDK-k beállítása.
- Előkészítheti az Application Insights a micro szolgáltatás összes összetevőjét. Tekintse meg [által támogatott platformok](../../azure-monitor/app/platforms.md).
- Lásd: [adatmodell](../../azure-monitor/app/data-model.md) Application Insights és modellhez.
- Ismerje meg, hogyan [bővítése és szűrőtelemetria](../../azure-monitor/app/api-filtering-sampling.md).
- [Application Insights konfigurációs – dokumentáció](configuration-with-applicationinsights-config.md)

