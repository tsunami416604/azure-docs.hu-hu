---
title: Az Azure Service Fabric Stateful Reliable Services diagnosztikai |} A Microsoft Docs
description: Az Azure Service Fabric Stateful Reliable Services diagnosztikai funkciói
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: dekapur
ms.openlocfilehash: c3116b42e8696ae0bb2457a3c64320cb187b7447
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890712"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>A Stateful Reliable Services diagnosztikai funkciói
Az Azure Service Fabric Stateful Reliable Services StatefulServiceBase osztály bocsát ki [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) eseményeket, amelyek a szolgáltatás hibakeresése segítségével hogyan a futtatókörnyezet üzemeltetési, és hibaelhárítást betekintést nyújtson.

## <a name="eventsource-events"></a>EventSource események
A Stateful Reliable Services StatefulServiceBase osztály az eseményforrás neve: "A Microsoft-ServiceFabric-szolgáltatások." Származó események megjelennek a [diagnosztikai események](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) ablakot, amikor folyamatban van, a szolgáltatás [hibakeresése a Visual Studióban](service-fabric-debugging-your-application.md).

Példa eszközöket és technológiákat, amelyek segítségével gyűjtése és/vagy EventSource események megtekintése a [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), és a [Microsoft TraceEvent Library](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Események
| Esemény neve | Esemény azonosítója | Szint | Esemény leírása |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Tájékoztató |Amikor a szolgáltatás RunAsync feladat elindult |
| StatefulRunAsyncCancellation |2 |Tájékoztató |Amikor a szolgáltatás RunAsync feladat meg lett szakítva |
| StatefulRunAsyncCompletion |3 |Tájékoztató |Amikor a szolgáltatás RunAsync feladat befejeződött |
| StatefulRunAsyncSlowCancellation |4 |Figyelmeztetés |Amikor a szolgáltatás RunAsync feladat hajtsa végre a megszakítási túl sokáig tart |
| StatefulRunAsyncFailure |5 |Hiba |Amikor a szolgáltatás RunAsync feladat kivételt jelez. |

## <a name="interpret-events"></a>Események értelmezése
A szolgáltatás-író szolgáltatás, valamint amikor szolgáltatás elindul, megszakítása vagy befejezi az időzítési életciklusának megértése StatefulRunAsyncInvocation StatefulRunAsyncCompletion és StatefulRunAsyncCancellation események hasznosak. Ez az információ esetekben lehet hasznos szolgáltatás hibáinak vagy szolgáltatás életciklusának ismertetése.

Szolgáltatás írók elolvassa az események StatefulRunAsyncSlowCancellation és StatefulRunAsyncFailure kell fordítani, mert azt jelzik, hogy a szolgáltatással kapcsolatos problémák.

StatefulRunAsyncFailure bocsásson ki, amikor a szolgáltatás RunAsync() feladat kivételt jelez. Általában egy kivétel lépett fel azt jelzi, hogy egy hiba vagy a hiba a szolgáltatásban. Ezenkívül a kivétel rendelkezik, a szolgáltatás sikertelen lesz, így áthelyezése egy másik csomópont. A művelet költséges lehet, és közben a szolgáltatás áthelyezése késleltetheti a bejövő kérelmeket. Szolgáltatás írók meg kell állapítsa meg a kivétel okát, és ha lehetséges, csökkenti azt.

StatefulRunAsyncSlowCancellation bocsásson ki, amikor a RunAsync feladathoz megszakítási kérelem 4 másodpercnél hosszabb ideig tart. Ha a szolgáltatás megszakítás túl sokáig tart, a szolgáltatás azon képessége, hogy gyorsan indítható újra egy másik csomóponton befolyásolja. Ebben a forgatókönyvben hatással lehet a szolgáltatás általános rendelkezésre állását.

## <a name="performance-counters"></a>Teljesítményszámlálók
A Reliable Services-modul határozza meg a következő teljesítményszámláló-kategóriák:

| Kategória | Leírás |
| --- | --- |
| Service Fabric tranzakciós replikátor |Adott, az Azure Service Fabric tranzakciós replikátor számlálói |
| Service Fabric TStore |Az Azure Service Fabric TStore vonatkozó számlálók |

A Service Fabric tranzakciós replikátor által használt a [Reliable State Manager](service-fabric-reliable-services-reliable-collections-internals.md) belül egy adott halmazát tranzakciók replikálásához [replikák](service-fabric-concepts-replica-lifecycle.md).

A Service Fabric TStore-e egy összetevő, a használt [a Reliable Collections](service-fabric-reliable-services-reliable-collections-internals.md) tárolására, és a kulcs-érték párok beolvasása.

A [Windows Performance Monitor](https://technet.microsoft.com/library/cc749249.aspx) alkalmazás, amely a Windows operációs rendszerben alapértelmezés szerint elérhető használható összegyűjtése és megtekintése a teljesítményszámláló-adatokat. [Az Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) van egy másik lehetőség a teljesítményszámlálók adatainak összegyűjtése, majd feltölteni a Azure-táblákat.

### <a name="performance-counter-instance-names"></a>Teljesítményszámlálók példány nevét
Egy fürt, amelyben a reliable services és reliable Services partíció nagy számú nagy számú tranzakciós replikátor teljesítmény számlálópéldány lesz. Ez a helyzet akkor is TStore teljesítményszámlálókkal, de is van a megbízható szótárakban és megbízható várólisták száma szorozva. A teljesítményszámláló-példány neveket segítséget nyújthat az azonosító az adott [partíció](service-fabric-concepts-partitioning.md), szolgáltatás-replikát, és állapotszolgáltató TStore, amelyhez társítva van a teljesítményszámláló-példány esetén.

#### <a name="service-fabric-transactional-replicator-category"></a>Service Fabric tranzakciós replikátor kategória
A kategória `Service Fabric Transactional Replicator`, a teljesítményszámlálók példány nevét a következő formátumban van:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* a Service Fabric partícióazonosító: a teljesítményszámláló-példány tartozó karakterlánca ábrázolása. A Partícióazonosító egy GUID Azonosítót, és annak karakterláncként jön létre keresztül [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) a "D" specifikátor formátu.

*ServiceFabricReplicaId* replikájának egy reliable Services-azonosítója. A teljesítményszámláló példány nevének az egyediség biztosítása céljából, és ugyanazon a partíción által létrehozott egyéb teljesítmény számlálópéldányt való ütközés elkerülése Replikaazonosító tartalmazza. A reliable services és a replikák kapcsolatos további részletek találhatók [Itt](service-fabric-concepts-replica-lifecycle.md).

A következő teljesítményszámláló-példány nevét a jellemző, a számláló a `Service Fabric Transactional Replicator` kategória:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

Az előző példában `00d0126d-3e36-4d68-98da-cc4f7195d85e` a Service Fabric Partícióazonosító karakterlánca ábrázolása és `131652217797162571` a replika azonosítója.

#### <a name="service-fabric-tstore-category"></a>Service Fabric TStore-kategória
A kategória `Service Fabric TStore`, a teljesítményszámlálók példány nevét a következő formátumban van:

`ServiceFabricPartitionId:ServiceFabricReplicaId:ServiceFabricStateProviderId_PerformanceCounterInstanceDifferentiator`

*ServiceFabricPartitionId* a Service Fabric partícióazonosító: a teljesítményszámláló-példány tartozó karakterlánca ábrázolása. A Partícióazonosító egy GUID Azonosítót, és annak karakterláncként jön létre keresztül [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) a "D" specifikátor formátu.

*ServiceFabricReplicaId* replikájának egy reliable Services-azonosítója. A teljesítményszámláló példány nevének az egyediség biztosítása céljából, és ugyanazon a partíción által létrehozott egyéb teljesítmény számlálópéldányt való ütközés elkerülése Replikaazonosító tartalmazza. A reliable services és a replikák kapcsolatos további részletek találhatók [Itt](service-fabric-concepts-replica-lifecycle.md).

*ServiceFabricStateProviderId* belül egy reliable Services állapot szolgáltatóval azonosítója. Állapotazonosító szolgáltató tartalmazza a teljesítményszámláló példány nevének egy TStore megkülönböztetni egymástól.

*PerformanceCounterInstanceDifferentiator* egy megkülönböztető belül egy szolgáltatóját egy teljesítményszámláló-példány azonosítója. Ezt a különbséget jelent a teljesítményszámláló példány nevének az egyediség biztosítása céljából, és egyéb teljesítmény számlálópéldányt állapot ugyanaz a szolgáltató által létrehozott való ütközés elkerülése tartalmazza.

A következő teljesítményszámláló-példány nevét a jellemző, a számláló a `Service Fabric TStore` kategória:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337`

Az előző példában `00d0126d-3e36-4d68-98da-cc4f7195d85e` a Service Fabric Partícióazonosító karakterlánca ábrázolása `131652217797162571` a másodpéldány-azonosító van `142652217797162571` állapota szolgáltató azonosítója, és `1337` van a teljesítmény számláló példány különbséget jelent.

### <a name="transactional-replicator-performance-counters"></a>Tranzakciós replikátor teljesítményszámlálók

A Reliable Services modul a következő események alapján bocsát ki a `Service Fabric Transactional Replicator` kategória

 Számláló neve | Leírás |
| --- | --- |
| Megkezdett tranzakciós művelet/másodperc | A másodpercenként létrehozott új írási tranzakciók száma.|
| Tranzakciós művelet/mp | A reliable collections másodpercenként végrehajtott hozzáadása/frissítése/törlése műveletek száma.|
| Átl. Kiürítési késés (ms) | A másodpercenkénti tranzakciós replikátor által a lemezre kiürített bájtok száma |
| Szabályozott művelet/mp | Műveletek száma másodpercenként a szabályozás miatt tranzakciós replikátor által elutasították. |
| Átl. Ms/véglegesítés | Véglegesítés átlagos késése ezredmásodpercben tranzakciónként |
| Átl. Kiürítési késés (ms) | Lemezes flush ezredmásodpercben a tranzakciós replikátor által kezdeményezett műveletek átlagos időtartama |

### <a name="tstore-performance-counters"></a>TStore-teljesítményszámlálók

A Reliable Services modul a következő események alapján bocsát ki a `Service Fabric TStore` kategória

 Számláló neve | Leírás |
| --- | --- |
| Elemek száma | A tárolóban lévő elemek száma.|
| Lemezméret | A teljes lemez méretét, a tároló ellenőrzőpontfájljainak bájtban.|
| Ellenőrzőpontfájl írása (bájt/s) | Az utolsó ellenőrzőpont fájlja számára másodpercenként írt bájtok száma.|
| Lemezmásolás átviteli sebessége (bájt/s) | (Az elsődleges replikán) olvasása vagy írása (a másodlagos replika) másodpercenként egy tároló másolása során lemez bájtok száma.|

## <a name="next-steps"></a>További lépések
[A PerfView EventSource szolgáltatók](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
