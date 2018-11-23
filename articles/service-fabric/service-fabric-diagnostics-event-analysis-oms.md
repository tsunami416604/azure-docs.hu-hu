---
title: Az Azure Service Fabric Eseményelemzés Log Analytics szolgáltatással |} A Microsoft Docs
description: További információ megjelenítése és elemzése a Log Analytics szolgáltatást a monitorozást és diagnosztikát az Azure Service Fabric-fürtök események.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: ed6a709418871ededc8ddfe06b0eb1ab3e4546e1
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291079"
---
# <a name="event-analysis-and-visualization-with-log-analytics"></a>Esemény elemzése és képi megjelenítés, a Log Analytics használatával
 A log Analytics gyűjti és elemzi az alkalmazások és szolgáltatások a felhőben üzemeltetett származó telemetriai adatok, és segítséget nyújtanak a rendelkezésre állás és teljesítmény maximalizálása elemzésére szolgáló eszközöket biztosít. Ez a cikk ismerteti, hogyan elemezheti és mi történik a fürtön a Log Analytics-lekérdezések futtatása. Az alábbi gyakori kérdések foglalkozik:

* Hogyan háríthatom el a health-események?
* Honnan tudhatom meg, amikor egy csomópont leáll?
* Honnan tudhatom, hogy ha saját alkalmazásszolgáltatások elindítani vagy leállítani?

## <a name="log-analytics-workspace"></a>Log Analytics-munkaterület

>[!NOTE] 
>Diagnosztikai tárfiók alapértelmezés szerint engedélyezve van a fürt létrehozásakor, miközben továbbra is be kell állítania a Log Analytics-munkaterületet a diagnosztikai tárfiók olvasni.

A log Analytics adatokat gyűjt a felügyelt erőforrások, például egy Azure storage-táblába, vagy egy ügynököt, és megőrzi azt egy központi tárházban. Az adatok ezután lehet, elemzés, a riasztás és a Vizualizáció használatos, vagy további exportálását. A log Analytics az eseményeket, teljesítményadatokat vagy bármely más egyéni adatokat támogatja. Tekintse meg [eseményeket a diagnosztikai bővítmény konfigurálásának lépései](service-fabric-diagnostics-event-aggregation-wad.md) és [olvasni a storage-ban az események Log Analytics-munkaterület létrehozásához szükséges lépéseket](service-fabric-diagnostics-oms-setup.md) , hogy az adatok Log Analytics beérkeznek .

Log Analytics által adatok fogadását követően az Azure rendelkezik több *felügyeleti megoldások* , amelyek előre összeállított megoldások vagy figyelheti a bejövő adatokat, testre szabva, hogy számos forgatókönyv operatív irányítópultokat. Ezek közé tartozik egy *Service Fabric-elemzés* megoldás és a egy *tárolók* megoldást, amely két dolgokat azok, diagnosztika és figyelés a Service Fabric-fürtök használatakor. Ez a cikk ismerteti, hogyan használható a Service Fabric-elemzés megoldást, amely a munkaterület jön létre.

## <a name="access-the-service-fabric-analytics-solution"></a>Hozzáférés a Service Fabric-elemzés megoldás

1. Az Azure Portalon nyissa meg az erőforráscsoport, amelyben létrehozta a Service Fabric-elemzés megoldás.

2. Válassza ki az erőforrást **ServiceFabric\<nameOfOMSWorkspace\>**.

2. A `Summary`, csempék formájában grafikon, látni fogja az egyes, a megoldások engedélyezve van, amelyek közül az egyik a Service fabric. Kattintson a **Service Fabric** graph (az alábbi első kép), hogy a Service Fabric-elemzés megoldás (második képen).

    ![A Service Fabric-megoldás](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

    ![A Service Fabric-megoldás](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

A fenti képen a Service Fabric-elemzés megoldás kezdőlapján. Ez az, hogy mi történik, a fürt egy pillanatképnézet. Ha engedélyezte a diagnosztikai fürt létrehozásakor, láthatja az események 

* [A Service Fabric-fürthöz kapcsolódó események](service-fabric-diagnostics-event-generation-operational.md)
* [A Reliable Actors programozási modell események](service-fabric-reliable-actors-diagnostics.md)
* [A Reliable Services programozási modell események](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>A beépített Service Fabric-események, valamint részletesebb rendszeresemények által gyűjtött [a diagnosztikai bővítmény, a konfiguráció frissítésével](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Nézet Service Fabric-események többek között a csomópontokon műveletek

1. A Service Fabric-elemzés oldalon kattintson a diagramon az **Service Fabric-események**.

    ![Service Fabric megoldást műveleti csatorna](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

2. Kattintson a **lista** az események megtekintése a listában. Miután itt látni fogja a rendszer minden olyan események, összegyűjtött alkalmazásproblémát. Referenciaként ezeket az Azure Storage-fiókban a WADServiceFabricSystemEventsTable származnak, és ehhez hasonlóan a reliable services és a következő látja actors-események megfelelő táblák.
    
    ![Lekérdezés műveleti csatorna](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Másik lehetőségként kattintson a Nagyító ikonra a bal oldali és a Kusto-lekérdezési nyelv használatával találja, amit keres. Ha például a fürtben található csomópontok végzett összes művelet megkereséséhez használhatja a következő lekérdezést. Az alábbiakban használt eseményazonosítók találhatók a [műveleti csatorna események referencia](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

A számos további mezők, például a csomópontok (számítógép) a rendszer szolgáltatás (TaskName) lekérdezheti.

### <a name="view-service-fabric-reliable-service-and-actor-events"></a>Nézet Service Fabric Reliable Services és az Aktor esemény

1. A Service Fabric-elemzés oldalon kattintson a diagram a **Reliable Services**.

    ![A Service Fabric megoldást a Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

2. Kattintson a **lista** az események megtekintése a listában. Itt láthatja a reliable services eseményei. A különböző események láthatja, ha a szolgáltatás runasync elkezdődött és befejeződött, amely általában akkor fordul elő a telepítésekkel és frissítésekkel. 

    ![A Reliable Services lekérdezés](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Reliable actors-események hasonló módon is megtekinthetők. A reliable actors részletesebb események beállításához módosítani szeretné a `scheduledTransferKeywordFilter` a Config a diagnosztikai bővítmény (lásd alább). Ezek a következők az értékeket a részletek a [a reliable actors-események referencia](service-fabric-reliable-actors-diagnostics.md#keywords).

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

A Kusto-lekérdezési nyelve, hatékony. Egy másik értékes lekérdezés futtatása, hogy ismerje meg, melyik csomópontokon létrehozó a legtöbb esemény. A lekérdezés az alábbi képernyőképen a Service Fabric műveleti események összesíteni az adott szolgáltatás és a csomópont jeleníti meg.

![Lekérdezés események száma csomópontonként](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>További lépések

* Ahhoz, hogy az infrastruktúra figyelő például teljesítményszámlálók, látogasson el [hozzáadása a Log Analytics-ügynököket](service-fabric-diagnostics-oms-agent.md). Az ügynök összegyűjti a teljesítményszámlálók, és hozzáadja őket a meglévő munkaterülethez.
* A helyszíni fürtök esetén a Log Analytics-átjárót nyújt (http-továbbítás Proxy), amelyek segítségével adatokat küldeni a Log Analytics. Tudjon meg többet arról, hogy a [internetelérés nélküli számítógépek csatlakoztatása a Log Analytics használata a Log Analytics-átjáró](../log-analytics/log-analytics-oms-gateway.md).
* Konfigurálása [automatizált riasztások](../log-analytics/log-analytics-alerts.md) , ezzel elősegítve az észlelési és a diagnosztikát.
* Ismerkedjen meg a Log Analytics részét képező [naplókeresési és lekérdezési](../log-analytics/log-analytics-log-searches.md) funkcióval.
* A Log Analytics és mit kínál részletes áttekintést, olvassa el [Mi az a Log Analytics?](../operations-management-suite/operations-management-suite-overview.md).
