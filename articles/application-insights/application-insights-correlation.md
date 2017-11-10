---
title: "Az Azure Application Insights Telemetria korrelációs |} Microsoft Docs"
description: "Application Insights telemetria korrelációs"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin
ms.openlocfilehash: e821a640d3d75e712c022bd681eb07b83da91911
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="telemetry-correlation-in-application-insights"></a>Az Application Insights telemetria korrelációs

A világ micro szolgáltatások minden logikai művelethez a szolgáltatás különböző összetevői végzett munkát. Ezeket az összetevőket külön felügyelhesse [Application Insights](app-insights-overview.md). A webes alkalmazás összetevő hitelesítési szolgáltató összetevő felhasználói fiók hitelesítő adatainak érvényesítéséhez, valamint az adatok lekérése a képi megjelenítéshez tartozó API összetevő kommunikál. Az API összetevőt a kapcsolja adatait kéri le más szolgáltatások és használhatja a gyorsítótár-szolgáltató összetevőinek és a számlázási összetevő a hívás kapcsolatos értesítés. Application Insights által támogatott elosztott telemetriai korrelációs. Ez lehetővé teszi, hogy melyik összetevő felelős a hibák vagy a teljesítmény romlását.

Ez a cikk ismerteti az adatmodell több összetevő által küldött telemetriai összefüggéseket Application Insights segítségével. A környezet propagálás módszerek és protokollok magában foglalja. Is magában foglalja a korrelációs fogalmak a különböző nyelvekhez és platformokhoz végrehajtását.

## <a name="telemetry-correlation-data-model"></a>Telemetria korrelációs adatmodell

Az Application Insights definiál egy [adatmodell](application-insights-data-model.md) elosztott telemetriai korrelációhoz. Telemetria a logikai művelethez társítható, minden telemetriai elemnek egy nevű környezet mező `operation_Id`. Ez az azonosító elosztott nyomkövetésben szereplő minden telemetriai elem által közösen. Egy rétegben a telemetriai adatok elvesztését használatát, így még továbbra is társíthat más összetevők által küldött telemetriai adatokat.

Elosztott logikai műveletet rendszerint kisebb műveletkészlet - összetevői által feldolgozott kérelmek áll. Ezek a műveletek által meghatározott [telemetriai kérelem](application-insights-data-model-request-telemetry.md). Minden – kéréstelemetria rendelkezik saját `id` , amely globálisan egyedi módon azonosítja. És az összes telemetriai adat - nyomkövetéseket, kivételek, stb. Ehhez a kérelemhez társított-et kell beállítania a `operation_parentId` értékével a kérés `id`.

Minden kimenő művelet, például egy másik összetevő által képviselt http hívása [– függőségi telemetria](application-insights-data-model-dependency-telemetry.md). – Függőségi telemetria is határozza meg a saját `id` globálisan egyedi. – Kéréstelemetria, ez a függőségi hívás által kezdeményezett használja ezt a szolgáltatást, `operation_parentId`.

A nézet elosztott logikai művelet használatával hozhat létre `operation_Id`, `operation_parentId`, és `request.id` rendelkező `dependency.id`. Ezek a mezők is meghatározhat a okozatiság telemetriai hívások sorrendjét.

Micro szolgáltatások környezet összetevők nyomkövetéseit előfordulhat, hogy keresse meg a különböző tárolók. Minden összetevő saját instrumentation kulcs lehet az Application Insights. Telemetriai adatainak lekérése a logikai műveletet, meg kell adatait kérdezi le minden tárolóból. Ha tárolók száma túl nagy, mutató hol következő rendelkeznie kell.

Az Application Insights adatmodell határozza meg a probléma megoldására két mező: `request.source` és `dependency.target`. Az első mezőben azonosítja az összetevő, amely a függőség kérelem kezdeményezett, és a második azonosítja, hogy melyik összetevő a választ küldött a függőségi hívás.


## <a name="example"></a>Példa

Vegyünk például egy alkalmazás készlet árak a külső API-jával készletek API hívása egy készlet aktuális piaci ára. A készlet árak alkalmazás is rendelkezik `Stock page` nyitható meg az ügyfél webes böngésző `GET /Home/Stock`. Az alkalmazás a készlet API lekérdezi egy HTTP-hívás használatával `GET /api/stock/value`.

Lekérdezés eredményeként kapott telemetriai elemezheti:

```
(requests | union dependencies | union pageViews) 
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Az eredmény nézet vegye figyelembe, hogy az összes telemetriai elemet a legfelső szintű közös a `operation_Id`. A lap – új egyedi azonosító ajax hívás esetén készült `qJSXU` hozzá van rendelve a függőségi telemetria pageView tartozó azonosító vettük `operation_ParentId`. Viszont a kiszolgálói kérelem használja ajax tartozó azonosítója `operation_ParentId`stb.

| ItemType   | név                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | A rendszer lap                |              | STYz               | STYz         |
| függőség | GET/Home/Stock           | qJSXU        | STYz               | STYz         |
| Kérelem    | GET otthoni/Stock            | KqKwlrSt9PA = | qJSXU              | STYz         |
| függőség | /Api/stock/value beolvasása      | bBrf2L7mm2g = | KqKwlrSt9PA =       | STYz         |

Most amikor a hívás `GET /api/stock/value` meg szeretné ismerni, hogy a kiszolgáló identitásának egy külső szolgáltatáshoz. Amelyen beállíthatja `dependency.target` megfelelően mezőben. Ha a külső szolgáltatás nem támogatja a figyelési - `target` értéke például a szolgáltatás állomásneve `stock-prices-api.com`. Azonban ha a szolgáltatás azonosítja magát vissza egy előre meghatározott HTTP-fejléc - `target` tartalmaz, amely lehetővé teszi, hogy a szolgáltatás telemetriai lekérdezésével elosztott nyomkövetési létrehozásához az Application Insights szolgáltatás identitásának. 

## <a name="correlation-headers"></a>Korrelációs fejlécek

RFC javaslatot dolgozunk a [korrelációs HTTP protokollt](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v1.md). Ez a javaslat meghatározza, hogy két fejléc:

- `Request-Id`a globálisan egyedi azonosítót a hívás végrehajtása
- `Correlation-Context`-portprofil az elosztott nyomkövetési tulajdonságok neve érték párok gyűjteménye

A standard is határozza meg a két sémái `Request-Id` generációs - és a hierarchikus. Az egyszerű sémával nincs jól ismert `Id` definiálva kulcs az `Correlation-Context` gyűjtemény.

Az Application Insights határozza meg a [bővítmény](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) a korreláció HTTP protokollt. Használja `Request-Context` name érték párok propagálására, a közvetlen hívónak vagy a hívott fél által használt tulajdonságok gyűjteménye. Application Insights SDK használja ezt a fejlécet beállítani `dependency.target` és `request.source` mezőket.

## <a name="open-tracing-and-application-insights"></a>Nyissa meg a nyomkövetés és az Application Insights

[Nyissa meg a nyomkövetési](http://opentracing.io/) és az Application Insights adatok modellek keresi 

- `request`, `pageView` van leképezve **Span** rendelkező`span.kind = server`
- `dependency`leképezve **Span** rendelkező`span.kind = client`
- `id`az egy `request` és `dependency` van leképezve **Span.Id**
- `operation_Id`leképezve **TraceId**
- `operation_ParentId`leképezve **hivatkozás** típusa`ChildOf`

Lásd: [adatmodell](application-insights-data-model.md) Application Insights-típusok és az adatok modell.

Lásd: [specification](https://github.com/opentracing/specification/blob/master/specification.md) és [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) nyitott nyomkövetés fogalmak definícióját.


## <a name="telemetry-correlation-in-net"></a>A .NET telemetriai korrelációs

Idővel .NET definiált számos módon összefüggéseket telemetriai és diagnosztikai naplókat. Nincs `System.Diagnostics.CorrelationManager` így nyomon követéséhez [LogicalOperationStack és ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). `System.Diagnostics.Tracing.EventSource`és Windows ETW határozza meg a metódus [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx). `ILogger`használja a [napló hatókörök](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). WCF és a Http átvitel közbeni "aktuális" környezetben propagálás fel.

Azokat a módszereket azonban automatikus elosztott nyomkövetés támogatásának engedélyezése nem. `DiagnosticsSource`módja a támogatja a gép korrelációs közötti automatikus. .NET-kódtárakra diagnosztika forrás támogatja, és engedélyezi a korrelációs környezetben, például a http szállítási keresztül gép terjesztésének közötti automatikus.

A [útmutató tevékenységben](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) diagnosztika forrás tevékenységek nyomon követése használatának alapjait ismerteti. 

Az ASP.NET Core 2.0 támogatja a HTTP-fejlécek és az új tevékenység indítása. 

`System.Net.HttpClient`Kezdő verzió `<fill in>` támogatja a HTTP-fejlécek korreláció automatikus injektálási és nyomon követni a tevékenységként http hívása.

Nincs új Http-modulja [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/) az ASP.NET klasszikus. Ebben a modulban telemetriai korrelációs DiagnosticsSource használatával. A bejövő kérelem fejlécek alapján tevékenység elindul. Emellett a különböző szakaszaiban a kérelem feldolgozása a telemetriai adatok ad eredményül. Még a az esetekben, amikor fut az IIS feldolgozó minden szakasza egy másik kezelése szálak.

Application Insights SDK kezdési verzió `2.4.0-beta1` gyűjthet, és rendelje hozzá azt az aktuális tevékenység DiagnosticsSource és a tevékenység használja. 

## <a name="next-steps"></a>Következő lépések

- [Egyéni telemetriai adatok írása](app-insights-api-custom-events-metrics.md)
- A bevezetni a micro Application Insights szolgáltatás összetevők. Tekintse meg [által támogatott platformok](app-insights-platforms.md).
- Lásd: [adatmodell](application-insights-data-model.md) Application Insights-típusok és az adatok modell.
- Megtudhatja, hogyan [kiterjesztése és a telemetriai adatok szűrése](app-insights-api-filtering-sampling.md).
