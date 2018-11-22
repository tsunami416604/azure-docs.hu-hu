---
title: Az Azure Service Fabric Application szint figyelése |} A Microsoft Docs
description: Tudnivalók az alkalmazás és szolgáltatás események és naplók monitorozása és diagnosztizálása az Azure Service Fabric-fürtökkel használt.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2018
ms.author: dekapur
ms.openlocfilehash: 532ad8dfeff9a14d3d1533669149e1e374ed4460
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52283230"
---
# <a name="application-and-service-level-logging"></a>Alkalmazás és szolgáltatás webhelyszintű naplózás

A kód szándékkal alapja a legtöbb más aspektusait, a szolgáltatások figyelése. Kialakítási az egyetlen módja, hogy valami nem megfelelő, azonosíthatja és diagnosztizálhatja a javításra szorul. Bár technikailag lehetséges szolgáltatáshoz való csatlakozáshoz hibakeresőt egy éles környezetben, akkor sem gyakori eljárásnak számít. Tehát hogy részletes rendszerállapot-adatok fontos.

Egyes termékek automatikusan a kód alkalmazásáról. Bár ezek a megoldások a is megfelelően működjön, a manuális instrumentation szinte mindig szükség. A végén rendelkeznie kell az alkalmazás forensically hibakeresése elegendő információt. Ez a dokumentum azt ismerteti, más megközelítést szándékkal, a kód és a egy megközelítést választani egy másikkal szemben.

Ezek a javaslatok használati példák: [naplózás hozzáadása a Service Fabric-alkalmazás](service-fabric-how-to-diagnostics-log.md).

## <a name="eventsource"></a>EventSource

Ha a Visual Studióban, egy sablonból hoz létre egy Service Fabric megoldást egy **EventSource**-származtatott osztály (**ServiceEventSource** vagy **ActorEventSource**) jön létre, . Sablon létrehozása, amelyben az alkalmazás vagy szolgáltatás eseményeket adhat hozzá. A **EventSource** neve **kell** egyedinek lennie, és érdemes lehet átnevezheti az alapértelmezett sablon karakterlánc értéket –&lt;megoldás&gt;-&lt;projekt &gt;. Több kellene **EventSource** tulajdonságdefiníciót használja ugyanazt a nevet egy problémát okoz a futási időben. Minden egyes megadott eseményének rendelkeznie kell egy egyedi azonosítója. Ha egy azonosító nem egyedi, futásidejű hiba történik. Egyes szervezetek külön fejlesztői csapatok közötti ütközések elkerülésével azonosítók értéktartományokat preassign. További információkért lásd: [előlegbekérő 's blog](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) vagy a [MSDN-dokumentáció](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## <a name="aspnet-core-logging"></a>ASP.NET Core-naplózás

Fontos alapos megtervezéséről, hogyan fogja a kód alkalmazásáról. A jobb oldali kialakítási terv segítségével elkerülhető a potenciálisan destabilizing a kódbázis, és ezután a kód reinstrument kellene. Kockázat csökkentéséhez, választhat egy rendszerállapot-tára például [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), amely a Microsoft ASP.NET Core része. ASP.NET Core rendelkezik egy [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) felület, amely a kiválasztott szolgáltatóval minimalizálhatók a hatása a meglévő kódot használhatja. Az ASP.NET Core Windows és Linux rendszereken is használhatja a kódot, és a teljes .NET-keretrendszer, ezért a kialakítási kódja szabványosított.

## <a name="application-insights-sdk"></a>Application Insights SDK letöltése

Az Application Insights-integrációja révén a Service Fabric beépített rendelkezik. Felhasználók hozzáadása az AI Service Fabric nuget-csomagok és fogadásához, adatok és a létrehozott naplók és a gyűjtött, az Azure Portalon. Emellett tanácsos adja hozzá a saját telemetriát, diagnosztizálása és keresése a saját alkalmazások és szolgáltatások és az alkalmazás részei, amelyek nyomon követése érdekében a leggyakrabban használt. A [TelemetryClient](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) osztályt az SDK szolgáltatással számos módon nyomon követéséhez az alkalmazások telemetriai adatokat. Tekintse meg a példa bemutatja, hogyan alakítsa ki, és adja hozzá az application insights az alkalmazáshoz a jelen oktatóanyagban szereplő [figyelése és diagnosztizálása a .NET-alkalmazás](service-fabric-tutorial-monitoring-aspnet.md)


## <a name="next-steps"></a>További lépések

Miután kiválasztotta a naplózás szolgáltatót, hogy az alkalmazások és szolgáltatások kialakítása, a naplók és események összesíteni kell, mielőtt azok bármely elemzési platform elküldött kell. További információ [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md), [eventflow segítségével](service-fabric-diagnostics-event-aggregation-eventflow.md), és [WAD](service-fabric-diagnostics-event-aggregation-wad.md) segít jobban megérteni az ajánlott beállítások egy része.
