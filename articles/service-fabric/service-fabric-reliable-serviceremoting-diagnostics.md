---
title: Az Azure ServiceFabric diagnostics és figyelése |} Microsoft Docs
description: Ez a cikk ismerteti a Service Fabric megbízható ServiceRemoting futásidejű, például a teljesítményszámlálók azt által kibocsátott alkalmazásteljesítmény-figyelési szolgáltatásokkal.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: suchiagicha
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: suchiagicha
ms.openlocfilehash: d462ba0955a362c27b786ee6a5670eec20c52a22
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206419"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnosztika és teljesítményfigyelés a megbízható szolgáltatás távelérése
A megbízható ServiceRemoting futásidejű bocsát ki [teljesítményszámlálók](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Ezek a ServiceRemoting működéséről betekintést, és a hibaelhárítás és a Teljesítményfigyelő segítségével.


## <a name="performance-counters"></a>Teljesítményszámlálók
A megbízható ServiceRemoting futásidejű meghatározza, hogy a következő teljesítményszámláló-kategóriák:

| Kategória | Leírás |
| --- | --- |
| Service Fabric-szolgáltatás |Azure Service Fabric szolgáltatás távoli eljáráshívási kapcsolatos számlálókat például átlagos kérelem feldolgozásához szükséges idő |
| Service Fabric-szolgáltatási metódus |Módszerek kapcsolatos számlálókat által megvalósított Service Fabric Remoting Service, például egy metódus meghívja gyakoriságát. |

A megelőző kategóriák egy vagy több számlálóval rendelkezik.

A [Windows Teljesítményfigyelő](https://technet.microsoft.com/library/cc749249.aspx) a Windows operációs rendszerben alapértelmezés szerint elérhető alkalmazás segítségével összegyűjtése és megtekintése a teljesítményszámláló-adatokat. [Az Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) van lehetősége, hogy a teljesítményszámláló-adatok összegyűjtésére, majd ismét feltölteni a Azure-táblákban.

### <a name="performance-counter-instance-names"></a>Teljesítményszámláló-példányok nevei
Egy fürt, amely nagy számú ServiceRemoting szolgáltatások vagy a partíciók teljesítmény számlálópéldány nagy számú rendelkezik. A teljesítményszámláló-példányok nevei segíthet beazonosítani az adott partíció és metódus (ha van ilyen), hogy a teljesítményszámláló-példány társítva.

#### <a name="service-fabric-service-category"></a>Service Fabric-szolgáltatás kategória
A kategória `Service Fabric Service`, a teljesítményszámláló-példányok nevei szerepelnek a következő formátumban:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* , amelyhez a teljesítményszámláló-példány társítva van a Service Fabric Partícióazonosító karakterláncos ábrázolása. A Partícióazonosító GUID-nak, és a karakterlánc-ábrázolása keresztül jön létre a [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) formátummegadó "D" metódust.

*ServiceReplicaOrInstanceId* a Service Fabric replika/példány azonosítója, amelyhez a teljesítményszámláló-példány társítva van karakterláncos ábrázolása.

*ServiceRuntimeInternalID* egy 64 bites egész szám, a belső használatra a Fabric-szolgáltatás futtatókörnyezet által létrehozott karakterláncos ábrázolása. Ez a egyediségi és egyéb teljesítményszámláló-példányok nevei való ütközés elkerülése érdekében a teljesítményszámlálójának példánynevét tartalmazza. Felhasználók nem próbálja meg ezt a teljesítményszámlálójának példánynevét részét értelmezni.

Az alábbiakban egy példa egy számláló, amelyhez tartozik egy teljesítményszámlálójának példánynevét a `Service Fabric Service` kategória:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

Az előző példában `2740af29-78aa-44bc-a20b-7e60fb783264` a Service Fabric Partícióazonosító karakterláncos ábrázolása `635650083799324046` replika/InstanceId karakterláncos ábrázolása és `5008379932` használjon 64-bit-es Azonosítóját, amelynek az a futtatókörnyezet által belső jön létre.

#### <a name="service-fabric-service-method-category"></a>Service Fabric szolgáltatás metódus kategória
A kategória `Service Fabric Service Method`, a teljesítményszámláló-példányok nevei szerepelnek a következő formátumban:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* a metódust, amelyhez társítva van a teljesítményszámláló-példány neve. A metódus nevének formátuma néhány logika, amely korlátozza a teljesítményszámláló-példányok nevei legfeljebb a Windows nevű olvashatóságát elosztja a Fabric-szolgáltatás futásidejű alapján határozza meg.

*ServiceRuntimeMethodId* egy 32 bites egész szám, a belső használatra a Fabric-szolgáltatás futtatókörnyezet által generált karakterláncos ábrázolása. Ez a egyediségi és egyéb teljesítményszámláló-példányok nevei való ütközés elkerülése érdekében a teljesítményszámlálójának példánynevét tartalmazza. Felhasználók nem próbálja meg ezt a teljesítményszámlálójának példánynevét részét értelmezni.

*ServiceFabricPartitionID* , amelyhez a teljesítményszámláló-példány társítva van a Service Fabric Partícióazonosító karakterláncos ábrázolása. A Partícióazonosító GUID-nak, és a karakterlánc-ábrázolása keresztül jön létre a [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) formátummegadó "D" metódust.

*ServiceReplicaOrInstanceId* a Service Fabric replika/példány azonosítója, amelyhez a teljesítményszámláló-példány társítva van karakterláncos ábrázolása.

*ServiceRuntimeInternalID* egy 64 bites egész szám, a belső használatra a Fabric-szolgáltatás futtatókörnyezet által létrehozott karakterláncos ábrázolása. Ez a egyediségi és egyéb teljesítményszámláló-példányok nevei való ütközés elkerülése érdekében a teljesítményszámlálójának példánynevét tartalmazza. Felhasználók nem próbálja meg ezt a teljesítményszámlálójának példánynevét részét értelmezni.

Az alábbiakban egy példa egy számláló, amelyhez tartozik egy teljesítményszámlálójának példánynevét a `Service Fabric Service Method` kategória:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

Az előző példában `ivoicemailboxservice.leavemessageasync` a metódus neve, `2` jön létre a futtatókörnyezet belső használatra, 32 bites azonosítója `89383d32-e57e-4a9b-a6ad-57c6792aa521` a Service Fabric Partícióazonosító karakterláncos ábrázolása`635650083804480486` a Service Fabric replika/példány azonosítója karakterláncos ábrázolása és `5008380` a 64 bites azonosító esetében a futtatókörnyezet által belső használja.

## <a name="list-of-performance-counters"></a>Teljesítményszámlálók listája
### <a name="service-method-performance-counters"></a>Szolgáltatások metódus teljesítményszámlálói

A megbízható szolgáltatás futásideje teszi közzé a következő teljesítményszámlálókkal kapcsolatos metódusok végrehajtása.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Service Fabric-szolgáltatási metódus |Hívás/mp |Amelyet a módszerrel másodpercenkénti száma |
| Service Fabric-szolgáltatási metódus |Hívásonkénti átlagos idő ezredmásodpercben |Ezredmásodpercben a metódus végrehajtásához szükséges idő |
| Service Fabric-szolgáltatási metódus |Keletkezett kivétel/mp |Ennyiszer, hogy a szolgáltatás metódus kivételt / másodperc |

### <a name="service-request-processing-performance-counters"></a>Szolgáltatási kérelem feldolgozása teljesítményszámlálói
Amikor egy ügyfél keresztül a szolgáltatási proxy objektumhoz egy metódust hívja, a a hálózaton keresztül a távelérési szolgáltatásával küldött kérelemüzenet eredményez. A szolgáltatás feldolgozza a kérelmet üzenetet, és elküld egy választ, az ügyfélnek. A megbízható ServiceRemoting futásidejű teszi közzé a következő teljesítményszámlálókkal kapcsolatos szolgáltatási kérelem feldolgozása.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Service Fabric-szolgáltatás |Függőben lévő kérések száma |A szolgáltatás a feldolgozás alatt álló kérelmek száma |
| Service Fabric-szolgáltatás |Kérelmenkénti átlagos idő ezredmásodpercben |Igénybe vett idő (ezredmásodpercben) a szolgáltatás által a kérelmek feldolgozásához |
| Service Fabric-szolgáltatás |Kérelem deszerializálásának átlagos ideje ezredmásodpercben |(Ezredmásodpercben) deszerializálása szolgáltatás üzenete, ha a szolgáltatás fogadja igénybe vett idő |
| Service Fabric-szolgáltatás |Válasz szerializálásának átlagos ideje ezredmásodpercben |(Ezredmásodpercben), a szolgáltatás a szolgáltatás válaszüzenetet szerializálni az ügyfélnek a válasz elküldése előtt igénybe vett idő |

## <a name="next-steps"></a>További lépések
* [Mintakód](https://github.com/Azure/servicefabric-samples)
* [A PerfView EventSource szolgáltatók](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
