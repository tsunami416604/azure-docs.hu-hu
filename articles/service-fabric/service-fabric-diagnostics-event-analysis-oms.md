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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: 8efbc1d400f1d32e6aee2c1e2d78847bea786940
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
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

1. Nyissa meg az erőforráscsoporthoz, amelyben létrehozta a Service Fabric elemzési megoldások. Válassza ki az erőforrás **ServiceFabric\<nameOfOMSWorkspace\>**  és Ugrás a – áttekintés oldalra.

2. A áttekintése lapon kattintson a hivatkozásra az OMS-portálon végezhető felső részén

    ![Hivatkozás OMS-portálon](media/service-fabric-diagnostics-event-analysis-oms/oms-portal-link.png)

3. Ön most az OMS-portálon, és láthatja a megoldások úgy állította be. A Service Fabric című grafikonon kattintson (az alábbi első kép) a Service Fabric-megoldás beszerzése fordítani (az alábbi második kép)

    ![OMS ú megoldás](media/service-fabric-diagnostics-event-analysis-oms/oms-workspace-all-solutions.png)

    ![OMS ú megoldás](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-new.png)

A fenti kép a Service Fabric elemzési megoldások kezdőlapján. Ez az egy pillanatkép nézet arról, mi történik a fürtön. Ha engedélyezte a diagnosztika a fürt létrehozása után, az események láthatja 

* [Működési csatorna](service-fabric-diagnostics-event-generation-operational.md): magasabb szintű műveletek a Service Fabric-platformról (rendszerszolgáltatások gyűjteménye) hajtja végre.
* [Reliable Actors programozási modell események](service-fabric-reliable-actors-diagnostics.md)
* [Megbízható Services-programozási modell események](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>A műveleti csatornát mellett részletes rendszeresemények által gyűjtött [a diagnosztika bővítmény konfiguráció frissítése](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)

### <a name="view-operational-events-including-actions-on-nodes"></a>Többek között a műveleteket az egyes csomópontok működési eseményeinek megtekintése

1. A Service Fabric Analytics oldalon az OMS-portálon kattintson a grafikonon működési csatorna

    ![Ú megoldás működési csatornát OMS](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-new-operational.png)

2. Kattintson a táblázat egy listán szereplő események megtekintéséhez. Miután itt jelenik meg a rendszer minden olyan események, összegyűjtött alkalmazásproblémát. Referenciaként ezek az Azure Storage-fiókban WADServiceFabricSystemEventsTable, és hasonló módon a reliable services és szereplője események lásd a következő a megfelelő lekérdezésben.
    
    ![Lekérdezés működési csatornát OMS](media/service-fabric-diagnostics-event-analysis-oms/oms-query-operational-channel.png)

Másik lehetőségként kattintson a bal oldali a Nagyító, és találja, amit keres a Kusto lekérdezési nyelv használatával. Például található összes műveleteit a fürt csomópontja, használhatja a következő lekérdezés. A eseményazonosítókat alatt használt találhatók a [működési csatorna események referencia](service-fabric-diagnostics-event-generation-operational.md)

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

A megadott csomópontok (számítógép) a rendszer szolgáltatás (feladatnév) például számos további mezőkre kérdezheti le.

### <a name="view-service-fabric-reliable-service-and-actor-events"></a>Nézet Service Fabric megbízható szolgáltatás és szereplő események

1. A Service Fabric Analytics oldalon az OMS-portálon kattintson a Reliable Services a diagramhoz

    ![OMS ú megoldás megbízható szolgáltatások](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-reliable-services.png)

2. Kattintson a táblázat egy listán szereplő események megtekintéséhez. Itt láthatja a megbízható szolgáltatás eseményeit. Ha a szolgáltatás runasync elkezdődött és befejeződött, amely általában akkor fordul elő a központi telepítések és frissítések különböző eseményeket tekintheti meg. 

    ![OMS megbízható szolgáltatások lekérdezése](media/service-fabric-diagnostics-event-analysis-oms/oms-query-reliable-services.png)

Megbízható szereplő események hasonló módon tekintheti meg. Részletesebb események konfigurálása a megbízható szereplőket, módosítania kell a `scheduledTransferKeywordFilter` a Config a diagnosztikai bővítmény (lásd alább). További tudnivalókat a ezek az értékek a [megbízható szereplője események referencia](service-fabric-reliable-actors-diagnostics.md#keywords)

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

A Kusto lekérdezési nyelv a hatékony. Egy másik értékes lekérdezés futtatása, hogy megtudja, mely csomópontok generál a legtöbb esemény. A lekérdezés az alábbi képernyőképen látható megbízható szolgáltatások esemény összesíteni az adott szolgáltatás és a csomópont jeleníti meg.

![Egy csomópont OMS lekérdezés események](media/service-fabric-diagnostics-event-analysis-oms/oms-query-events-per-node.png)

## <a name="next-steps"></a>További lépések

* Ahhoz, hogy az infrastruktúra-figyelési azaz teljesítményszámlálókat, látogasson el [hozzáadása az OMS-ügynököt](service-fabric-diagnostics-oms-agent.md). Az ügynök teljesítményszámlálók gyűjt, és hozzáadja őket a meglévő munkaterületen.
* A helyi fürthöz OMS szeretnék adatokat küldeni a OMS használható átjáró (http-továbbítás Proxy) kínál. További tájékoztatást talál, amely a [internetkapcsolattal nem rendelkező számítógépek kapcsolódásához az OMS-be az OMS-átjáró](../log-analytics/log-analytics-oms-gateway.md)
* Konfigurálja az OMS beállítása [riasztás automatikus](../log-analytics/log-analytics-alerts.md) észlelési és diagnosztika
* Az beszerzése familiarized a [naplófájl keresési és lekérdezése](../log-analytics/log-analytics-log-searches.md) szolgáltatásai által kínált Naplóelemzési
* A Naplóelemzési, és mi kínál részletesebb áttekintését, olvassa el [Naplóelemzési újdonságai?](../operations-management-suite/operations-management-suite-overview.md)
