---
title: Az Azure Service Fabric esemény elemzése a Log Analyticshez |} Microsoft Docs
description: Információ megjelenítése és események Log Analyticshez használják a figyelési és az Azure Service Fabric-fürtök diagnosztika elemzése.
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
ms.date: 05/29/2018
ms.author: srrengar
ms.openlocfilehash: 184faa0f6171ff00ab3c2398f693e9c7ad015d33
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839588"
---
# <a name="event-analysis-and-visualization-with-log-analytics"></a>Esemény elemzése és Naplóelemzési a képi megjelenítés

Naplóelemzési, más néven OMS (Operations Management Suite), a felhőben üzemeltetett szolgáltatások és szolgáltatások figyelése és az alkalmazások diagnosztika segítenek gyűjteménye. Ez a cikk ismerteti, hogyan lekérdezéseket futtathat a Log Analyticshez megismerésében, illetve háríthatóak el, mi történik a fürtön. A következő gyakori kérdések tárgyalja:

* Hogyan hibáinak elhárítása állapotával kapcsolatos események?
* Hogyan állapítható meg, ha egy csomópont leáll?
* Honnan tudhatom, ha az alkalmazás szolgáltatások elindítani vagy leállítani?

## <a name="log-analytics-workspace"></a>Log Analytics-munkaterület

Naplóelemzési kezelt erőforrások, például egy Azure storage tábla vagy az ügynök gyűjti az adatokat, és egy központi tárházban megőrzi azt. Az elemzés, a riasztás és a képi megjelenítés, vagy a használt további exportáló majd lehet az az adatokat. A Naplóelemzési támogatja az eseményeket, teljesítményadatokat vagy egyéb egyéni adatokat. Tekintse meg [lépésekkel állíthatja be az összegyűjtött eseményeket a diagnosztika bővítmény](service-fabric-diagnostics-event-aggregation-wad.md) és [lépések végrehajtásával hozza létre a Naplóelemzési munkaterület olvasni a tárolási események](service-fabric-diagnostics-oms-setup.md) adat áramlik Log Analyticshez való biztosításához .

Naplóelemzési fogadja a adatokat, miután a Azure rendelkezik, több *megoldások* , amelyek a figyelheti a bejövő adatok, több forgatókönyv szerint testre szabott előre csomagolt megoldások. Ezek közé tartozik egy *Service Fabric Analytics* megoldás és a *tárolók* megoldást, amely a két legfontosabb ők diagnosztika és a Service Fabric-fürtök használata esetén figyelését. A cikkből megtudhatja, hogyan használhatja a Service Fabric Analytics megoldást, amely a munkaterület jön létre.

## <a name="access-the-service-fabric-analytics-solution"></a>Hozzáférés a Service Fabric elemzési megoldások

1. Az Azure-portálon lépjen az erőforráscsoporthoz, amelyben létrehozta a Service Fabric elemzési megoldások.

2. Válassza ki az erőforrás **ServiceFabric\<nameOfOMSWorkspace\>**.

2. Összefoglalva az egyes a megoldásairól engedélyezve van, egy a Service Fabric jelenik meg egy grafikonon formájában csempék. Kattintson a **Service Fabric** diagramot (az alábbi első kép) továbbra is a Service Fabric elemzési megoldások (az alábbi második kép).

    ![OMS ú megoldás](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

    ![OMS ú megoldás](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

A fenti kép a Service Fabric elemzési megoldások kezdőlapján. Ez az egy pillanatkép nézet arról, mi történik a fürtön. Ha engedélyezte a diagnosztika a fürt létrehozása után, az események láthatja 

* [Működési csatorna](service-fabric-diagnostics-event-generation-operational.md): magasabb szintű műveletek a Service Fabric-platformról (rendszerszolgáltatások gyűjteménye) hajtja végre.
* [Reliable Actors programozási modell események](service-fabric-reliable-actors-diagnostics.md)
* [Megbízható Services-programozási modell események](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>A műveleti csatornát mellett részletes rendszeresemények által gyűjtött [frissítése a diagnosztika bővítmény config](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Service Fabric események megtekintése többek között a műveleteket az egyes csomópontok

1. A Service Fabric Analytics lapján kattintson a grafikonon **Service Fabric események**.

    ![Ú megoldás működési csatornát OMS](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

2. Kattintson a **lista** megtekintéséhez az események listájában. Miután itt jelenik meg a rendszer minden olyan események, összegyűjtött alkalmazásproblémát. Referenciaként ezek az Azure Storage-fiókban WADServiceFabricSystemEventsTable, és hasonló módon a reliable services és szereplője események lásd a következő a megfelelő lekérdezésben.
    
    ![Lekérdezés működési csatornát OMS](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Másik lehetőségként kattintson a bal oldali a Nagyító, és találja, amit keres a Kusto lekérdezési nyelv használatával. Például található összes műveleteit a fürt csomópontja, használhatja a következő lekérdezés. A eseményazonosítókat alatt használt találhatók a [működési csatorna események hivatkozás](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

A megadott csomópontok (számítógép) a rendszer szolgáltatás (feladatnév) például számos további mezőkre kérdezheti le.

### <a name="view-service-fabric-reliable-service-and-actor-events"></a>Nézet Service Fabric megbízható szolgáltatás és szereplő események

1. A Service Fabric Analytics lapon kattintson a ábrázoló **Reliable Services**.

    ![OMS ú megoldás megbízható szolgáltatások](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

2. Kattintson a **lista** megtekintéséhez az események listájában. Itt láthatja a megbízható szolgáltatás eseményeit. Ha a szolgáltatás runasync elkezdődött és befejeződött, amely általában akkor fordul elő a központi telepítések és frissítések különböző eseményeket tekintheti meg. 

    ![OMS megbízható szolgáltatások lekérdezése](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Megbízható szereplő események hasonló módon tekintheti meg. Részletesebb események konfigurálása a megbízható szereplőket, módosítania kell a `scheduledTransferKeywordFilter` a Config a diagnosztikai bővítmény (lásd alább). További tudnivalókat a ezek az értékek a [megbízható szereplője események hivatkozás](service-fabric-reliable-actors-diagnostics.md#keywords).

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

A Kusto lekérdezési nyelv a hatékony. Egy másik értékes lekérdezés futtatása, hogy megtudja, mely csomópontok generál a legtöbb esemény. A lekérdezés az alábbi képernyőfelvételen a Service Fabric működési eseményeit összesíteni az adott szolgáltatás és a csomópont jeleníti meg.

![Egy csomópont OMS lekérdezés események](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>További lépések

* Ahhoz, hogy az infrastruktúra-figyelési azaz teljesítményszámlálókat, látogasson el [hozzáadása az OMS-ügynököt](service-fabric-diagnostics-oms-agent.md). Az ügynök teljesítményszámlálók gyűjt, és hozzáadja őket a meglévő munkaterületen.
* A helyi fürthöz OMS szeretnék adatokat küldeni a OMS használható átjáró (http-továbbítás Proxy) kínál. További tájékoztatást talál, amely a [internetkapcsolattal nem rendelkező számítógépek kapcsolódásához az OMS-be az OMS-átjáró](../log-analytics/log-analytics-oms-gateway.md)
* Konfigurálja az OMS beállítása [riasztás automatikus](../log-analytics/log-analytics-alerts.md) észlelési és diagnosztika
* Az beszerzése familiarized a [naplófájl keresési és lekérdezése](../log-analytics/log-analytics-log-searches.md) szolgáltatásai által kínált Naplóelemzési
* A Naplóelemzési, és mi kínál részletesebb áttekintését, olvassa el [Naplóelemzési újdonságai?](../operations-management-suite/operations-management-suite-overview.md)
