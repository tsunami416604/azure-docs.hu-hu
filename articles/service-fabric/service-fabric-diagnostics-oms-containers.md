---
title: A Log Analytics az Azure Service Fabric-tárolók monitorozása |} A Microsoft Docs
description: Log Analytics használata az Azure Service Fabric-fürtben futó tárolók.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: aabdae370c28f8fa633372be4505c00c25254408
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49403250"
---
# <a name="monitor-containers-with-log-analytics"></a>Tárolók figyelése a Log Analytics használatával
 
Ez a cikk ismerteti a tároló-események megtekintése az Azure Log Analytics-tároló monitorozására szolgáló megoldás beállításához szükséges lépéseket. A fürt beállításához a tároló eseménygyűjtési, ez látható [részletes oktatóanyag](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

## <a name="set-up-the-container-monitoring-solution"></a>A tároló monitorozására szolgáló megoldás beállítása

> [!NOTE]
> Rendelkezik a Log Analytics a fürt számára, valamint a rendelkezik a Log Analytics-ügynököket a csomópontokon telepített kell. Ha nem, hajtsa végre a [Log Analytics beállítása](service-fabric-diagnostics-oms-setup.md) és [hozzáadása a Log Analytics-ügynököket a fürt](service-fabric-diagnostics-oms-agent.md) első.

1. Ha a Log Analytics és a Log Analytics-ügynököket a fürt be van állítva, a tárolók üzembe helyezése. Várjon, amíg a tárolókat, mielőtt a következő lépésben telepíteni.

2. Az Azure Marketplace-en, keresse meg *Tárolómonitorozási megoldás* , majd kattintson a a **Tárolómonitorozási megoldás** erőforrás, amely a figyelés + felügyelet alatt jelenik meg kategória.

    ![Tárolómegoldások hozzáadása](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. A megoldás, amely a fürt számára már létrehoztak ugyanazon a munkaterületen belül létrehozni. Ez a változás automatikusan elindítja az ügynököt, hogy a tárolók docker adatgyűjtés indítása. Körülbelül 15 perc és így megtekintheti a könnyű fel beérkező naplók és -statisztikák, a megoldás az alábbi képen látható módon.

    ![Alapszintű Log Analytics-irányítópult](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Az ügynök lehetővé teszi, hogy a gyűjtemény több tároló-specifikus naplók kérdezhető le a Log Analyticsben, vagy teljesítménymutatók megjelenítéséhez használja. A gyűjtött log típusok a következők:

* ContainerInventory: a tároló helye, nevét és a képek vonatkozó információkat jeleníti meg.
* ContainerImageInventory: információ a központilag telepített lemezképek, többek között az azonosítók vagy méretek
* ContainerLog: adott hibanaplóit, docker-naplók (stdout, stb.) és egyéb-bejegyzések
* ContainerServiceLog: docker démon parancsok futtatása
* Teljesítmény: teljesítményszámlálók, beleértve a tároló cpu, memória, a hálózati forgalmat, a lemez i/o és a gazdagép gépekről származó egyéni metrikák



## <a name="next-steps"></a>További lépések
* Tudjon meg többet [Log Analytics tárolómegoldások](../log-analytics/log-analytics-containers.md).
* További információ a Service Fabric - tárolóvezénylés [Service Fabric és a tárolók](service-fabric-containers-overview.md)
* Ismerkedjen meg a [naplókeresési és lekérdezési](../log-analytics/log-analytics-log-searches.md) funkciók a Log Analytics részeként érhető el
* Konfigurálja a Log Analytics beállítása [automatizált riasztások](../log-analytics/log-analytics-alerts.md) , ezzel elősegítve az észlelést és a diagnosztikát szabályok