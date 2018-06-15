---
title: Az Azure Service Fabric állapotalapú Reliable Services diagnosztika |} Microsoft Docs
description: Diagnosztikai funkciók állapotalapú Reliable Services az Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 268ec61515f438fb7f98b6cef7a8ec60ba22e23f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212636"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>A Stateful Reliable Services diagnosztikai funkciói
Az Azure Service Fabric állapotalapú alkalmazások és szolgáltatások megbízható szolgáltatások StatefulServiceBase osztály bocsát ki [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) eseményeket, amelyek segítségével a szolgáltatás hibakeresési módját a futtatókörnyezet működő, és segítenek a hibaelhárításban betekintést.

## <a name="eventsource-events"></a>EventSource események
Az állapotalapú alkalmazások és szolgáltatások megbízható szolgáltatások StatefulServiceBase osztály az EventSource neve: "Microsoft-ServiceFabric-szolgáltatások." Ez az esemény forrásból származó események megjelennek a [diagnosztika események](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) ablakot, ha a szolgáltatás folyamatban van [indítja a Visual Studio](service-fabric-debugging-your-application.md).

Például az eszközök és technológiák segíti a összegyűjtése és/vagy EventSource események megtekintése [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), és a [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Események
| Esemény neve | Esemény azonosítója | Szint | Esemény leírása |
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

## <a name="performance-counters"></a>Teljesítményszámlálók
A Reliable Services futásidejű meghatározza, hogy a következő teljesítményszámláló-kategóriák:

| Kategória | Leírás |
| --- | --- |
| Service Fabric tranzakciós replikátor |Az Azure Service Fabric tranzakciós replikátor vonatkozó számlálók |

A Service Fabric tranzakciós replikátor használják a [megbízható állapotkezelője](service-fabric-reliable-services-reliable-collections-internals.md) belül egy adott készletét tranzakciók replikálásához [replikák](service-fabric-concepts-replica-lifecycle.md). 

A [Windows Teljesítményfigyelő](https://technet.microsoft.com/library/cc749249.aspx) a Windows operációs rendszerben alapértelmezés szerint elérhető alkalmazás segítségével összegyűjtése és megtekintése a teljesítményszámláló-adatokat. [Az Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) van lehetősége, hogy a teljesítményszámláló-adatok összegyűjtésére, majd ismét feltölteni a Azure-táblákban.

### <a name="performance-counter-instance-names"></a>Teljesítményszámláló-példányok nevei
Egy fürt, amely megbízható szolgáltatásoknak vagy szolgáltatás partíciók nagy számú nagyszámú tranzakciós replikátor teljesítmény számlálópéldány lesz. A teljesítményszámláló-példányok nevei segíthet beazonosítani az egyes [partíció](service-fabric-concepts-partitioning.md) és a szolgáltatás-replikával, amely a teljesítményszámláló-példány társítva van.

#### <a name="service-fabric-transactional-replicator-category"></a>Service Fabric tranzakciós replikátor kategória
A kategória `Service Fabric Transactional Replicator`, a teljesítményszámláló-példányok nevei szerepelnek a következő formátumban:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* , amelyhez a teljesítményszámláló-példány társítva van a Service Fabric Partícióazonosító karakterláncos ábrázolása. A Partícióazonosító GUID-nak, és a karakterlánc-ábrázolása keresztül jön létre [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) a formátummegadóval való ábrázoláshoz "D".

*ServiceFabricReplicaId* a replika megbízható szolgáltatás azonosítója. Másodpéldány-azonosító szerepel a egyediségi és egyéb teljesítmény számlálópéldány partícióra által generált való ütközés elkerülése a teljesítményszámlálójának példánynevét. Replikák és a megbízható szolgáltatások szerepkörük kapcsolatos részletes adatok található [Itt](service-fabric-concepts-replica-lifecycle.md).

A következő teljesítményszámláló-példány nevét a számláló a jellemző a `Service Fabric Transactional Replicator` kategória:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

Az előző példában `00d0126d-3e36-4d68-98da-cc4f7195d85e` a Service Fabric Partícióazonosító karakterláncos ábrázolása és `131652217797162571` a másodpéldány-azonosító.

### <a name="transactional-replicator-performance-counters"></a>Tranzakciós replikátor teljesítményszámlálói

A Reliable Services futásidejű bocsát ki a következő események a `Service Fabric Transactional Replicator` kategória

 Számláló neve | Leírás |
| --- | --- |
| Megkezdett tranzakciós művelet/másodperc | A másodpercenként létrehozott új írási tranzakciók száma.|
| Tranzakciós művelet/mp | Adja hozzá/frissítés/törlés műveletek száma másodpercenként megbízható gyűjtemények végre.|
| Átlagos Ürítse ki a késés (ms) | A másodpercenként tranzakciós replikátor által a lemezre kiürített bájtok száma |
| Szabályozott művelet/mp | Műveletek száma a tranzakciós replikátor szabályozás miatt által másodpercenként visszautasított. |
| Átlagos Ms/véglegesítés | Véglegesítés átlagos várakozási ideje ezredmásodpercben tranzakciónként |
| Átlagos Ürítse ki a késés (ms) | Átlagos időtartama ezredmásodpercben tranzakciós replikátor által kezdeményezett kiürítési lemezműveletekért |

## <a name="next-steps"></a>További lépések
[A PerfView EventSource szolgáltatók](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
