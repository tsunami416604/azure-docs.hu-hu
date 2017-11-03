---
title: "Szereplője diagnosztika és figyelése |} Microsoft Docs"
description: "Ez a cikk ismerteti a diagnosztika és teljesítményfigyelés a Service Fabric Reliable Actors futásidejű, beleértve az események és teljesítményszámlálók által kibocsátott szolgáltatásai."
services: service-fabric
documentationcenter: .net
author: abhishekram
manager: timlt
editor: vturecek
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: 5fbef8a3fb32f4bc47856ef6c6b459ae389dd541
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>A Reliable Actors diagnosztizálása és teljesítmény-figyelése
A Reliable Actors futásidejű bocsát ki [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) események és [teljesítményszámlálók](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Ezek a futtatókörnyezet működéséről betekintést és a hibaelhárítás és a teljesítmény figyelése súgó.

## <a name="eventsource-events"></a>EventSource események
Az EventSource szolgáltató neve a Reliable Actors futásidejű: "Microsoft-ServiceFabric-szereplője". Ez az esemény forrásból származó események megjelennek a [diagnosztika események](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) ablakot, ha az aktor alkalmazás [indítja a Visual Studio](service-fabric-debugging-your-application.md).

Például az eszközök és technológiák segíti a összegyűjtése és/vagy EventSource események megtekintése [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), [szemantikai naplózás](https://msdn.microsoft.com/library/dn774980.aspx), és a [ Microsoft TraceEvent könyvtár](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Kulcsszavak
A megbízható szereplője EventSource tartozó összes esemény egy vagy több kulcsszót a társított. Ez lehetővé teszi, hogy az összegyűjtött események szűrése. A következő kulcsszó bits vannak definiálva.

| bit | Leírás |
| --- | --- |
| 0x1 |Fontos, hogy a művelet a háló szereplője futtatókörnyezet összefoglalója események készlet. |
| 0x2 |Aktor metódushívások leíró események készlete. További információkért lásd: a [szereplője bevezető című](service-fabric-reliable-actors-introduction.md). |
| 0x4 |Aktorállapot kapcsolatos események készlete. További információkért lásd a témakör [szereplő állapotkezelés](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Kapcsolja-alapú feldolgozási a szereplő a kapcsolódó eseményeket készlete. További információkért lásd a témakör [egyidejűségi](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Teljesítményszámlálók
A Reliable Actors futásidejű határozza meg a következő teljesítményszámláló-kategóriák.

| Kategória | Leírás |
| --- | --- |
| Service Fabric szereplő |Azure Service Fabric szereplője, pl. időt vett aktor állapotmentésének vonatkozó számlálók |
| Service Fabric-Aktormetódus |Számlálók a Service Fabric szereplője által megvalósított metódusok jellemző, pl. milyen gyakran egy aktormetódus meghívták |

A fenti kategóriák egy vagy több számlálóval rendelkezik.

A [Windows Teljesítményfigyelő](https://technet.microsoft.com/library/cc749249.aspx) a Windows operációs rendszerben alapértelmezés szerint elérhető alkalmazás segítségével összegyűjtése és megtekintése a teljesítményszámláló-adatokat. [Az Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) van lehetősége, hogy a teljesítményszámláló-adatok összegyűjtésére, majd ismét feltölteni a Azure-táblákban.

### <a name="performance-counter-instance-names"></a>Teljesítményszámláló-példányok nevei
Nagyszámú aktorszolgáltatások vagy aktor szolgáltatáspartíciók rendelkező fürtnek nagyszámú szereplő teljesítmény számlálópéldány lesz. A teljesítményszámláló-példányok nevei segíthet beazonosítani az egyes [partíció](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) és aktormetódus (ha van ilyen), amelyhez a teljesítményszámláló-példány társítva van.

#### <a name="service-fabric-actor-category"></a>Service Fabric szereplő kategória
A kategória `Service Fabric Actor`, a teljesítményszámláló-példányok nevei szerepelnek a következő formátumban:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* , amelyhez a teljesítményszámláló-példány társítva van a Service Fabric Partícióazonosító karakterláncos ábrázolása. A Partícióazonosító GUID-nak, és a karakterlánc-ábrázolása keresztül jön létre a [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) formátummegadó "D" metódust.

*ActorRuntimeInternalID* egy 64 bites egész szám, a belső használatra a háló szereplője futtatókörnyezet által létrehozott karakterláncos ábrázolása. Ez a egyediségi és egyéb teljesítményszámláló-példányok nevei való ütközés elkerülése érdekében a teljesítményszámlálójának példánynevét tartalmazza. Felhasználók nem próbálja meg ezt a teljesítményszámlálójának példánynevét részét értelmezni.

Az alábbiakban egy példa egy számláló, amelyhez tartozik egy teljesítményszámlálójának példánynevét a `Service Fabric Actor` kategória:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

A példában `2740af29-78aa-44bc-a20b-7e60fb783264` a Service Fabric Partícióazonosító karakterláncos ábrázolása és `635650083799324046` használjon 64-bit-es Azonosítóját, amelynek az a futtatókörnyezet által belső jön létre.

#### <a name="service-fabric-actor-method-category"></a>Service Fabric-Aktormetódus kategória
A kategória `Service Fabric Actor Method`, a teljesítményszámláló-példányok nevei szerepelnek a következő formátumban:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* a aktormetódus, amelyhez társítva van a teljesítményszámláló-példány neve. A metódus nevének formátuma néhány logika, amely korlátozza a teljesítményszámláló-példányok nevei legfeljebb a Windows nevű olvashatóságát elosztja a háló szereplője futásidejű alapján határozza meg.

*ActorsRuntimeMethodId* egy 32 bites egész szám, a belső használatra a háló szereplője futtatókörnyezet által generált karakterláncos ábrázolása. Ez a egyediségi és egyéb teljesítményszámláló-példányok nevei való ütközés elkerülése érdekében a teljesítményszámlálójának példánynevét tartalmazza. Felhasználók nem próbálja meg ezt a teljesítményszámlálójának példánynevét részét értelmezni.

*ServiceFabricPartitionID* , amelyhez a teljesítményszámláló-példány társítva van a Service Fabric Partícióazonosító karakterláncos ábrázolása. A Partícióazonosító GUID-nak, és a karakterlánc-ábrázolása keresztül jön létre a [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) formátummegadó "D" metódust.

*ActorRuntimeInternalID* egy 64 bites egész szám, a belső használatra a háló szereplője futtatókörnyezet által létrehozott karakterláncos ábrázolása. Ez a egyediségi és egyéb teljesítményszámláló-példányok nevei való ütközés elkerülése érdekében a teljesítményszámlálójának példánynevét tartalmazza. Felhasználók nem próbálja meg ezt a teljesítményszámlálójának példánynevét részét értelmezni.

Az alábbiakban egy példa egy számláló, amelyhez tartozik egy teljesítményszámlálójának példánynevét a `Service Fabric Actor Method` kategória:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

A példában `ivoicemailboxactor.leavemessageasync` a metódus neve, `2` jön létre a futtatókörnyezet belső használatra, 32 bites azonosítója `89383d32-e57e-4a9b-a6ad-57c6792aa521` a Service Fabric Partícióazonosító karakterláncos ábrázolása és `635650083804480486` előállított 64 bites azonosítója a futtatókörnyezet belső használatra.

## <a name="list-of-events-and-performance-counters"></a>Események és teljesítményszámlálók listája
### <a name="actor-method-events-and-performance-counters"></a>Aktor metódus események és teljesítményszámlálók
A Reliable Actors futásidejű bocsát ki a következő események, kapcsolódó [szereplő módszerek](service-fabric-reliable-actors-introduction.md).

| esemény neve | Eseményazonosító | Szint | Kulcsszó | Leírás |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Részletes |0x2 |Szereplője futásidejű arra készül, hogy egy aktormetódus meghívni. |
| ActorMethodStop |8 |Részletes |0x2 |Az aktor metódus végrehajtása befejeződött. Ez azt jelenti, hogy a futtatókörnyezet aszinkron hívás a aktormetódus adott vissza, és a tevékenység aktor metódus által visszaadott befejeződött. |
| ActorMethodThrewException |9 |Figyelmeztetés |0x3 |Kivétel történt egy aktormetódus végrehajtása közben a aktormetódus a futtatókörnyezet aszinkron hívás során vagy a feladat végrehajtása közben a aktormetódus által visszaadott. Ez az esemény azt jelzi, hogy valamilyen hiba történt a aktor kódot, amelyet a vizsgálat a. |

A Reliable Actors futásidejű teszi közzé a következő teljesítményszámlálókkal szereplő eljárások közül végrehajtásával kapcsolatos.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Service Fabric-Aktormetódus |Indítások/mp |Amelyet az aktorszolgáltatási metódus másodpercenkénti száma |
| Service Fabric-Aktormetódus |Hívásonkénti átlagos idő ezredmásodpercben |Az aktorszolgáltatási metódus végrehajtásához felhasznált idő ezredmásodpercben |
| Service Fabric-Aktormetódus |Kiváltott kivételek száma |Hogy az aktorszolgáltatási metódus száma másodpercenként kivételt váltott ki. |

### <a name="concurrency-events-and-performance-counters"></a>Párhuzamossági események és teljesítményszámlálók
A Reliable Actors futásidejű bocsát ki a következő események, kapcsolódó [egyidejűségi](service-fabric-reliable-actors-introduction.md#concurrency).

| esemény neve | Eseményazonosító | Szint | Kulcsszó | Leírás |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Részletes |0x8 |Ez az esemény írása a egy szereplő minden egyes új kapcsolja elején. A függőben lévő szerezni a kapcsolja-alapú feldolgozási megőrző aktoronkénti zárolásra váró aktorhívások számát tartalmazza. |

A Reliable Actors futásidejű közzéteszi a következő feldolgozási kapcsolódó teljesítményszámlálók.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Service Fabric szereplő |Az aktor zárolására váró aktorhívások száma |Függőben lévő szerezni a kapcsolja-alapú feldolgozási megőrző aktoronkénti zárolásra váró aktorhívások száma |
| Service Fabric szereplő |Zárolásra való várakozás átlagos ideje ezredmásodpercben |Ideje (ezredmásodpercben), amely képes kikényszeríteni kapcsolja-alapú feldolgozási az aktoronkénti zárolásra |
| Service Fabric szereplő |Aktorzárolás fenntartásának átlagos ideje ezredmásodpercben |Időtartama (ezredmásodpercben), amelynek az aktoronkénti zárolásra tárolt |

### <a name="actor-state-management-events-and-performance-counters"></a>Aktor állapot felügyeleti események és teljesítményszámlálók
A Reliable Actors futásidejű bocsát ki a következő események, kapcsolódó [szereplő állapotkezelés](service-fabric-reliable-actors-state-management.md).

| esemény neve | Eseményazonosító | Szint | Kulcsszó | Leírás |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Részletes |0x4 |Szereplője futásidejű arra készül, hogy az aktor állapotmentésének időtartama. |
| ActorSaveStateStop |11 |Részletes |0x4 |Szereplője futásidejű befejezte a aktorállapot mentése. |

A Reliable Actors futásidejű szereplő állapotkezelés kapcsolódó alábbi teljesítményszámlálók közzéteszi.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Service Fabric szereplő |Állapotmentési műveletenként átlagosan eltelt ezredmásodpercek |Az aktor állapotmentésének időtartama ezredmásodpercben |
| Service Fabric szereplő |Állapotbetöltési művelet átlagos ideje ezredmásodpercben |Aktorállapot betöltésének ideje ezredmásodpercben |

### <a name="events-related-to-actor-replicas"></a>Aktor replikák kapcsolódó eseményeket.
A Reliable Actors futásidejű bocsát ki a következő események, kapcsolódó [szereplő replikák](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| esemény neve | Eseményazonosító | Szint | Kulcsszó | Leírás |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Tájékoztató |0x1 |Aktor replika szerepkör elsődleges értékre módosult. Ez azt jelenti, hogy a partíció szereplője Ez a másodpéldány lesz hozhatók létre. |
| ReplicaChangeRoleFromPrimary |2 |Tájékoztató |0x1 |Aktor replika nem elsődleges szerepkör változott. Ez azt jelenti, hogy a partíció gyakrabban fog már nem hozhatók létre a replikára. Ez a másodpéldány belül már létrehozott szereplője az új kérelmek nem továbbítani. A szereplője meg kell semmisíteni, a folyamatban lévő kéréseit elvégzése után. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Aktor aktiválás és az inaktiválást események és teljesítményszámlálók
A Reliable Actors futásidejű bocsát ki a következő események, kapcsolódó [szereplő aktiválás és az inaktiválást](service-fabric-reliable-actors-lifecycle.md).

| esemény neve | Eseményazonosító | Szint | Kulcsszó | Leírás |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Tájékoztató |0x1 |Egy szereplő aktiválva lett. |
| ActorDeactivated |6 |Tájékoztató |0x1 |Egy szereplő inaktív. |

A Reliable Actors futásidejű teszi közzé a következő teljesítményszámlálókkal kapcsolatos szereplő aktiválás és az inaktiválást.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Service Fabric szereplő |Átlagos OnActivateAsync ideje ezredmásodpercben |Az OnActivateAsync metódus végrehajtásának átlagos ideje ezredmásodpercben |

### <a name="actor-request-processing-performance-counters"></a>Aktor Kérelemfeldolgozás teljesítményszámlálói
Amikor egy ügyfél keresztül szereplő proxy objektum metódus meghívja, egy üzenet az aktor szolgáltatáshoz a hálózaton keresztül küldött eredményez. A szolgáltatás feldolgozza a kérelmet üzenetet, és elküld egy választ, az ügyfélnek. A Reliable Actors futásidejű szereplő Kérelemfeldolgozás kapcsolódó alábbi teljesítményszámlálók közzéteszi.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Service Fabric szereplő |Függőben lévő kérések száma |A szolgáltatás a feldolgozás alatt álló kérelmek száma |
| Service Fabric szereplő |Kérelmenkénti átlagos idő ezredmásodpercben |Igénybe vett idő (ezredmásodpercben) a szolgáltatás által a kérelmek feldolgozásához |
| Service Fabric szereplő |Kérelem deszerializálásának átlagos ideje ezredmásodpercben |(Ezredmásodpercben) szereplő kérelemüzenet deszerializálása, ha a szolgáltatás fogadja igénybe vett idő |
| Service Fabric szereplő |Válasz szerializálásának átlagos ideje ezredmásodpercben |Az aktor válasz jelenik meg a szolgáltatás szerializálni az ügyfélnek a válasz elküldése előtt (ezredmásodpercben) szükséges idő |

## <a name="next-steps"></a>Következő lépések
* [A Service Fabric-platformról használatát a Reliable Actors](service-fabric-reliable-actors-platform.md)
* [Aktor API referenciadokumentációt](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Mintakód](https://github.com/Azure/servicefabric-samples)
* [A PerfView EventSource szolgáltatók](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
