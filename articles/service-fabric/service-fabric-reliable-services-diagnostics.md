---
title: "Az Azure Service Fabric állapotalapú Reliable Services diagnosztika |} Microsoft Docs"
description: "Diagnosztikai funkciók állapotalapú Reliable Services az Azure Service Fabric"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: edcaaaf8f1619082b33195aedf1fb1abf32e85b1
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2017
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>A Stateful Reliable Services diagnosztikai funkciói
Az Azure Service Fabric állapotalapú alkalmazások és szolgáltatások megbízható szolgáltatások StatefulServiceBase osztály bocsát ki [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) eseményeket, amelyek segítségével a szolgáltatás hibakeresési módját a futtatókörnyezet működő, és segítenek a hibaelhárításban betekintést.

## <a name="eventsource-events"></a>EventSource események
Az állapotalapú alkalmazások és szolgáltatások megbízható szolgáltatások StatefulServiceBase osztály az EventSource neve: "Microsoft-ServiceFabric-szolgáltatások." Ez az esemény forrásból származó események megjelennek a [diagnosztika események](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) ablakot, ha a szolgáltatás folyamatban van [indítja a Visual Studio](service-fabric-debugging-your-application.md).

Például az eszközök és technológiák segíti a összegyűjtése és/vagy EventSource események megtekintése [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), és a [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Események
| esemény neve | Eseményazonosító | Szint | Esemény leírása |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Tájékoztató |Amikor a szolgáltatás RunAsync feladat elindítva |
| StatefulRunAsyncCancellation |2 |Tájékoztató |Amikor a szolgáltatás RunAsync feladat meg lett szakítva |
| StatefulRunAsyncCompletion |3 |Tájékoztató |Amikor a szolgáltatás RunAsync feladat befejeződött. |
| StatefulRunAsyncSlowCancellation |4 |Figyelmeztetés |Amikor a szolgáltatás RunAsync feladat megszakításának befejezéséhez túl sokáig tart |
| StatefulRunAsyncFailure |5 |Hiba |Amikor a szolgáltatás RunAsync tevékenység kivételt jelez. |

## <a name="interpret-events"></a>Események értelmezése
A szolgáltatás-író szolgáltatás, valamint amikor egy szolgáltatás elindul, visszavonja vagy befejezi a időzítést, a felügyeleti életciklus megismerése érdemes StatefulRunAsyncInvocation StatefulRunAsyncCompletion és StatefulRunAsyncCancellation eseményeket is. Ez az információ esetekben lehet hasznos szolgáltatás hibáinak feltárására, vagy a szolgáltatás életciklusának megértése.

Szolgáltatás írók kell figyelmesen elolvassa StatefulRunAsyncSlowCancellation és StatefulRunAsyncFailure események, mert a szolgáltatás problémákat jeleznek.

Amikor a RunAsync() feladat kivételt StatefulRunAsyncFailure kibocsátott. Általában egy kivétel lépett fel hiba vagy probléma a szolgáltatás jelzi. Emellett a kivétel hatására a szolgáltatás leállását, ezért egy másik csomópont áthelyezés. Ez a művelet költséges, és a bejövő kérelmek késleltetheti, ha a szolgáltatás áthelyezte. Szolgáltatás írók határozza meg, az okot a kivétel és, ha lehetséges, mérsékelni.

StatefulRunAsyncSlowCancellation kibocsátott, amikor a RunAsync feladat megszakítási kérelmet a 4 másodpercnél hosszabb időbe telik. Amikor egy szolgáltatás túl lassan törlése befejeződik, az hatással van a a szolgáltatás gyorsan újra kell indítani egy másik csomópontra. Ebben a forgatókönyvben hatással lehet a szolgáltatás rendelkezésre állását.

## <a name="next-steps"></a>Következő lépések
[A PerfView EventSource szolgáltatók](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
