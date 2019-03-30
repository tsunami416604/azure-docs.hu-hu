---
title: Az Azure ServiceFabric-diagnosztika és figyelés |} A Microsoft Docs
description: Ez a cikk ismerteti a Service Fabric Reliable ServiceRemoting futtatókörnyezetben, például teljesítményszámlálók, által kibocsátott teljesítmény figyelési szolgáltatásokat.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: suchiagicha
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: suchiagicha
ms.openlocfilehash: e4c698dc5aa8a07d09835adeac39db6ee35f720c
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666154"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnosztikai és a Reliable Service szolgáltatás táveléréséhez alkalmazásteljesítmény-figyelés
A megbízható ServiceRemoting futásidejű bocsát ki [teljesítményszámlálók](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Ezek hogyan működik a ServiceRemoting betekintést nyújtson, és a hibaelhárítás és alkalmazásteljesítmény-figyelés segítségével.


## <a name="performance-counters"></a>Teljesítményszámlálók
A megbízható ServiceRemoting futásidejű határozza meg a következő teljesítményszámláló-kategóriák:

| Kategória | Leírás |
| --- | --- |
| Service Fabric-szolgáltatás |Az Azure Service Fabric Service Remoting vonatkozó számlálókat például átlagos kérelem feldolgozásához szükséges idő |
| Service Fabric-szolgáltatási metódus |Módszerek vonatkozó számlálókat által megvalósított Fabric Service Remoting Service, például egy metódus meghívásainak gyakoriságát. |

A megelőző kategóriák egy vagy több számlálóval rendelkezik.

A [Windows Performance Monitor](https://technet.microsoft.com/library/cc749249.aspx) alkalmazás, amely a Windows operációs rendszerben alapértelmezés szerint elérhető használható összegyűjtése és megtekintése a teljesítményszámláló-adatokat. [Az Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) van egy másik lehetőség a teljesítményszámlálók adatainak összegyűjtése, majd feltölteni a Azure-táblákat.

### <a name="performance-counter-instance-names"></a>Teljesítményszámlálók példány nevét
Egy fürtöt, amely a nagy számú ServiceRemoting szolgáltatások vagy partíciókat sok teljesítményszámláló-példányok teljesítményét. A teljesítményszámláló-példány neveket segítséget nyújthat az azonosító az adott partíció- és szolgáltatási metódus (ha van ilyen), hogy a teljesítményszámláló-példány társítva.

#### <a name="service-fabric-service-category"></a>Service Fabric-szolgáltatás kategória
A kategória `Service Fabric Service`, a teljesítményszámlálók példány nevét a következő formátumban van:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* a Service Fabric partícióazonosító: a teljesítményszámláló-példány tartozó karakterlánca ábrázolása. A Partícióazonosító egy GUID Azonosítót, és annak karakterláncként keresztül jön létre a [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) formátummegadó "D" metódust.

*ServiceReplicaOrInstanceId* a Service Fabric replika és példány azonosítója, amelyhez társítva van a teljesítményszámláló-példány karakterlánca ábrázolása.

*ServiceRuntimeInternalID* egy 64 bites egész számot, a Fabric Service-futtatókörnyezet a belső használatra által létrehozott karakterlánca ábrázolása. Ez a teljesítményszámláló példány nevének az egyediség biztosítása céljából, és ne ütközzenek más teljesítményszámlálók példány nevét tartalmazza. Felhasználók nem próbálja meg a teljesítményszámláló példány nevének ezen részét értelmezni.

Az alábbiakban egy példát egy teljesítményszámláló-példány neve, amelyhez tartozik egy számláló a a `Service Fabric Service` kategória:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

Az előző példában `2740af29-78aa-44bc-a20b-7e60fb783264` a Service Fabric Partícióazonosító karakterlánca ábrázolása `635650083799324046` replika/InstanceId karakterlánca ábrázolása és `5008379932` a 64 bites azonosítója, amely akkor jön létre, a futásidejű a belső használni.

#### <a name="service-fabric-service-method-category"></a>Service Fabric-szolgáltatási metódus kategória
A kategória `Service Fabric Service Method`, a teljesítményszámlálók példány nevét a következő formátumban van:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* a metódust, amely társítva van a teljesítményszámláló-példány neve. A metódus nevének formátuma a Fabric Service-futtatókörnyezetben, amely elosztja a nevét, a Windows a teljesítményszámláló-példány neveket maximális hossza megkötésekkel rendelkező olvashatóságát néhány logika alapján határozza meg.

*ServiceRuntimeMethodId* egy 32 bites egész szám, a Fabric Service-futtatókörnyezet a belső használatra által létrehozott karakterlánca ábrázolása. Ez a teljesítményszámláló példány nevének az egyediség biztosítása céljából, és ne ütközzenek más teljesítményszámlálók példány nevét tartalmazza. Felhasználók nem próbálja meg a teljesítményszámláló példány nevének ezen részét értelmezni.

*ServiceFabricPartitionID* a Service Fabric partícióazonosító: a teljesítményszámláló-példány tartozó karakterlánca ábrázolása. A Partícióazonosító egy GUID Azonosítót, és annak karakterláncként keresztül jön létre a [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) formátummegadó "D" metódust.

*ServiceReplicaOrInstanceId* a Service Fabric replika és példány azonosítója, amelyhez társítva van a teljesítményszámláló-példány karakterlánca ábrázolása.

*ServiceRuntimeInternalID* egy 64 bites egész számot, a Fabric Service-futtatókörnyezet a belső használatra által létrehozott karakterlánca ábrázolása. Ez a teljesítményszámláló példány nevének az egyediség biztosítása céljából, és ne ütközzenek más teljesítményszámlálók példány nevét tartalmazza. Felhasználók nem próbálja meg a teljesítményszámláló példány nevének ezen részét értelmezni.

Az alábbiakban egy példát egy teljesítményszámláló-példány neve, amelyhez tartozik egy számláló a a `Service Fabric Service Method` kategória:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

Az előző példában `ivoicemailboxservice.leavemessageasync` metódus neve `2` jön létre a futtatókörnyezet belső használatra, 32 bites azonosítója `89383d32-e57e-4a9b-a6ad-57c6792aa521` a Service Fabric Partícióazonosító karakterlánca ábrázolása`635650083804480486` karakterlánca ábrázolása a Service Fabric replika és példány Azonosítóját és `5008380` esetében a futtatókörnyezet a belső generált 64-bit-es azonosító használni.

## <a name="list-of-performance-counters"></a>Teljesítményszámlálók listája
### <a name="service-method-performance-counters"></a>Service metódus teljesítményszámlálók

A Reliable Services modul a következő teljesítményszámlálókkal kapcsolatos szolgáltatás metódusok végrehajtása tesz közzé.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Service Fabric-szolgáltatási metódus |Hívás/mp |A szolgáltatás metódus meghívásainak száma másodpercenként hányszor |
| Service Fabric-szolgáltatási metódus |Hívásonkénti átlagos idő ezredmásodpercben |Ennyi ezredmásodpercig tart a metódus végrehajtásához szükséges idő |
| Service Fabric-szolgáltatási metódus |Keletkezett kivétel/mp |Hányszor, hogy a metódus másodpercenként kivételt okozott |

### <a name="service-request-processing-performance-counters"></a>Szolgáltatási kérelem feldolgozása teljesítményszámlálók
Amikor egy ügyfél keresztül a szolgáltatás proxy objektum egy metódust hív meg, a távelérés szolgáltatáshoz a hálózaton keresztül küldött kérelem üzenet eredményez. A szolgáltatás feldolgozza a kérést üzenetet, és visszaküldi a választ az ügyfél. A megbízható ServiceRemoting futásidejű tesz közzé a következő teljesítményszámlálókkal kapcsolatos szolgáltatási kérelem feldolgozása.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Service Fabric-szolgáltatás |Függőben lévő kérések száma |A szolgáltatás a feldolgozás alatt álló kérelmek száma |
| Service Fabric-szolgáltatás |Kérelmenkénti átlagos idő ezredmásodpercben |Igénybe vett idő (ezredmásodpercben) a szolgáltatás által feldolgozott kérések |
| Service Fabric-szolgáltatás |Kérelem deszerializálásának átlagos ideje ezredmásodpercben |(Ezredmásodpercben) szolgáltatás kérelemüzenet deszerializálása, amikor a szolgáltatás igénybe vett idő |
| Service Fabric-szolgáltatás |Válasz szerializálásának átlagos ideje ezredmásodpercben |Időtartam (ezredmásodpercben) a szolgáltatás a szolgáltatás válaszüzenet szerializálni az ügyfélnek a válasz elküldése előtt |

## <a name="next-steps"></a>További lépések
* [Mintakód](https://azure.microsoft.com/en-us/resources/samples/?service=service-fabric&sort=0)
* [A PerfView EventSource szolgáltatók](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
