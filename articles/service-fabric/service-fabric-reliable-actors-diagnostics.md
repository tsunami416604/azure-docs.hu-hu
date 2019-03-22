---
title: Actors-diagnosztika és figyelés |} A Microsoft Docs
description: Ez a cikk ismerteti a diagnosztikai és a teljesítményfigyelési funkciókat a Service Fabric Reliable Actors-futtatókörnyezetben, többek között az események és teljesítményszámlálók, által kibocsátott.
services: service-fabric
documentationcenter: .net
author: abhishekram
manager: timlt
editor: vturecek
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: 888f9e04e048e3da4c9809ac4f8570f020030335
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57855835"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>A Reliable Actors diagnosztizálása és teljesítmény-figyelése
A Reliable Actors-futtatókörnyezet bocsát ki [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) események és [teljesítményszámlálók](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Ezek hogyan működik a futtatókörnyezet betekintést nyújtson, és a hibaelhárítás és alkalmazásteljesítmény-figyelés segítségével.

## <a name="eventsource-events"></a>EventSource események
A Reliable Actors-futtatókörnyezet EventSource szolgáltató neve "A Microsoft-ServiceFabric-Actors". Származó események megjelennek a [diagnosztikai események](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) ablakot, amikor folyamatban van, az actors-alkalmazás [hibakeresése a Visual Studióban](service-fabric-debugging-your-application.md).

Példa eszközöket és technológiákat, amelyek segítségével gyűjtése és/vagy EventSource események megtekintése a [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), [Szemantikus naplózás](https://msdn.microsoft.com/library/dn774980.aspx), és a [ A Microsoft TraceEvent könyvtár](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Kulcsszavak
A Reliable Actors EventSource tartozó összes esemény legalább egy kulcsszavas társítva. Ez lehetővé teszi, hogy a gyűjtött események szűrése. A következő kulcsszó bits vannak meghatározva.

| bit | Leírás |
| --- | --- |
| 0x1 |Állítsa be a fontos eseményekről, amelyek a Fabric Actors futásidejű művelet összefoglalásához. |
| 0x2 |Módszer aktorhívások leíró események készlete. További információkért lásd: a [actors bevezető üzenettémához](service-fabric-reliable-actors-introduction.md). |
| 0x4 |Aktorok állapotának kapcsolatos események készlete. További információkért lásd a témakör [aktor állapot-felügyeleti](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Az aktor fordulókra egyidejűségi kapcsolatos események készlete. További információkért lásd a témakör [egyidejűségi](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Teljesítményszámlálók
A Reliable Actors-futtatókörnyezet határozza meg a következő teljesítményszámláló-kategóriák.

| Kategória | Leírás |
| --- | --- |
| Service Fabric-aktor |Azure Service Fabric actors, pl. időt vett aktor állapotmentésének jellemző teljesítményszámlálók |
| Service Fabric-aktormetódus |Számlálók Service Fabric actors által megvalósított metódusok jellemző, például hogy milyen gyakran egy aktor metódus meghívásakor kerül |

A fenti kategóriákba mindegyike egy vagy több számlálóval rendelkezik.

A [Windows Performance Monitor](https://technet.microsoft.com/library/cc749249.aspx) alkalmazás, amely a Windows operációs rendszerben alapértelmezés szerint elérhető használható összegyűjtése és megtekintése a teljesítményszámláló-adatokat. [Az Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) van egy másik lehetőség a teljesítményszámlálók adatainak összegyűjtése, majd feltölteni a Azure-táblákat.

### <a name="performance-counter-instance-names"></a>Teljesítményszámlálók példány nevét
Egy fürt, amelyben sok aktorszolgáltatások vagy aktor service partíciók teljesítményét számláló aktorpéldányok nagy számú lesz. A teljesítményszámláló-példány neveket segítséget nyújthat az azonosító az adott [partíció](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) és aktormetódus (ha van ilyen), amelyhez a teljesítményszámláló-példány van társítva.

#### <a name="service-fabric-actor-category"></a>Service Fabric-Aktor kategória
A kategória `Service Fabric Actor`, a teljesítményszámlálók példány nevét a következő formátumban van:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* a Service Fabric partícióazonosító: a teljesítményszámláló-példány tartozó karakterlánca ábrázolása. A Partícióazonosító egy GUID Azonosítót, és annak karakterláncként keresztül jön létre a [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) formátummegadó "D" metódust.

*ActorRuntimeInternalID* egy 64 bites egész számot, a belső használatra a Fabric Actors modul által létrehozott karakterlánca ábrázolása. Ez a teljesítményszámláló példány nevének az egyediség biztosítása céljából, és ne ütközzenek más teljesítményszámlálók példány nevét tartalmazza. Felhasználók nem próbálja meg a teljesítményszámláló példány nevének ezen részét értelmezni.

Az alábbiakban egy példát egy teljesítményszámláló-példány neve, amelyhez tartozik egy számláló a a `Service Fabric Actor` kategória:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

A fentiek példában `2740af29-78aa-44bc-a20b-7e60fb783264` a Service Fabric Partícióazonosító karakterlánca ábrázolása és `635650083799324046` a 64 bites azonosítója, amely akkor jön létre, a futásidejű a belső használni.

#### <a name="service-fabric-actor-method-category"></a>Service Fabric-Aktormetódus kategória
A kategória `Service Fabric Actor Method`, a teljesítményszámlálók példány nevét a következő formátumban van:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* a aktormetódus, amelyhez társítva van a teljesítményszámláló-példány neve. Název metody formátumát határozza meg, amely elosztja az olvashatóság érdekében a neve a maximálisan megengedett a teljesítményszámláló-példány neveket a korlátozások a Windows Fabric Actors-futtatókörnyezetben egy logikai.

*ActorsRuntimeMethodId* egy 32 bites egész szám, a belső használatra a Fabric Actors modul által létrehozott karakterlánca ábrázolása. Ez a teljesítményszámláló példány nevének az egyediség biztosítása céljából, és ne ütközzenek más teljesítményszámlálók példány nevét tartalmazza. Felhasználók nem próbálja meg a teljesítményszámláló példány nevének ezen részét értelmezni.

*ServiceFabricPartitionID* a Service Fabric partícióazonosító: a teljesítményszámláló-példány tartozó karakterlánca ábrázolása. A Partícióazonosító egy GUID Azonosítót, és annak karakterláncként keresztül jön létre a [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) formátummegadó "D" metódust.

*ActorRuntimeInternalID* egy 64 bites egész számot, a belső használatra a Fabric Actors modul által létrehozott karakterlánca ábrázolása. Ez a teljesítményszámláló példány nevének az egyediség biztosítása céljából, és ne ütközzenek más teljesítményszámlálók példány nevét tartalmazza. Felhasználók nem próbálja meg a teljesítményszámláló példány nevének ezen részét értelmezni.

Az alábbiakban egy példát egy teljesítményszámláló-példány neve, amelyhez tartozik egy számláló a a `Service Fabric Actor Method` kategória:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

A fentiek példában `ivoicemailboxactor.leavemessageasync` metódus neve `2` jön létre a futtatókörnyezet belső használatra, 32 bites azonosítója `89383d32-e57e-4a9b-a6ad-57c6792aa521` a Service Fabric Partícióazonosító karakterlánca ábrázolása és `635650083804480486` jön létre a 64 bites azonosítója a a modul a belső használatra.

## <a name="list-of-events-and-performance-counters"></a>Események és teljesítményszámlálók listája
### <a name="actor-method-events-and-performance-counters"></a>Aktor metódus események és teljesítményszámlálók
A Reliable Actors-futtatókörnyezet az alábbi kapcsolatos eseményeket bocsát ki [aktor módszerek](service-fabric-reliable-actors-introduction.md).

| Esemény neve | Esemény azonosítója | Szint | Kulcsszó | Leírás |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Részletes |0x2 |Aktorok futásidejű arra készül, hogy az aktor metódus meghívása. |
| ActorMethodStop |8 |Részletes |0x2 |Egy aktor metódus végrehajtása befejeződött. Azt jelenti a futásidejű aszinkron hívás az aktor metódus visszatért, és az aktor metódus által visszaadott a feladat befejeződött. |
| ActorMethodThrewException |9 |Figyelmeztetés |0x3 |Kivétel történt az aktor metódus végrehajtása közben vagy a futásidejű aszinkron az aktor metódus hívása során vagy a feladat végrehajtása közben az aktor metódus adja vissza. Az esemény azt jelzi, hogy valamilyen hiba történt a az aktor kódot, amely a platformnaplók vizsgálata szükséges. |

A Reliable Actors-futtatókörnyezet az aktor módszerek végrehajtásával kapcsolatos alábbi teljesítményszámlálók tesz közzé.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Service Fabric-aktormetódus |Hívás/mp |Az aktor service metódus meghívásainak száma másodpercenként hányszor |
| Service Fabric-aktormetódus |Hívásonkénti átlagos idő ezredmásodpercben |Az aktorszolgáltatási metódus végrehajtásához felhasznált idő ezredmásodpercben |
| Service Fabric-aktormetódus |Keletkezett kivétel/mp |Az aktorszolgáltatási metódus száma másodpercenként kivételt okozott. |

### <a name="concurrency-events-and-performance-counters"></a>Egyidejűség-események és teljesítményszámlálók
A Reliable Actors-futtatókörnyezet az alábbi kapcsolatos eseményeket bocsát ki [egyidejűségi](service-fabric-reliable-actors-introduction.md#concurrency).

| Esemény neve | Esemény azonosítója | Szint | Kulcsszó | Leírás |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Részletes |0x8 |Ez az esemény minden egyes új kapcsolja az aktor elején van megírva. A függőben lévő beszerezni az aktoronkénti zárolásra fordulókra egyidejűségi megőrző váró aktorhívások számát tartalmazza. |

A Reliable Actors-modul a következő teljesítményszámlálókkal kapcsolatos egyidejűségi tesz közzé.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Service Fabric-aktor |Az aktor zárolására váró aktorhívások száma |Függőben lévő beszerezni az aktoronkénti zárolásra fordulókra egyidejűségi megőrző váró aktorhívások száma |
| Service Fabric-aktor |Zárolásra való várakozás átlagos ideje ezredmásodpercben |Ideje (ezredmásodpercben), amelyeket az egyidejűségi fordulókra kényszerít az aktoronkénti zárolásra |
| Service Fabric-aktor |Aktorzárolás fenntartásának átlagos ideje ezredmásodpercben |Az aktoronkénti zárolásra tárolt, amelynek időtartama (ezredmásodpercben) |

### <a name="actor-state-management-events-and-performance-counters"></a>Aktor felügyeleti események és teljesítményszámlálók
A Reliable Actors-futtatókörnyezet az alábbi kapcsolatos eseményeket bocsát ki [aktor állapot-felügyeleti](service-fabric-reliable-actors-state-management.md).

| Esemény neve | Esemény azonosítója | Szint | Kulcsszó | Leírás |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Részletes |0x4 |Aktorok futásidejű arra készül, hogy az aktor állapotmentésének időtartama. |
| ActorSaveStateStop |11 |Részletes |0x4 |Aktorok runtime az aktorok állapotának mentése befejeződött. |

A Reliable Actors-modul a következő teljesítményszámlálókkal kapcsolatos aktor állapot-felügyeleti tesz közzé.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Service Fabric-aktor |Állapotmentési műveletenként átlagosan eltelt ezredmásodpercek |Az aktor állapotmentésének időtartama ezredmásodpercben |
| Service Fabric-aktor |Állapotbetöltési művelet átlagos ideje ezredmásodpercben |Aktorállapot betöltésének ideje ezredmásodpercben |

### <a name="events-related-to-actor-replicas"></a>Szereplők replikáinak kapcsolódó eseményeket.
A Reliable Actors-futtatókörnyezet az alábbi kapcsolatos eseményeket bocsát ki [szereplők replikáinak](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| Esemény neve | Esemény azonosítója | Szint | Kulcsszó | Leírás |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Tájékoztató |0x1 |Aktor replika szerepkör elsődleges módosult. Ez azt jelenti, hogy a partíció a szereplők az ezen belül hozható létre. |
| ReplicaChangeRoleFromPrimary |2 |Tájékoztató |0x1 |Aktor replika nem elsődleges szerepkör módosult. Ez azt jelenti, hogy a partíció az aktorokat már nem jön létre az ezen belül. Ez a másodpéldány belül már létrehozott actors nincs új kérelem lesz elküldve. Az aktorok meg kell semmisíteni, a folyamatban lévő kérések után. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Aktor aktiválás és az inaktiválást események és teljesítményszámlálók
A Reliable Actors-futtatókörnyezet az alábbi kapcsolatos eseményeket bocsát ki [aktor aktiválás és az inaktiválást](service-fabric-reliable-actors-lifecycle.md).

| Esemény neve | Esemény azonosítója | Szint | Kulcsszó | Leírás |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Tájékoztató |0x1 |Egy aktor aktiválva lett. |
| ActorDeactivated |6 |Tájékoztató |0x1 |Egy aktor ki lett kapcsolva. |

A Reliable Actors-modul a következő teljesítményszámlálókkal kapcsolatos aktor aktiválás és az inaktiválást tesz közzé.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Service Fabric-aktor |OnActivateAsync metódus átlagos ideje ezredmásodpercben |Az OnActivateAsync metódus végrehajtásának átlagos ideje ezredmásodpercben |

### <a name="actor-request-processing-performance-counters"></a>Teljesítményszámlálók aktor a kérelem feldolgozása
Amikor egy ügyfél keresztül aktor proxy objektum egy metódust hív meg, egy kérelemüzenetet küld a hálózaton keresztül az aktorszolgáltatás eredményez. A szolgáltatás feldolgozza a kérést üzenetet, és visszaküldi a választ az ügyfél. A Reliable Actors-futtatókörnyezet tesz közzé a következő teljesítményszámlálókkal kapcsolatos aktor a kérelem feldolgozása.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Service Fabric-aktor |Függőben lévő kérések száma |A szolgáltatás a feldolgozás alatt álló kérelmek száma |
| Service Fabric-aktor |Kérelmenkénti átlagos idő ezredmásodpercben |Igénybe vett idő (ezredmásodpercben) a szolgáltatás által feldolgozott kérések |
| Service Fabric-aktor |Kérelem deszerializálásának átlagos ideje ezredmásodpercben |(Ezredmásodpercben) deszerializálása actor-kérelem üzenet érkezésekor Ez a szolgáltatás igénybe vett idő |
| Service Fabric-aktor |Válasz szerializálásának átlagos ideje ezredmásodpercben |Időtartam (ezredmásodpercben) az aktor válaszüzenetet, a szolgáltatás szerializálni az ügyfélnek a válasz elküldése előtt |

## <a name="next-steps"></a>További lépések
* [A Service Fabric platformot használja a Reliable Actors](service-fabric-reliable-actors-platform.md)
* [Aktor API dokumentációja](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [A PerfView EventSource szolgáltatók](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
