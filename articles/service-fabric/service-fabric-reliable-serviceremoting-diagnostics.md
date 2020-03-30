---
title: Azure ServiceFabric diagnosztika és figyelés
description: Ez a cikk ismerteti a szolgáltatásfabric megbízható serviceRemoting futásidejű teljesítményfigyelési funkciók, például az általa kibocsátott teljesítményszámlálók.
author: suchiagicha
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: pepogors
ms.openlocfilehash: 31095a619fc4d756fa4ef9c29691d1d511d59ece
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282275"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnosztika és teljesítményfigyelés a megbízható szolgáltatás-átmatolók számára
A Megbízható ServiceRemoting futásidő [teljesítményszámlálókat](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx)bocsát ki. Ezek betekintést nyújtanak a ServiceRemoting működésébe, és segítenek a hibaelhárításban és a teljesítményfigyelésben.


## <a name="performance-counters"></a>Teljesítményszámlálók
A Megbízható ServiceRemoting futásidő a következő teljesítményszámláló-kategóriákat határozza meg:

| Kategória | Leírás |
| --- | --- |
| Szolgáltatás fabric szolgáltatás |Az Azure Service Fabric szolgáltatás hívásátorának számlálói, például a kérés feldolgozásához szükséges átlagos idő |
| Szolgáltatásfabric szolgáltatás metódusa |A Service Fabric-remoting szolgáltatás által megvalósított metódusok számlálói, például a szolgáltatásmetódusok meghívásának gyakran |

Az előző kategóriák mindegyike rendelkezik egy vagy több számlálóval.

A Windows operációs rendszerben alapértelmezés szerint elérhető [Windows Teljesítményfigyelő](https://technet.microsoft.com/library/cc749249.aspx) alkalmazás használható teljesítményszámláló-adatok gyűjtésére és megtekintésére. [Az Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) egy másik lehetőség a teljesítményszámláló-adatok gyűjtésére és feltöltésére az Azure-táblákba.

### <a name="performance-counter-instance-names"></a>Teljesítményszámláló-példánynevek
A fürt, amely nagyszámú ServiceRemoting szolgáltatások vagy partíciók nagy számú teljesítményszámláló példányok. A teljesítményszámláló-példány nevei segíthetnek a teljesítményszámláló-példány társított adott partíciójának és szolgáltatásmetódusának azonosításában.

#### <a name="service-fabric-service-category"></a>Szolgáltatásfabric szolgáltatás kategória
A kategória `Service Fabric Service`esetében a számlálópéldányok nevei a következő formátumúak:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* a Service Fabric partícióazonosító karakterlánc-ábrázolása, amelyhez a teljesítményszámláló példány társítva van. A partícióazonosító guid azonosító, és karakterlánc-ábrázolása a [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) "D" formátumú metóduson keresztül jön létre.

*ServiceReplicaOrInstanceId* a Service Fabric Replika/Példányazonosító karakterlánc-ábrázolása, amelyhez a teljesítményszámláló példány társítva van.

*ServiceRuntimeInternalID* egy 64 bites egész karakterlánc-ábrázolása, amelyet a Fabric Service futásidejű belső használatra hoz létre. Ez szerepel a teljesítményszámláló-példány nevében, hogy biztosítsa annak egyediségét, és elkerülje a többi teljesítményszámláló-példány nevével való ütközést. A felhasználók nak nem szabad megpróbálni értelmezni a teljesítményszámláló példánynevének ezt a részét.

Az alábbi példa egy számlálópéldány nevét mutat be a `Service Fabric Service` kategóriához tartozó számlálóhoz:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

Az előző példában `2740af29-78aa-44bc-a20b-7e60fb783264` a Service Fabric partícióazonosító karakterlánc-ábrázolása, a Replika/InstanceId karakterlánc-ábrázolása, `635650083799324046` és `5008379932` a 64 bites azonosító, amely a futásidejű belső használatra jön létre.

#### <a name="service-fabric-service-method-category"></a>Szolgáltatásfabric szolgáltatásmetódus kategóriája
A kategória `Service Fabric Service Method`esetében a számlálópéldányok nevei a következő formátumúak:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*A Metódusnév* annak a szolgáltatásmetódusnak a neve, amelyhez a teljesítményszámláló példány társítva van. A metódus nevének formátuma a Fabric Service futásidejű valamilyen logikája alapján kerül meghatározásra, amely kiegyenlíti a név olvashatóságát a Windows teljesítményszámláló-példánynevének maximális hosszára vonatkozó korlátozásokkal.

*ServiceRuntimeMethodId* egy 32 bites egész karakterlánc-ábrázolása, amelyet a Fabric Service futásideje hoz létre belső használatra. Ez szerepel a teljesítményszámláló-példány nevében, hogy biztosítsa annak egyediségét, és elkerülje a többi teljesítményszámláló-példány nevével való ütközést. A felhasználók nak nem szabad megpróbálni értelmezni a teljesítményszámláló példánynevének ezt a részét.

*ServiceFabricPartitionID* a Service Fabric partícióazonosító karakterlánc-ábrázolása, amelyhez a teljesítményszámláló példány társítva van. A partícióazonosító guid azonosító, és karakterlánc-ábrázolása a [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) "D" formátumú metóduson keresztül jön létre.

*ServiceReplicaOrInstanceId* a Service Fabric Replika/Példányazonosító karakterlánc-ábrázolása, amelyhez a teljesítményszámláló példány társítva van.

*ServiceRuntimeInternalID* egy 64 bites egész karakterlánc-ábrázolása, amelyet a Fabric Service futásidejű belső használatra hoz létre. Ez szerepel a teljesítményszámláló-példány nevében, hogy biztosítsa annak egyediségét, és elkerülje a többi teljesítményszámláló-példány nevével való ütközést. A felhasználók nak nem szabad megpróbálni értelmezni a teljesítményszámláló példánynevének ezt a részét.

Az alábbi példa egy számlálópéldány nevét mutat be a `Service Fabric Service Method` kategóriához tartozó számlálóhoz:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

Az `ivoicemailboxservice.leavemessageasync` előző példában a metódus `2` neve, a 32 bites azonosító t generált a `89383d32-e57e-4a9b-a6ad-57c6792aa521` runtime belső használatra, a string`635650083804480486` ábrázolása a Service Fabric partíció azonosító, `5008380` a string ábrázolása a Service Fabric Replika/Példányazonosító, és a 64 bites azonosító t a futásidejű belső használatra létrehozott.

## <a name="list-of-performance-counters"></a>Teljesítményszámlálók listája
### <a name="service-method-performance-counters"></a>A szolgáltatásmetódus teljesítményszámlálói

A Megbízható szolgáltatás futásidejű közzéteszi a következő teljesítményszámlálók a szolgáltatás módszerek végrehajtásával kapcsolatos.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Szolgáltatásfabric szolgáltatás metódusa |Meghívás/mp |A szolgáltatásmetódus másodpercenkénti meghívásának száma |
| Szolgáltatásfabric szolgáltatás metódusa |Átlagos ezredmásodperc meghívásonként |A szolgáltatásmetódus ezredmásodpercben való végrehajtásához szükséges idő |
| Szolgáltatásfabric szolgáltatás metódusa |Másodpercenként idúló kivételek |Azon alkalmak száma, ahányszor a szolgáltatásmetódus kivételt okozott másodpercenként |

### <a name="service-request-processing-performance-counters"></a>Szolgáltatáskérés-feldolgozási teljesítményszámlálók
Amikor egy ügyfél meghívja a metódust egy szolgáltatás proxyobjektumon keresztül, azt eredményezi, hogy egy kérésüzenetet küld a hálózaton keresztül a kapcsolathívási szolgáltatásnak. A szolgáltatás feldolgozza a kérési üzenetet, és választ küld az ügyfélnek. A Megbízható ServiceRemoting futásidejű közzéteszi a következő teljesítményszámlálók kapcsolatos szolgáltatáskérelmek feldolgozása.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Szolgáltatás fabric szolgáltatás |# a fennálló kérelmek |A szolgáltatásban feldolgozott kérelmek száma |
| Szolgáltatás fabric szolgáltatás |Kérésenkénti átlagos ezredmásodperc |A szolgáltatás által a kérelem feldolgozásához szükséges idő (ezredmásodpercben) |
| Szolgáltatás fabric szolgáltatás |A kérelem deszerializálásának átlagos ezredmásodpercei |A szolgáltatáshoz érkező szolgáltatáskérési üzenet deszerializálásához szükséges idő (ezredmásodpercben) |
| Szolgáltatás fabric szolgáltatás |A válasz szerializálásának átlagos ezredmásodpercei |A szolgáltatás válaszüzenetének szerializálásához szükséges idő (ezredmásodpercben) a válasz ügyfélnek való küldése előtt |

## <a name="next-steps"></a>További lépések
* [Mintakód](https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0)
* [EventSource-szolgáltatók a PerfView-ban](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
