---
title: Az Azure Service Fabric alkalmazásszintű figyelése |} Microsoft Docs
description: Tudnivalók az alkalmazás és szolgáltatás szintű esemény és figyelése és diagnosztizálása Azure Service Fabric-fürtök használt naplókat.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2018
ms.author: dekapur
ms.openlocfilehash: 258aac722aa1c94ecf2cbf0524a3e4b53b8a788c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="application-and-service-level-logging"></a>Alkalmazás és szolgáltatás webhelyszintű naplózás

A kód tagolása alapja legtöbb egyéb elemeinek a szolgáltatások figyelése. Instrumentation csak úgy is, hogy valami nem megfelelő, és javításra szorul diagnosztizálása érdekében. Bár technikailag lehetséges hibakereső kapcsolódni egy éles szolgáltatáshoz, nincs gyakori eljárásnak számít. Úgy hogy a WMI-adatok részletes fontos.

Egyes termékek automatikusan állíthatnak be a kódját. Bár ezek a megoldások is működőképesek, kézi instrumentation szinte mindig szükség. A végén az alkalmazás hibakeresése forensically elegendő információt kell rendelkeznie. Ez a dokumentum ismerteti a kódot, és ha egyik módszer kiválasztásához másikkal tagolása eltérő megközelítést.

## <a name="eventsource"></a>EventSource

A Service Fabric megoldást a Visual Studio sablonból létrehozásakor egy **EventSource**-osztályt (**ServiceEventSource** vagy **ActorEventSource**) jön létre . A sablon jön létre, az alkalmazás vagy szolgáltatás események adhat hozzá. A **EventSource** neve **kell** egyedinek lennie, és az alapértelmezett sablon karakterláncból értéket - érdemes nevezhető&lt;megoldás&gt;-&lt;projekt &gt;. Több **EventSource** tulajdonságdefiníciót használja ugyanazt a nevet a futási időben problémát okoz. Minden egyes meghatározott esemény egyedi azonosítóval kell rendelkeznie. Ha egy azonosító nem egyedi, futásidejű hiba történik. Egyes szervezetek preassign külön fejlesztési csapat közötti ütközések elkerülésével azonosítói értékeit a tartományait. További információkért lásd: [előlegbekérő 's blog](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) vagy a [az MSDN dokumentációjában tájékozódhat](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## <a name="aspnet-core-logging"></a>Az ASP.NET Core naplózása

Fontos alapos megtervezéséről, hogyan fogja beállíthatják a kódot. A jobb oldali instrumentation terv segítséget nyújt a potenciálisan destabilizing kódbázis, és hogy a kód reinstrument majd kellene elkerülése érdekében. Kockázatok csökkentése érdekében dönthet úgy, mint egy rendszerállapot-tára [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), amely Microsoft ASP.NET Core része. Az ASP.NET Core rendelkezik egy [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) felület, amely csak akkor használhatja a szolgáltató az Ön által választott, ugyanakkor minimalizálja a meglévő kódot hatással. Az ASP.NET Core Windows és Linux kódot is használhatja, és a teljes .NET-keretrendszer, ezért a rendszerállapot-kód szabványosított.

## <a name="choosing-a-logging-provider"></a>Egy naplózási szolgáltató kiválasztása

Ha az alkalmazás nagy teljesítményű, **EventSource** van általában egy jó módszer. **EventSource** *általában* kevesebb erőforrást használ, és az ASP.NET Core naplózás, illetve a rendelkezésre álló külső megoldások jobb teljesítményt.  Ez számos szolgáltatás, de ha a szolgáltatás-e a teljesítmény-központú, használja a problémát nem **EventSource** megfelelőbb választás lehet. Azonban ezek előnyök a naplózás, strukturált **EventSource** egy nagyobb befektetési a mérnöki csapat igényel. Ha lehetséges néhány naplózási beállítások gyors prototípus tegye, és válassza ki azt, amelyik az igényeinek leginkább megfelelő.

## <a name="next-steps"></a>További lépések

Miután kiválasztotta a naplózás szolgáltató is beállíthatják az alkalmazások és szolgáltatások, a naplók és események összesíthető ahhoz, azok bármely analysis platform lehet küldeni kell. További információ a [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) és [ÜVEGVATTA](service-fabric-diagnostics-event-aggregation-wad.md) jobb megértése érdekében ajánlott beállítások egy része.
