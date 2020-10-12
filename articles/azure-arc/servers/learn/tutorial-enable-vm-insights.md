---
title: Oktatóanyag – hibrid gép figyelése Azure Monitor for VMs
description: Megtudhatja, hogyan gyűjthet és elemezheti az adatokat egy hibrid gépről Azure Monitorban.
ms.topic: tutorial
ms.date: 09/23/2020
ms.openlocfilehash: 97ab390570f434295a5aa836ef994640f6dc14f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335415"
---
# <a name="tutorial-monitor-a-hybrid-machine-with-azure-monitor-for-vms"></a>Oktatóanyag: hibrid gép monitorozása Azure Monitor for VMs

A [Azure monitor](../overview.md) adatokat gyűjthet közvetlenül a hibrid virtuális gépekről egy log Analytics-munkaterületre, amely részletes elemzést és korrelációt biztosít. Ez általában a [log Analytics ügynök](../../../azure-monitor/platform/agents-overview.md#log-analytics-agent) telepítését vonja maga után parancsfájl, manuálisan vagy automatizált metódus használatával a konfiguráció-felügyeleti szabványok alapján. Az arc-kompatibilis kiszolgálók a közelmúltban vezették be a Log Analytics és a függőségi ügynök virtuálisgép- [bővítményeinek](../manage-vm-extensions.md) telepítését a Windows és a Linux rendszerhez, amely lehetővé teszi a Azure monitor számára, hogy adatokat gyűjtsön a nem Azure-beli virtuális gépek

Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat és gyűjthet adatokat a Linux vagy Windows rendszerű virtuális gépekről, ha a Azure Monitor for VMs egy egyszerűsített lépések követésével egyszerűsíti a felhasználói élményt, és rövidebb időt vesz igénybe.  

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

* A VM-bővítmény funkció csak a [támogatott régiók](../overview.md#supported-regions)listájában érhető el.

* A [támogatott operációs rendszerekkel](../../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) biztosíthatja, hogy a Azure monitor for VMS által támogatott virtuális gépek operációs rendszerét is támogatja.

* Tekintse át az [log Analytics-ügynök áttekintésében](../../../azure-monitor/platform/log-analytics-agent.md#network-requirements)megadott log Analytics ügynökre vonatkozó tűzfalszabályok ismertetését. A Azure Monitor for VMs Map-függőségi ügynök nem küld magába adatokat, és nem igényel semmilyen módosítást a tűzfalakon vagy a portokon.

## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="enable-azure-monitor-for-vms"></a>Azure Monitor for VMs engedélyezése

1. Indítsa el az Azure arc szolgáltatást a Azure Portal a **minden szolgáltatás**elemre kattintva, majd a gépek keresése és kiválasztása **– Azure arc**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Az ív használatára képes kiszolgálók keresése az összes szolgáltatásban" border="false":::

1. A **Machines – Azure arc** lapon válassza ki a rövid [útmutató cikkben létrehozott](quick-enable-hybrid-vm.md) csatlakoztatott gépet.

1. A **figyelés** szakasz bal oldali paneljén válassza az eredmények elemet **, majd az** **Engedélyezés**lehetőséget.

    :::image type="content" source="./media/tutorial-enable-vm-insights/insights-option.png" alt-text="Az ív használatára képes kiszolgálók keresése az összes szolgáltatásban" border="false":::

1. Az **Azure monitor a** bevezetési bevezetési lapon a rendszer a munkaterület létrehozását kéri. Ebben az oktatóanyagban nem javasoljuk, hogy válasszon ki egy meglévő Log Analytics munkaterületet, ha már rendelkezik ilyennel. Válassza ki az alapértelmezett értéket, amely a regisztrált csatlakoztatott géppel azonos régióban található egyedi névvel rendelkező munkaterület. Ez a munkaterület létrehozva és konfigurálva van.

    :::image type="content" source="./media/tutorial-enable-vm-insights/enable-vm-insights.png" alt-text="Az ív használatára képes kiszolgálók keresése az összes szolgáltatásban" border="false":::

1. Az állapotüzenetek a konfiguráció végrehajtása közben érkeznek. Ez a folyamat néhány percet vesz igénybe, mivel a bővítmények telepítve vannak a csatlakoztatott számítógépen.

    :::image type="content" source="./media/tutorial-enable-vm-insights/onboard-vminsights-vm-portal-status.png" alt-text="Az ív használatára képes kiszolgálók keresése az összes szolgáltatásban" border="false":::

    Ha elkészült, megjelenik egy üzenet arról, hogy a gép sikeresen bekerült, és a rendszer sikeresen telepítette a betekintést.

## <a name="view-data-collected"></a>Összegyűjtött adatok megtekintése

Az üzembe helyezés és a konfiguráció befejezése után válassza az **eredmények** **lehetőséget, majd kattintson a**teljesítmény fülre. A teljesítmény lapon megjelenik a virtuális gép vendég operációs rendszerében gyűjtött teljesítményszámlálók kiválasztott csoportja. Görgessen lefelé a további számlálók megjelenítéséhez, és mozgassa az egeret egy gráfra, hogy megtekintse a Log Analytics virtuálisgép-bővítménynek a gépre való telepítésének idejétől számított átlagos és százalékos értékeit.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-performance-charts.png" alt-text="Az ív használatára képes kiszolgálók keresése az összes szolgáltatásban" border="false":::

Válassza a **Térkép** lehetőséget a Maps szolgáltatás megnyitásához, amely a virtuális gépen futó folyamatokat és azok függőségeit jeleníti meg. Válassza a **Tulajdonságok** lehetőséget a Tulajdonságok ablaktábla megnyitásához, ha még nincs megnyitva.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-map.png" alt-text="Az ív használatára képes kiszolgálók keresése az összes szolgáltatásban" border="false":::

Bontsa ki a virtuális gép folyamatait. Válassza ki az egyik folyamatot a részletek megtekintéséhez és a függőségek kiemeléséhez.

Válassza újra a virtuális gépet, majd válassza a **naplózási események**lehetőséget. A virtuális gép Log Analytics munkaterületén tárolt táblák listája látható. A lista eltérő lehet attól függően, hogy Windows vagy Linux rendszerű virtuális gépet használ-e. Válassza ki az **Event** táblát. Az **Event** tábla tartalmazza a Windows Eseménynapló összes eseményét. Log Analytics megnyílik egy egyszerű lekérdezéssel az összegyűjtött Eseménynapló-bejegyzések lekéréséhez.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Azure Monitorről, tekintse meg a következő cikket:

> [!div class="nextstepaction"]
> [Azure Monitor – áttekintés](../../../azure-monitor/overview.md)