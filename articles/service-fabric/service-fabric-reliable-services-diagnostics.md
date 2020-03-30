---
title: Az Azure Service Fabric állapotalapú megbízható szolgáltatások diagnosztikája
description: Diagnosztikai funkció az állapotalapú megbízható szolgáltatásokhoz az Azure Service Fabricben
author: dkkapur
ms.topic: conceptual
ms.date: 8/24/2018
ms.author: dekapur
ms.openlocfilehash: 37162287e130b05dc41453c579b3a628ac878fca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282262"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>A Stateful Reliable Services diagnosztikai funkciói
Az Azure Service Fabric stateful reliable services statefulservicebase osztály [olyan EventSource-eseményeket](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) bocsát ki, amelyek a szolgáltatás hibakereséséhez, a futásidejű működésének elemzési adataival, valamint a hibaelhárítással kapcsolatos segítségre használhatók.

## <a name="eventsource-events"></a>EventSource-események
Az Állapotalapú Reliable Services StatefulServiceBase osztály EventSource-neve "Microsoft-ServiceFabric-Services". Az ebből az eseményforrásból származó események akkor jelennek meg a [Diagnosztikai események](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) ablakban, amikor a szolgáltatás hibakeresés alatt áll [a Visual Studióban.](service-fabric-debugging-your-application.md)

Az EventSource-események gyűjtésében és/vagy megtekintésében segítséget segítő eszközök és technológiák például a [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [az Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md)és a [Microsoft TraceEvent Library.](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)

## <a name="events"></a>Események
| Esemény neve | Eseményazonosító | Szint | Esemény leírása |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Tájékoztató |A szolgáltatás RunAsync feladatának indításakor kibocsátott |
| StatefulRunAsyncCancellation |2 |Tájékoztató |A szolgáltatás RunAsync feladatának megszakításakor kibocsátott |
| StatefulRunAsyncBefejezés |3 |Tájékoztató |A szolgáltatás RunAsync feladatának befejezésekor kibocsátott |
| StatefulRunAsyncSlowCancellation |4 |Figyelmeztetés |Akkor ad ki, ha a szolgáltatás RunAsync feladata túl sokáig tart a megszakítás befejezéséhez |
| StatefulRunAsyncFailure |5 |Hiba |Akkor szabadul meg, ha a service RunAsync-feladat kivételt okoz |

## <a name="interpret-events"></a>Események értelmezése
A StatefulRunAsyncInvocation, a StatefulRunAsyncCompletion és a StatefulRunAsyncCancellation események hasznosak a szolgáltatás írója számára a szolgáltatás életciklusának, valamint a szolgáltatás indításának, megszakításának vagy befejezésének időzítéséhez. Ez az információ akkor lehet hasznos, ha hibakeresés szolgáltatás problémák vagy a szolgáltatás életciklusának megismerése.

A szolgáltatásíróknak nagy figyelmet kell fordítaniuk a StatefulRunAsyncSlowCancellation és a StatefulRunAsyncFailure eseményekre, mert a szolgáltatással kapcsolatos problémákat jeleznek.

StatefulRunAsyncFailure akkor kerül kibocsátásra, amikor a service RunAsync() feladat kivételt okoz. Általában egy kivétel jelenik meg azt jelzi, hiba vagy hiba a szolgáltatásban. Emellett a kivétel okozza a szolgáltatás sikertelen, így átkerül egy másik csomópontra. Ez a művelet költséges lehet, és késleltetheti a bejövő kérelmeket a szolgáltatás áthelyezése közben. A szolgáltatásíróknak meg kell határozniuk a kivétel okát, és ha lehetséges, enyhíteniük kell azt.

A statefulRunAsyncSlowCancellation akkor kerül kibocsátásra, ha a RunAsync feladat megszakítási kérelme négy másodpercnél tovább tart. Ha egy szolgáltatás lemondása túl sokáig tart, hatással van a szolgáltatás egy másik csomóponton történő gyors újraindításának képességére. Ez a forgatókönyv hatással lehet a szolgáltatás általános rendelkezésre állására.

## <a name="performance-counters"></a>Teljesítményszámlálók
A Reliable Services futásidejű határozza meg a következő teljesítményszámláló-kategóriák:

| Kategória | Leírás |
| --- | --- |
| Szolgáltatásfabric tranzakciós replikátor |Az Azure Service Fabric tranzakciós replikátorának számlálói |
| Szolgáltatás háló tstore |Az Azure Service Fabric TStore-ra jellemző számlálók |

A Service Fabric tranzakciós replikátor a [Reliable State Manager](service-fabric-reliable-services-reliable-collections-internals.md) segítségével replikálja a tranzakciókat egy adott replikák egy adott [készletén](service-fabric-concepts-replica-lifecycle.md)belül.

A Service Fabric TStore egy olyan összetevő, amely et [a Reliable Collections](service-fabric-reliable-services-reliable-collections-internals.md) kulcs-érték párok tárolására és beolvasására használt.

A Windows operációs rendszerben alapértelmezés szerint elérhető [Windows Teljesítményfigyelő](https://technet.microsoft.com/library/cc749249.aspx) alkalmazás használható teljesítményszámláló-adatok gyűjtésére és megtekintésére. [Az Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) egy másik lehetőség a teljesítményszámláló-adatok gyűjtésére és feltöltésére az Azure-táblákba.

### <a name="performance-counter-instance-names"></a>Teljesítményszámláló-példánynevek
A fürt, amely nagyszámú megbízható szolgáltatások vagy megbízható szolgáltatáspartíciók lesz nagy számú tranzakciós replikátor teljesítményszámláló példányok. Ez a TStore teljesítményszámlálók esetében is így van, de megszorozza a használt megbízható szótárak és megbízható várólisták számát is. A teljesítményszámláló-példány nevei segíthetnek azonosítani azt a [partíciót](service-fabric-concepts-partitioning.md), szolgáltatásreplikát és állapotszolgáltatót a TStore esetében, amelyhez a teljesítményszámláló-példány társítva van.

#### <a name="service-fabric-transactional-replicator-category"></a>Szolgáltatásfabric tranzakciós replikátor kategória
A kategória `Service Fabric Transactional Replicator`esetében a számlálópéldányok nevei a következő formátumúak:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* a Service Fabric partícióazonosító karakterlánc-ábrázolása, amelyhez a teljesítményszámláló példány társítva van. A partícióazonosító guid azonosító, és karakterlánc-ábrázolása [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) a "D" formátum-megváltóval jön létre.

*ServiceFabricReplicaId* egy megbízható szolgáltatás adott replikájával társított azonosító. A replikaazonosító szerepel a teljesítményszámláló-példány nevében, hogy biztosítsa annak egyediségét, és elkerülje az ugyanazon partíció által létrehozott más teljesítményszámláló-példányokkal való ütközést. További részletek a replikákról és a megbízható szolgáltatásokban betöltött szerepükről [itt](service-fabric-concepts-replica-lifecycle.md)található .

A következő számlálópéldány neve jellemző a `Service Fabric Transactional Replicator` kategória alatti számlálókra:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

Az előző példában `00d0126d-3e36-4d68-98da-cc4f7195d85e` a Service Fabric partícióazonosító karakterlánc-ábrázolása, és `131652217797162571` a replika azonosítója.

#### <a name="service-fabric-tstore-category"></a>Service Fabric TStore kategória
A kategória `Service Fabric TStore`esetében a számlálópéldányok nevei a következő formátumúak:

`ServiceFabricPartitionId:ServiceFabricReplicaId:StateProviderId_PerformanceCounterInstanceDifferentiator_StateProviderName`

*ServiceFabricPartitionId* a Service Fabric partícióazonosító karakterlánc-ábrázolása, amelyhez a teljesítményszámláló példány társítva van. A partícióazonosító guid azonosító, és karakterlánc-ábrázolása [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) a "D" formátum-megváltóval jön létre.

*ServiceFabricReplicaId* egy megbízható szolgáltatás adott replikájával társított azonosító. A replikaazonosító szerepel a teljesítményszámláló-példány nevében, hogy biztosítsa annak egyediségét, és elkerülje az ugyanazon partíció által létrehozott más teljesítményszámláló-példányokkal való ütközést. További részletek a replikákról és a megbízható szolgáltatásokban betöltött szerepükről [itt](service-fabric-concepts-replica-lifecycle.md)található .

*A StateProviderId* egy megbízható szolgáltatáson belüli állami szolgáltatóhoz társított azonosító. Az állapotszolgáltató azonosítója szerepel a teljesítményszámláló példánynevében, hogy megkülönböztesse a TStore-t a másiktól.

*A PerformanceCounterInstanceDifferentiator* egy egy államszolgáltatón belüli teljesítményszámláló-példányhoz társított különbséget vevő azonosító. Ez a differenciáló szerepel a teljesítményszámláló-példány nevében, hogy biztosítsa annak egyediségét, és elkerülje az ugyanazon állapotszolgáltató által létrehozott más teljesítményszámláló-példányokkal való ütközést.

*StateProviderName* egy megbízható szolgáltatáson belüli állapotszolgáltatóhoz társított név. Az állapotszolgáltató neve szerepel a teljesítményszámláló példánynevében, hogy a felhasználók könnyen azonosíthassák, milyen állapotot biztosít.

A következő számlálópéldány neve jellemző a `Service Fabric TStore` kategória alatti számlálókra:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337_urn:MyReliableDictionary/dataStore`

Az előző `00d0126d-3e36-4d68-98da-cc4f7195d85e` példában a Service Fabric partícióazonosító karakterlánc-ábrázolása, `131652217797162571` a `142652217797162571` replika azonosítója, `1337` az állapotszolgáltató azonosítója, és a teljesítményszámláló-példány differenciálója. `urn:MyReliableDictionary/dataStore`annak az államszolgáltatónak a neve, amely `urn:MyReliableDictionary`a gyűjtemény adatait tárolja.

### <a name="transactional-replicator-performance-counters"></a>Tranzakciós replikátor teljesítményszámlálók

A Reliable Services futásidejű a következő `Service Fabric Transactional Replicator` eseményeket bocsátja ki a

 Számláló neve | Leírás |
| --- | --- |
| Txn-műveletek kezdete/mp | A másodpercenként létrehozott új írási tranzakciók száma.|
| Txn műveletek/mp | A megbízható gyűjtemények en végzett add/update/delete műveletek száma másodpercenként.|
| Napló-kiürítési bájtok/mp | A tranzakciós replikátor által a lemezre ürített bájtok másodpercenkénti száma |
| Szabályozott műveletek/mp | A tranzakciós replikátor által a szabályozás miatt másodpercenként elutasított műveletek száma. |
| Átlagos tranzakció ms/véglegesítés | Tranzakciónkénti átlagos véglegesítési késés ezredmásodpercben |
| Átlagos öblítési késleltetés (ms) | A tranzakciós replikátor által kezdeményezett lemezöblítési műveletek átlagos időtartama ezredmásodpercben |

### <a name="tstore-performance-counters"></a>TStore teljesítményszámlálók

A Reliable Services futásidejű a következő `Service Fabric TStore` eseményeket bocsátja ki a

 Számláló neve | Leírás |
| --- | --- |
| Cikkszám | Az üzletben lévő elemek száma.|
| Lemezméret | A tároló ellenőrzőpont-fájljainak teljes lemezmérete bájtban.|
| Ellenőrzőpont-fájl írási bájtjai/mp | A legutóbbi ellenőrzőpont-fájl másodpercenként írt bájtjainak száma.|
| Lemezátadási bájtok/mp másolása | Az olvasási (az elsődleges replika) vagy a másodlagos replika(másodlagos replikán) másodpercenkénti lemezbájtok száma a tároló példánya során.|

## <a name="next-steps"></a>További lépések
[EventSource-szolgáltatók a PerfView-ban](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
