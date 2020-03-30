---
title: Virtuális gépek Azure-figyelőjének engedélyezése az Azure Portalon
description: Megtudhatja, hogyan értékelheti ki az Azure Monitor virtuális gépekegyetlen Azure virtuális gépen vagy egy virtuális gép méretezési csoporton.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 45bc8f16a547d4a95820f9dcd02132844b3be83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480708"
---
# <a name="enable-azure-monitor-for-vms-in-the-azure-portal"></a>Virtuális gépek Azure-figyelőjének engedélyezése az Azure Portalon

Ez a cikk ismerteti, hogyan engedélyezheti az Azure Monitor virtuális gépek kis számú Azure virtuális gépek (Virtuális gépek) az Azure Portalhasználatával. A cél a virtuális gépek figyelése, és a teljesítmény- vagy rendelkezésre állási problémák felderítése. 

Mielőtt elkezdené, tekintse át az [előfeltételeket,](vminsights-enable-overview.md) és győződjön meg arról, hogy az előfizetés és az erőforrások megfelelnek a követelményeknek.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Egyetlen Azure-virtuális gép figyelésének engedélyezése
Az Azure virtuális gép figyelésének engedélyezése:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Válassza a **Virtuális gépek**lehetőséget.

1. Válasszon ki egy virtuális gépet a listából.

1. A virtuális gép lapján a **Figyelés** csoportban válassza az **Insights,** majd **az Enable lehetőséget.**

    ![Virtuális gépek Azure-figyelőjének engedélyezése](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Az **Azure Monitor Insights bevezetési** lapon, ha egy meglévő Log Analytics-munkaterület ugyanabban az előfizetésben, válassza ki a legördülő listában.  

    A lista előre kiválasztja az alapértelmezett munkaterületet és helyet, ahol a virtuális gép telepítve van az előfizetésben. 

    >[!NOTE]
    >Új Log Analytics-munkaterület létrehozásához a virtuális gép figyelési adatainak tárolására, olvassa [el a Log Analytics-munkaterület létrehozása című témakört.](../../azure-monitor/learn/quick-create-workspace.md) A Log Analytics-munkaterületnek a támogatott régiók egyikéhez kell [tartoznia.](vminsights-enable-overview.md#log-analytics)

6. A konfiguráció végrehajtásakor állapotüzeneteket fog kapni.

    ![Az Azure Monitor engedélyezése virtuális gépek számára a központi telepítés feldolgozásának figyelése](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Egyetlen virtuálisgép-méretezési csoport figyelésének engedélyezése

Az Azure virtuálisgép-méretezési csoport figyelésének engedélyezése:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. Válassza a **Virtuálisgép-méretezési csoportok lehetőséget.**

3. A listából válasszon ki egy virtuálisgép-méretezési készletet.

4. A virtuálisgép méretezési csoport ának **figyelője** szakaszában válassza az Insights ( **Betekintés,** majd **az Engedélyezés) lehetőséget.**

5. Az **Insights** lapon, ha egy meglévő Log Analytics-munkaterületet szeretne használni, jelölje ki azt a legördülő listában.

    A lista előre kiválasztja az alapértelmezett munkaterületet és helyet, amelya virtuális gép telepítve van az előfizetésben. 

    ![Virtuális gépek Azure-figyelőjének engedélyezése virtuális gépméret-készlethez](media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Új Log Analytics-munkaterület létrehozásához a figyelési adatok tárolásához a virtuálisgép-méretezési csoportból, [lásd: Log Analytics-munkaterület létrehozása.](../learn/quick-create-workspace.md) A Log Analytics-munkaterületnek a támogatott régiók egyikéhez kell [tartoznia.](vminsights-enable-overview.md#log-analytics)

6. A konfiguráció végrehajtásakor állapotüzeneteket fog kapni.

    >[!NOTE]
    >Ha manuális frissítési modellt használ a méretezési csoporthoz, frissítse a példányokat a telepítés befejezéséhez. A frissítéseket a **Példányok** lap Beállítások szakaszában **indíthatja** el.
    
    ![Az Azure Monitor engedélyezése virtuális gépek számára a központi telepítés feldolgozásának figyelése](media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status.png)

Most, hogy engedélyezte a figyelést a virtuális gép vagy a virtuális gép méretezési készletéhez, a figyelési információk elemzésre érhetők el az Azure Monitor virtuális gépekhez. 

## <a name="next-steps"></a>További lépések

* A felderített alkalmazásfüggőségek megtekintéséhez olvassa el [az Azure Monitor használata virtuális gépek leképezéséhez.](vminsights-maps.md) 
* A szűk keresztmetszetek, az általános kihasználtság és a virtuális gép teljesítményének azonosításáról az [Azure virtuális gép teljesítményének megtekintése](vminsights-performance.md)című témakörben nyújt elő.
