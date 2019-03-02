---
title: Az Azure Monitor naplóira Azure Service fabric-tárolók monitorozása |} A Microsoft Docs
description: Az Azure Monitor naplóira használja az Azure Service Fabric-fürtben futó tárolók.
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
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: d5840db718191c9b67a8b28a2efccd55146ae510
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246933"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Az Azure Monitor naplóira-tárolók monitorozása
 
Ez a cikk ismerteti a tároló-események megtekintése az Azure Monitor naplókat tároló monitorozására szolgáló megoldás beállításához szükséges lépéseket. A fürt beállításához a tároló eseménygyűjtési, ez látható [részletes oktatóanyag](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>A tároló monitorozására szolgáló megoldás beállítása

> [!NOTE]
> Szüksége lesz az Azure Monitor naplók állítsa be a fürt számára, valamint a Log Analytics-ügynököket a csomópontok telepítve van. Ha nem, hajtsa végre a [beállítása az Azure Monitor naplóira](service-fabric-diagnostics-oms-setup.md) és [hozzáadása a Log Analytics-ügynököket a fürt](service-fabric-diagnostics-oms-agent.md) első.

1. Miután az Azure Monitor-naplók és a Log Analytics-ügynököket a fürt be van állítva, a tárolók üzembe helyezése. Várjon, amíg a tárolókat, mielőtt a következő lépésben telepíteni.

2. Az Azure Marketplace-en, keresse meg *Tárolómonitorozási megoldás* , majd kattintson a a **Tárolómonitorozási megoldás** erőforrás, amely a figyelés + felügyelet alatt jelenik meg kategória.

    ![Tárolómegoldások hozzáadása](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. A megoldás, amely a fürt számára már létrehoztak ugyanazon a munkaterületen belül létrehozni. Ez a változás automatikusan elindítja az ügynököt, hogy a tárolók docker adatgyűjtés indítása. Körülbelül 15 perc és így megtekintheti a könnyű fel beérkező naplók és -statisztikák, a megoldás az alábbi képen látható módon.

    ![Alapszintű Log Analytics-irányítópult](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Az ügynök lehetővé teszi, hogy a gyűjtemény több tároló-specifikus naplók az Azure Monitor naplóira kérdezhető le, vagy teljesítménymutatók megjelenítéséhez használja. A gyűjtött log típusok a következők:

* ContainerInventory: a tároló helye, nevét és a képek vonatkozó információkat jeleníti meg.
* ContainerImageInventory: információ a központilag telepített lemezképek, többek között az azonosítók vagy méretek
* ContainerLog: adott hibanaplóit, docker-naplók (stdout, stb.) és egyéb-bejegyzések
* ContainerServiceLog: docker démon parancsok futtatása
* Teljesítmény: teljesítményszámlálók, beleértve a tároló cpu, memória, a hálózati forgalmat, a lemez i/o és a gazdagép gépekről származó egyéni metrikák



## <a name="next-steps"></a>További lépések
* Tudjon meg többet [Azure Monitor-naplók tárolási megoldását](../azure-monitor/insights/containers.md).
* További információ a Service Fabric - tárolóvezénylés [Service Fabric és a tárolók](service-fabric-containers-overview.md)
* Ismerkedjen meg a [naplókeresési és lekérdezési](../log-analytics/log-analytics-log-searches.md) szolgáltatásai által kínált Azure Monitor naplóira
* Az Azure Monitor naplók, állítsa be a [automatizált riasztások](../log-analytics/log-analytics-alerts.md) , ezzel elősegítve az észlelést és a diagnosztikát szabályok