---
title: Az Azure Application Insights Telemetriai korreláció |} A Microsoft Docs
description: Application Insights telemetriai korreláció
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/09/2018
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 057e47c19f6405bec9e1fa80dd7097476876baa9
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645500"
---
# <a name="telemetry-correlation-in-application-insights"></a>Az Application Insights telemetriai korreláció

Minden logikai művelet mikroszolgáltatásokhoz világában alatt a szolgáltatás különböző összetevői szükségesek. Ezek az összetevők mindegyike külön használatával figyelhető [Application Insights](app-insights-overview.md). A webes alkalmazás-összetevő hitelesítési szolgáltató összetevőt felhasználói fiók hitelesítő adatainak érvényesítéséhez, és az API összetevővel való adatbeolvasás a Vizualizáció kommunikál. A kapcsolja be az API-összetevő más szolgáltatások adatokat kérdezhet le és használja a gyorsítótár-szolgáltató összetevőinek és értesítés a hívás a számlázási összetevő. Application Insights támogatja az elosztott telemetriai korreláció. Lehetővé teszi, hogy melyik összetevő felelős hibák vagy a teljesítmény romlását észlelése.

Ez a cikk ismerteti az adatmodellbe, több összetevő által küldött telemetriai adatainak az Application Insights segítségével. Ez fedezi a környezet propagálás módszerek és protokollok. Emellett ismerteti a különböző nyelveken és platformokon korrelációs fogalmak megvalósítását.

## <a name="telemetry-correlation-data-model"></a>Telemetriai korreláció adatmodell

Az Application Insights meghatározása egy [adatmodell](application-insights-data-model.md) elosztott telemetriai korreláció. Telemetriai társítani a logikai művelettel, minden szereplő telemetriai elem környezet mezője rendelkezik `operation_Id`. Ez az azonosító az elosztott nyomkövetést minden szereplő telemetriai elem által megosztott. Egyetlen lejátszóval származó telemetriai adatok elvesztését tehát még az továbbra is társíthat más összetevők által küldött telemetriát.

Elosztott logikai művelet jellemzően kisebb műveletek – a egyik által feldolgozott kérelmek egy készlete áll. Ezek a műveletek által meghatározott [telemetriai kérelem](application-insights-data-model-request-telemetry.md). Minden kéréstelemetria rendelkezik a saját `id` , amely globálisan egyedi módon azonosítja. Minden telemetriai adat - nyomkövetéseket, kivételek, stb. Ehhez a kérelemhez társított kell beállítania, és a `operation_parentId` értékével a kérés `id`.

Minden kimenő művelet, például a http-hívás egy másik összetevő által képviselt [– függőségi telemetria](application-insights-data-model-dependency-telemetry.md). Függőségek telemetriáját is meghatározza a saját `id` , amely globálisan egyedi. Kérelmek telemetriai adatai, a függőségi hívás által kezdeményezett használja, mint `operation_parentId`.

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

| ItemType   | név                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| oldalmegtekintés   | Tőzsdei lap                |              | STYz               | STYz         |
| függőség | GET/Home/készlet           | qJSXU        | STYz               | STYz         |
| kérelem    | GET Home/készlet            | KqKwlrSt9PA= | qJSXU              | STYz         |
| függőség | /Api/stock/value beolvasása      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Mostantól Ha a hívás `GET /api/stock/value` érdemes figyelembe venni, hogy a kiszolgáló identitását egy külső szolgáltatáshoz. Tehát beállíthatja `dependency.target` megfelelő mezőbe. Ha a külső szolgáltatás nem támogatja a figyelés – `target` az állomás neve, mint a szolgáltatás beállítása `stock-prices-api.com`. Azonban ha a szolgáltatás azonosítja magát egy előre meghatározott visszaadó HTTP-fejléc - `target` tartalmazza a felügyeltszolgáltatás-identitás, amely lehetővé teszi, hogy az Application Insights hozhat létre elosztott nyomkövetési ehhez lekérdezi a telemetriát a szolgáltatástól. 

## <a name="correlation-headers"></a>Korrelációs fejlécek

RFC javaslatot is dolgozunk a [korrelációs HTTP-protokoll](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v1.md). Ez a javaslat két fejlécek határozza meg:

- `Request-Id` a globálisan egyedi azonosító a hívás végrehajtása
- `Correlation-Context` – a végrehajtott műveletek közül sokat elosztott nyomkövetési tulajdonságainak neve érték párok gyűjteménye

A standard is határozza meg a két sémái `Request-Id` generování - és a hierarchikus. A egybesimított séma, amely egy jól ismert `Id` definiált kulcs a `Correlation-Context` gyűjtemény.

Az Application Insights határozza meg a [bővítmény](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) a korrelációs HTTP-protokoll esetében. Használ `Request-Context` érték párok propagálása a közvetlen hívó megjelölése vagy a hívott fél által használt tulajdonságok a gyűjtemény neve. Application Insights SDK-t használ a fejléc beállítása `dependency.target` és `request.source` mezőket.

## <a name="open-tracing-and-application-insights"></a>Nyissa meg a nyomkövetés és az Application Insights

[Nyissa meg a nyomkövetési](http://opentracing.io/) és az adatok a modellek úgy tűnik, Application Insights 

- `request`, `pageView` képez le **Span** az `span.kind = server`
- `dependency` a Maps **Span** az `span.kind = client`
- `id` az egy `request` és `dependency` képez le **Span.Id**
- `operation_Id` a Maps **TraceId**
- `operation_ParentId` a Maps **referencia** típusa `ChildOf`

Lásd: [adatmodell](application-insights-data-model.md) Application Insights és modellhez.

Lásd: [specifikáció](https://github.com/opentracing/specification/blob/master/specification.md) és [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) nyílt nyomkövetés fogalmak definícióját.


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
A [Application Insights Java SDK](app-insights-java-get-started.md) támogatja az automatikus korrelációs telemetriai kezdetének verziójával `2.0.0`. Automatikusan feltölti `operation_id` az összes telemetriai adatok (traces, kivételek, egyéni eseményeket stb.) kiadott kérelem hatókörén belül. Azt is gondoskodik propagálása a korrelációs fejléceket (lásd fent) a szolgáltatások közötti hívások HTTP-n keresztül, ha a [Java SDK ügynök](app-insights-java-agent.md) van konfigurálva. Megjegyzés: a korrelációs funkció csak a HTTP-alapú Apache-n keresztül végzett hívások támogatottak. Spring Rest-sablon vagy Feign használja, ha mindkét használható az Apache HTTP-ügyfél technikai részletek.

Több üzenetkezelési technológiák (például Kafka, RabbitMQ, Azure Service Bus) környezet automatikus propagálás jelenleg nem támogatott. Lehetőség azonban manuálisan hozhatna az ilyen forgatókönyvek például a `trackDependency` és `trackRequest` API-k, amellyel egy függőségi telemetria egy folyamatban lévő MIF-fájl a gyártó által üzenet és a kérelem egy fogyasztó által feldolgozott üzenet jelenti. Ebben az esetben is `operation_id` és `operation_parentId` kell propagálására, az üzenet tulajdonságai.

<a name="java-role-name"></a>
### <a name="role-name"></a>Szerepkör neve
Időnként érdemes testre is szabhatja összetevők nevéhez megjelennek a [Alkalmazástérkép](app-insights-app-map.md). Ehhez manuálisan állíthatja a `cloud_roleName` tegye a következők egyikét:

Keresztül egy telemetriainicializáló (összes telemetriai elem címkézett)
```Java
public class CloudRoleNameInitializer extends WebTelemetryInitializerBase {

    @Override
    protected void onInitializeTelemetry(Telemetry telemetry) {
        telemetry.getContext().getTags().put(ContextTagKeys.getKeys().getDeviceRoleName(), "My Component Name");
    }
  }
```
-N keresztül a [eszköz adatbáziskörnyezet osztályának](https://docs.microsoft.com/et-ee/java/api/com.microsoft.applicationinsights.extensibility.context._device_context) (csak a telemetriai elem címkézett)
```Java
telemetry.getContext().getDevice().setRoleName("My Component Name");
```

## <a name="next-steps"></a>További lépések

- [Egyéni telemetriát írhat](app-insights-api-custom-events-metrics.md)
- Előkészítheti az Application Insights a micro szolgáltatás összes összetevőjét. Tekintse meg [által támogatott platformok](app-insights-platforms.md).
- Lásd: [adatmodell](application-insights-data-model.md) Application Insights és modellhez.
- Ismerje meg, hogyan [bővítése és szűrőtelemetria](app-insights-api-filtering-sampling.md).
