---
title: Tárolók figyelése az Azure Monitor-naplókkal
description: Azure Monitor naplók azure Service Fabric-fürtöken futó tárolók figyeléséhez.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8d4231de13da3f8b2960bd4852136f803a97a546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614434"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Tárolók figyelése az Azure Monitor-naplókkal
 
Ez a cikk ismerteti az Azure Monitor naplók tárolófigyelési megoldás tárolóesemények megtekintéséhez szükséges lépéseket. Ha be szeretné állítani a fürtöt a tárolóesemények gyűjtésére, tekintse meg ezt [a részletes oktatóanyagot.](service-fabric-tutorial-monitoring-wincontainers.md) 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>A tárolófigyelési megoldás beállítása

> [!NOTE]
> Be kell állítania az Azure Monitor-naplókat a fürthöz, valamint telepítenie kell a Log Analytics-ügynököt a csomópontokon. Ha nem, kövesse az Azure Monitor naplók beállítása és [a Log Analytics-ügynök hozzáadása a fürthöz](service-fabric-diagnostics-oms-agent.md) című [lépéseit.](service-fabric-diagnostics-oms-setup.md)

1. Miután a fürt be van állítva az Azure Monitor naplók és a Log Analytics-ügynök, telepítse a tárolók. Várja meg, amíg a tárolók üzembe helyezése, mielőtt a következő lépésre.

2. Az Azure Marketplace-en keresse meg a *Tárolófigyelési megoldást,* és kattintson a **Tárolófigyelési megoldás** erőforrásra, amely megjelenik a Figyelés + Kezelés kategóriában.

    ![Tárolómegoldások hozzáadása](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Hozza létre a megoldást ugyanazon a munkaterületen belül, amely már létrejött a fürthöz. Ez a módosítás automatikusan elindítja az ügynököt, hogy kezdje el a docker-adatok gyűjtését a tárolókon. Körülbelül 15 perc múlva, meg kell jelennie a megoldás világít a bejövő naplók és statisztikák, amint az alábbi képen látható.

    ![Egyszerű logelemzési irányítópult](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Az ügynök lehetővé teszi, hogy több tároló-specifikus naplók, amelyek lekérdezhetők az Azure Monitor naplók, vagy teljesítménymutatók megjelenítésére használható gyűjteménye. A gyűjtött naplótípusok a következők:

* ContainerInventory: információkat jelenít meg a tároló helyéről, nevéről és lemezképeiről
* ContainerImageInventory: az üzembe helyezett lemezképekre vonatkozó információk, beleértve az azonosítókat vagy méreteket
* ContainerLog: konkrét hibanaplók, docker-naplók (stdout, stb.) és egyéb bejegyzések
* ContainerServiceLog: futtatott docker démonparancsok
* Perf: teljesítményszámlálók, beleértve a tároló processzorát, a memóriát, a hálózati forgalmat, a lemez i/o-t és az egyéni metrikákat a gazdagépekről



## <a name="next-steps"></a>További lépések
* További információ az [Azure Monitor naplótárolók megoldásáról.](../azure-monitor/insights/containers.md)
* További információ a tárolóvezénylésről a Service Fabric - [Service Fabric és tárolók](service-fabric-containers-overview.md)
* Ismerkedjen meg az Azure Monitor naplóinak részeként kínált [naplókeresési és lekérdezési](../log-analytics/log-analytics-log-searches.md) funkciókkal
* Az Azure Monitor naplóinak konfigurálása [automatikus riasztási](../log-analytics/log-analytics-alerts.md) szabályok beállításához a felderítés és a diagnosztika elősegítésére