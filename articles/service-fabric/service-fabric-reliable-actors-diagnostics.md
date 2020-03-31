---
title: Szereplők diagnosztikája és monitorozása
description: Ez a cikk ismerteti a diagnosztikai és teljesítményfigyelési funkciók a Service Fabric reliable actors futásidejű, beleértve az események és az általa kibocsátott teljesítményszámlálók.
author: abhishekram
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: e6e9fb66368461e0d3ebdd2709f4ced0e796bea5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282327"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>A Reliable Actors diagnosztizálása és teljesítmény-figyelése
A Reliable Actors futásidejű bocsát [eventsource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) események és [teljesítményszámlálók.](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx) Ezek betekintést nyújtanak a futásidejű működésbe, és segítenek a hibaelhárításban és a teljesítményfigyelésben.

## <a name="eventsource-events"></a>EventSource-események
A Reliable Actors futásidejű EventSource-szolgáltató neve "Microsoft-ServiceFabric-Actors". Az eseményforrásból származó események a [Diagnosztikai események](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) ablakban jelennek meg, amikor az aktoralkalmazás [hibakeresést végez a Visual Studióban.](service-fabric-debugging-your-application.md)

Az EventSource-események gyűjtésében és/vagy megtekintésében segítséget segítő eszközök és technológiák például a [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [az Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), [a Szemantikus naplózás](https://msdn.microsoft.com/library/dn774980.aspx)és a Microsoft [TraceEvent Könyvtár.](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)

### <a name="keywords"></a>Kulcsszavak
A Reliable Actors EventSource-hoz tartozó összes esemény egy vagy több kulcsszóhoz van társítva. Ez lehetővé teszi az összegyűjtött események szűrését. A következő kulcsszóbitek vannak definiálva.

| Kicsit | Leírás |
| --- | --- |
| 0x1 |A Fabric Actors futásidejű működésének összegzése fontos események beállítása. |
| 0x2 |Aktor metódushívásokat leíró események készlete. További információt a [szereplőkről szóló bevezető témakörben talál.](service-fabric-reliable-actors-introduction.md) |
| 0x4 |Aktorállapothoz kapcsolódó események halmaza. További információt az [aktorállapot-kezelésről](service-fabric-reliable-actors-state-management.md)szóló témakörben talál. |
| 0x8 |Az aktor on-diondusegyidejűségével kapcsolatos események halmaza. További információt az [egyidejűségről](service-fabric-reliable-actors-introduction.md#concurrency)szóló témakörben talál. |

## <a name="performance-counters"></a>Teljesítményszámlálók
A Reliable Actors futásidejű határozza meg a következő teljesítményszámláló-kategóriák.

| Kategória | Leírás |
| --- | --- |
| Szolgáltatás fabric actor |Az Azure Service Fabric szereplőinek egyedi számlálói, például az aktorállapot mentéséhez szükséges idő |
| Szolgáltatásfabric aktor metódus |A Service Fabric-szereplők által megvalósított metódusokra jellemző számlálók, például az aktormetódusok meghívásának gyakran |

A fenti kategóriák mindegyike rendelkezik egy vagy több számlálóval.

A Windows operációs rendszerben alapértelmezés szerint elérhető [Windows Teljesítményfigyelő](https://technet.microsoft.com/library/cc749249.aspx) alkalmazás használható teljesítményszámláló-adatok gyűjtésére és megtekintésére. [Az Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) egy másik lehetőség a teljesítményszámláló-adatok gyűjtésére és feltöltésére az Azure-táblákba.

### <a name="performance-counter-instance-names"></a>Teljesítményszámláló-példánynevek
A fürt, amely nagy számú aktor szolgáltatások vagy aktor szolgáltatás partíciók lesz számos aktor teljesítmény számláló példányok. A teljesítményszámláló-példány nevei segíthetnek azonosítani azt a [partíciót](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) és aktormetódust (ha van ilyen), amelyhez a teljesítményszámláló-példány társítva van.

#### <a name="service-fabric-actor-category"></a>Szolgáltatásfabric aktor kategória
A kategória `Service Fabric Actor`esetében a számlálópéldányok nevei a következő formátumúak:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* a Service Fabric partícióazonosító karakterlánc-ábrázolása, amelyhez a teljesítményszámláló példány társítva van. A partícióazonosító guid azonosító, és karakterlánc-ábrázolása a [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) "D" formátumú metóduson keresztül jön létre.

*ActorRuntimeInternalID* egy 64 bites egész karakterlánc-ábrázolása, amelyet a Fabric Actors futásidejű a belső használatra generál. Ez szerepel a teljesítményszámláló-példány nevében, hogy biztosítsa annak egyediségét, és elkerülje a többi teljesítményszámláló-példány nevével való ütközést. A felhasználók nak nem szabad megpróbálni értelmezni a teljesítményszámláló példánynevének ezt a részét.

Az alábbi példa egy számlálópéldány nevét mutat be a `Service Fabric Actor` kategóriához tartozó számlálóhoz:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

A fenti `2740af29-78aa-44bc-a20b-7e60fb783264` példában a Service Fabric partícióazonosító karakterlánc-ábrázolása, és `635650083799324046` a 64 bites azonosító, amely a futásidejű belső használatra létrehozott.

#### <a name="service-fabric-actor-method-category"></a>Szolgáltatásfabric aktor metódus akta kategória
A kategória `Service Fabric Actor Method`esetében a számlálópéldányok nevei a következő formátumúak:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*A MethodName* annak az aktormetódusnak a neve, amelyhez a teljesítményszámláló példány társítva van. A metódus nevének formátuma a Fabric Actors futásidejű valamilyen logika alapján kerül meghatározásra, amely kiegyenlíti a név olvashatóságát a Windows teljesítményszámláló-példánynevének maximális hosszára vonatkozó korlátozásokkal.

*ActorsRuntimeMethodId* egy 32 bites egész karakterlánc-ábrázolása, amelyet a Fabric Actors futásidejű a belső használatra generál. Ez szerepel a teljesítményszámláló-példány nevében, hogy biztosítsa annak egyediségét, és elkerülje a többi teljesítményszámláló-példány nevével való ütközést. A felhasználók nak nem szabad megpróbálni értelmezni a teljesítményszámláló példánynevének ezt a részét.

*ServiceFabricPartitionID* a Service Fabric partícióazonosító karakterlánc-ábrázolása, amelyhez a teljesítményszámláló példány társítva van. A partícióazonosító guid azonosító, és karakterlánc-ábrázolása a [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) "D" formátumú metóduson keresztül jön létre.

*ActorRuntimeInternalID* egy 64 bites egész karakterlánc-ábrázolása, amelyet a Fabric Actors futásidejű a belső használatra generál. Ez szerepel a teljesítményszámláló-példány nevében, hogy biztosítsa annak egyediségét, és elkerülje a többi teljesítményszámláló-példány nevével való ütközést. A felhasználók nak nem szabad megpróbálni értelmezni a teljesítményszámláló példánynevének ezt a részét.

Az alábbi példa egy számlálópéldány nevét mutat be a `Service Fabric Actor Method` kategóriához tartozó számlálóhoz:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

A `ivoicemailboxactor.leavemessageasync` fenti példában a metódus `2` neve, a 32 bites azonosító t generált a `89383d32-e57e-4a9b-a6ad-57c6792aa521` runtime belső használatra, a string `635650083804480486` ábrázolása a Service Fabric partíció azonosítóját, és a 64 bites azonosítót generált a futásidejű belső használatra.

## <a name="list-of-events-and-performance-counters"></a>Események és teljesítményszámlálók listája
### <a name="actor-method-events-and-performance-counters"></a>Aktor metódusesemények és teljesítményszámlálók
A Reliable Actors futásidejű bocsát ki a következő események kapcsolatos [aktor metódusok.](service-fabric-reliable-actors-introduction.md)

| Esemény neve | Eseményazonosító | Szint | Kulcsszó | Leírás |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Részletes |0x2 |A szereplők futásidejű hamarosan meghívja az aktor metódust. |
| ActorMethodStop |8 |Részletes |0x2 |Az aktormetódus végrehajtása befejeződött. Ez azt, hogy a futásidejű aszinkron hívása az aktor metódus visszaadta, és az aktor metódus által visszaadott feladat befejeződött. |
| ActorMethodThrewException |9 |Figyelmeztetés |0x3 |Egy aktormetódus végrehajtása során kivétel történt, akár a runtime aszinkron hívása során az aktor metódushoz, akár az aktor metódus által visszaadott feladat végrehajtása során. Ez az esemény azt jelzi, hogy valamilyen hiba az aktor kód, amely vizsgálatot igényel. |

A Reliable Actors futásidejű közzéteszi a következő teljesítményszámlálók végrehajtásával kapcsolatos aktor metódusok.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Szolgáltatásfabric aktor metódus |Meghívás/mp |Az aktor szolgáltatásmetódus másodpercenkénti meghívásának száma |
| Szolgáltatásfabric aktor metódus |Átlagos ezredmásodperc meghívásonként |Az aktor szolgáltatásmetódus ezredmásodpercben való végrehajtásához szükséges idő |
| Szolgáltatásfabric aktor metódus |Másodpercenként idúló kivételek |Azaktor service metódus aktor szolgáltatás metódus a d. kivétel másodpercenkénti száma |

### <a name="concurrency-events-and-performance-counters"></a>Egyidejűségi események és teljesítményszámlálók
A Reliable Actors futásidejű bocsát ki a következő események kapcsolatos [egyidejűség](service-fabric-reliable-actors-introduction.md#concurrency).

| Esemény neve | Eseményazonosító | Szint | Kulcsszó | Leírás |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaiting |12 |Részletes |0x8 |Ez az esemény van írva elején minden új viszont egy színész. A függőben lévő aktor hívások számát tartalmazza, amelyek a kondionalapuló egyidejűséget kikényszerítő aktinalapult zárolás beszerzésére várnak. |

A Reliable Actors futásidejű közzéteszi a következő teljesítményszámlálók kapcsolatos egyidejűség.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Szolgáltatás fabric actor |# A színész kéri vár színész zár |A többalapú egyidejűséget kikényszerítő egyaktorzár beszerzésére váró függőben lévő aktorhívások száma |
| Szolgáltatás fabric actor |Átlagos ezredmásodperc zárolási várakozásonként |A turn-based egyidejűséget kikényszerítő aktinónkénti zárolás megszerzéséhez szükséges idő (ezredmásodpercben) |
| Szolgáltatás fabric actor |Átlagos ezredmásodperc aktazár tartott |Az az idő (ezredmásodpercben), amelynél a per-akta zárat tartják |

### <a name="actor-state-management-events-and-performance-counters"></a>Aktor állapotkezelési eseményei és teljesítményszámlálói
A Reliable Actors futásidejű bocsát ki a következő események kapcsolatos [aktor állapotkezelés.](service-fabric-reliable-actors-state-management.md)

| Esemény neve | Eseményazonosító | Szint | Kulcsszó | Leírás |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Részletes |0x4 |A színészek futásideje az aktorállapotának megmentésére készül. |
| ActorSaveStateStop |11 |Részletes |0x4 |A színészek futásidejű mentése befejeződött az aktorállapot mentése. |

A Reliable Actors futásidejű közzéteszi a következő teljesítményszámlálók az aktor állapotkezeléshez kapcsolódóan.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Szolgáltatás fabric actor |Átlagos ezredmásodperc mentési állapotonkénti műveletenként |Az aktor állapotának ezredmásodpercben való mentéséhez szükséges idő |
| Szolgáltatás fabric actor |Átlagos ezredmásodperc terhelési állapotonkénti műveletenként |Az aktor állapotának ezredmásodpercben való betöltéséhez szükséges idő |

### <a name="events-related-to-actor-replicas"></a>Aktor replikákkal kapcsolatos események
A Reliable Actors futásidejű bocsát ki a következő események kapcsolatos [aktor replikák.](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)

| Esemény neve | Eseményazonosító | Szint | Kulcsszó | Leírás |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Tájékoztató |0x1 |Az aktor replikája elsődleges szerepkörre módosított. Ez azt jelenti, hogy a partíció szereplői jönnek létre a replika belül. |
| ReplicaChangeRoleFromPrimary |2 |Tájékoztató |0x1 |Az akennek a kópia nem elsődleges szerepkörre módosította a szerepkört. Ez azt jelenti, hogy a partíció szereplői a továbbiakban nem jönnek létre a replikán belül. A replikán belül már létrehozott szereplőknek nem lesznek új kérelmek kézbesítése. A szereplők a folyamatban lévő kérelmek teljesítését követően semmisülnek meg. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Aktor aktiválási és inaktiválási eseményei és teljesítményszámlálói
A Reliable Actors futásidejű bocsát ki a következő események kapcsolatos [aktor aktiválása és kikapcsolása.](service-fabric-reliable-actors-lifecycle.md)

| Esemény neve | Eseményazonosító | Szint | Kulcsszó | Leírás |
| --- | --- | --- | --- | --- |
| ActorActivated (színészaktiválva) |5 |Tájékoztató |0x1 |Egy színész aktiválva. |
| ActorDeactivated |6 |Tájékoztató |0x1 |Egy színészt deaktiváltak. |

A Reliable Actors futásidejű közzéteszi a következő teljesítményszámlálók kapcsolatos aktor aktiválása és kikapcsolása.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Szolgáltatás fabric actor |Átlagos OnActivateAsync ezredmásodperc |Az OnActivateAsync metódus ezredmásodpercben történő végrehajtásához szükséges idő |

### <a name="actor-request-processing-performance-counters"></a>Aktor kérelem feldolgozási teljesítményszámlálók
Amikor egy ügyfél meghívja a metódust egy aktor proxyobjektumon keresztül, azt eredményezi, hogy egy kérésüzenetet küld a hálózaton keresztül az aktor szolgáltatás. A szolgáltatás feldolgozza a kérési üzenetet, és választ küld az ügyfélnek. A Reliable Actors futásidejű közzéteszi a következő teljesítményszámlálók kapcsolatos aktor kérelem feldolgozása.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Szolgáltatás fabric actor |# a fennálló kérelmek |A szolgáltatásban feldolgozott kérelmek száma |
| Szolgáltatás fabric actor |Kérésenkénti átlagos ezredmásodperc |A szolgáltatás által a kérelem feldolgozásához szükséges idő (ezredmásodpercben) |
| Szolgáltatás fabric actor |A kérelem deszerializálásának átlagos ezredmásodpercei |A szolgáltatáshoz való beérkezéskor az aktorkérésüzenet deszerializálásához szükséges idő (ezredmásodpercben) |
| Szolgáltatás fabric actor |A válasz szerializálásának átlagos ezredmásodpercei |Az aktor válaszüzenetszerializálásához szükséges idő (ezredmásodpercben) a válasz ügyfélnek való küldése előtt |

## <a name="next-steps"></a>További lépések
* [Hogyan használja a Megbízható szereplők a Service Fabric platformot?](service-fabric-reliable-actors-platform.md)
* [Aktor API-referenciadokumentáció](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [EventSource-szolgáltatók a PerfView-ban](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
