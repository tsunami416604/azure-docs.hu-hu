---
title: Azure Service Fabric eseményelemzés az Azure Monitor-naplókkal
description: Ismerje meg az események vizualizálását és elemzését az Azure Service Fabric-fürtök figyeléséhez és diagnosztikájához használt Azure Monitor naplók használatával.
author: srrengar
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: 40dd930aa21e3056d5ecc908359215d6874ed8ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464739"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Eseményelemzés és vizualizáció az Azure Monitor-naplókkal
 Az Azure Monitor naplói összegyűjtik és elemzik a felhőben üzemeltetett alkalmazások és szolgáltatások telemetriai adatait, és elemzési eszközöket biztosít a rendelkezésre állásuk és teljesítményük maximalizálásához. Ez a cikk ismerteti, hogyan futtathatja a lekérdezéseket az Azure Monitor naplóiban, hogy betekintést nyerjen, és elhárítsa a fürtben zajló eseményeket. A következő gyakori kérdésekkel foglalkozunk:

* Hogyan háríthatók el az egészségügyi események?
* Honnan tudhatom, ha egy csomópont leáll?
* Honnan tudhatom, hogy az alkalmazásom szolgáltatásai elindultak vagy leálltak?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>A Log Analytics munkaterület áttekintése

>[!NOTE] 
>Bár a diagnosztikai tároló alapértelmezés szerint engedélyezve van a fürt létrehozása kor, továbbra is be kell állítania a Log Analytics munkaterületet a diagnosztikai tárolóból történő olvasáshoz.

Az Azure Monitor naplók adatokat gyűjt a felügyelt erőforrások, beleértve az Azure storage-tábla vagy ügynök, és egy központi tárházban tartja fenn. Az adatok ezután elemzésre, riasztásra és vizualizációra, illetve további exportálásra használhatók. Az Azure Monitor naplói támogatja az eseményeket, a teljesítményadatokat vagy bármely más egyéni adatot. Tekintse meg [a lépéseket, hogy konfigurálja a diagnosztikai bővítmény összesített események](service-fabric-diagnostics-event-aggregation-wad.md) és lépések létrehozása a Log [Analytics-munkaterületet olvasni a tárolási események,](service-fabric-diagnostics-oms-setup.md) győződjön meg arról, hogy az adatok áramlik az Azure Monitor naplók.

Miután az Azure Monitor naplói adatokat kapnak, az Azure számos *figyelési megoldással* rendelkezik, amelyek előre csomagolt megoldások vagy működési irányítópultok a bejövő adatok figyelésére, több forgatókönyvre szabottan. Ezek közé tartozik a *Service Fabric Analytics-megoldás* és a *Tárolók* megoldás, amelyek a két legfontosabb a diagnosztika és a figyelés a Service Fabric-fürtök használatakor. Ez a cikk ismerteti, hogyan használhatja a Service Fabric Analytics-megoldás, amely a munkaterülettel létrehozott.

## <a name="access-the-service-fabric-analytics-solution"></a>A Service Fabric Analytics-megoldás elérése

Az [Azure Portalon](https://portal.azure.com)nyissa meg azt az erőforráscsoportot, amelyben létrehozta a Service Fabric Analytics-megoldást.

Válassza ki a **\<ServiceFabric\>nameOfOMSWorkspace erőforrást.**

A `Summary`ban, látni fogja csempe formájában egy grafikon minden a megoldások engedélyezve vannak, beleértve a Service Fabric. Kattintson a **Service Fabric** graph a Service Fabric Analytics-megoldás folytatásához.

![Service Fabric-megoldás](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

Az alábbi képen a Service Fabric Analytics-megoldás kezdőlapja látható. Ez a kezdőlap pillanatkép-nézetet nyújt afürtben zajló eseményekről.

![Service Fabric-megoldás](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Ha engedélyezte a diagnosztikát a fürt létrehozásakor, láthatja a 

* [Service Fabric-fürtesemények](service-fabric-diagnostics-event-generation-operational.md)
* [Megbízható szereplők programozási modell események](service-fabric-reliable-actors-diagnostics.md)
* [Megbízható szolgáltatások programozási modell eseményei](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>A Service Fabric-események mellett részletesebb rendszeresemények is gyűjthetők [a diagnosztikai bővítmény konfigurációjának frissítésével.](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>A Szolgáltatásháló-események megtekintése, beleértve a csomópontokon végrehajtott műveleteket is

A Service Fabric Analytics lapon kattintson a **Service Fabric-események grafikonjára.**

![Service Fabric-megoldás működési csatornája](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Kattintson a **Lista** gombra a listában szereplő események megtekintéséhez. Miután itt látni fogja az összes rendszer eseményeket, amelyeket összegyűjtött. Referenciaként ezek a **WADServiceFabricSystemEventsTable** az Azure Storage-fiókban, és hasonlóképpen a megbízható szolgáltatások és szereplők események lásd a következő a megfelelő táblákból.
    
![Lekérdezés operatív csatornája](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Másik lehetőségként kattintson a bal oldali nagyítóra, és a Kusto lekérdezési nyelv segítségével keresse meg, amit keres. Például a fürt csomópontjain végrehajtott összes művelet megkereséséhez használhatja a következő lekérdezést. Az alább használt eseményazonosítók az [operatív csatorna eseményeinek hivatkozásában](service-fabric-diagnostics-event-generation-operational.md)találhatók.

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Sokkal több mezőt is lekérdezhet, például a rendszerszolgáltatás adott csomópontjait (Számítógép) (TaskName).

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>A Szolgáltatásháló megbízható szolgáltatás- és aktoreseményeinek megtekintése

A Service Fabric Analytics lapon kattintson a **megbízható szolgáltatások grafikonjára.**

![Service Fabric megoldás megbízható szolgáltatások](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Kattintson a **Lista** gombra a listában szereplő események megtekintéséhez. Itt láthatja az eseményeket a megbízható szolgáltatásokból. Megtekintheti a különböző eseményeket, amikor a szolgáltatás runasync elindul, és befejeződött, amely általában történik a központi telepítések és frissítések. 

![Megbízható szolgáltatások lekérdezése](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Megbízható szereplő események is megtekinthetők hasonló módon. A megbízható szereplők részletesebb eseményeinek konfigurálásához `scheduledTransferKeywordFilter` módosítania kell a diagnosztikai bővítmény konfigurációját (lásd alább). Ezek relevanciaértékeia [megbízható szereplőkre vonatkozó referenciában található](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

A Kusto lekérdezési nyelv erőllág. Egy másik értékes lekérdezés futtatható, hogy megtudja, mely csomópontok generálják a legtöbb eseményt. Az alábbi képernyőképen látható lekérdezés a Service Fabric működési eseményeit mutatja az adott szolgáltatással és csomóval összesítve.

![Események lekérdezése csomópontonként](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>További lépések

* Az infrastruktúra figyelésének, azaz a teljesítményszámlálóknak a engedélyezéséhez menjen [a Log Analytics-ügynök hozzáadásához.](service-fabric-diagnostics-oms-agent.md) Az ügynök összegyűjti a teljesítményszámlálókat, és hozzáadja őket a meglévő munkaterülethez.
* A helyszíni fürtök, az Azure Monitor naplók egy átjáró (HTTP továbbító proxy) kínál, amely adatok küldésére használható az Azure Monitor naplók. Erről a [Log Analytics-átjáró használatával internet-hozzáféréssel nem rendelkező számítógépek csatlakoztatása az Azure Monitor-naplókhoz című](../azure-monitor/platform/gateway.md)dokumentumban olvashat bővebben.
* Állítsa be [az automatikus riasztást](../log-analytics/log-analytics-alerts.md) az észlelés és a diagnosztika elősegítésére.
* Ismerkedjen meg az Azure Monitor-naplók részeként kínált [naplókeresési és lekérdezési](../log-analytics/log-analytics-log-searches.md) funkciókkal.
* Az Azure Monitor-naplók és az általa ajánlott ajánlatok részletesebb áttekintését olvassa el [a Mi az Azure Monitor-naplók című témakörben.](../operations-management-suite/operations-management-suite-overview.md)
