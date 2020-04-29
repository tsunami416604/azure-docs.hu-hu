---
title: Azure Monitor for VMs engedélyezése a Azure Portal
description: Megtudhatja, hogyan értékelheti ki Azure Monitor for VMs egyetlen Azure-beli virtuális gépen vagy virtuálisgép-méretezési csoporton.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 45bc8f16a547d4a95820f9dcd02132844b3be83c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480708"
---
# <a name="enable-azure-monitor-for-vms-in-the-azure-portal"></a>Azure Monitor for VMs engedélyezése a Azure Portal

Ez a cikk azt ismerteti, hogyan engedélyezhető a Azure Monitor for VMs egy kis számú Azure-beli virtuális gépen (VM) a Azure Portal használatával. A cél a virtuális gépek monitorozása és a teljesítmény-vagy rendelkezésre állási problémák felderítése. 

Mielőtt elkezdené, tekintse át az [előfeltételeket](vminsights-enable-overview.md) , és győződjön meg arról, hogy az előfizetése és erőforrásai megfelelnek a követelményeknek.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Egyetlen Azure-beli virtuális gép figyelésének engedélyezése
Az Azure-beli virtuális gép monitorozásának engedélyezése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza a **Virtual Machines**lehetőséget.

1. Válasszon ki egy virtuális gépet a listából.

1. A virtuális gép lap **figyelés** területén válassza az **eredmények elemet, majd az** **Engedélyezés**lehetőséget.

    ![Virtuális gép Azure Monitor for VMsának engedélyezése](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Ha egy meglévő Log Analytics munkaterülettel rendelkezik ugyanabban az előfizetésben, akkor a **Azure monitor** megállapítások bevezetése lapon válassza ki azt a legördülő listából.  

    A lista előjelöli az alapértelmezett munkaterületet és helyet, ahol a virtuális gép üzembe lett helyezve az előfizetésben. 

    >[!NOTE]
    >Ha új Log Analytics munkaterületet szeretne létrehozni a figyelési adatok virtuális gépről való tárolásához, tekintse meg a [log Analytics munkaterület létrehozása](../../azure-monitor/learn/quick-create-workspace.md)című témakört. A Log Analytics-munkaterületnek a [támogatott régiók](vminsights-enable-overview.md#log-analytics)egyikéhez kell tartoznia.

6. Az állapotüzenetek a konfiguráció elvégzése után kapják meg az üzeneteket.

    ![Azure Monitor for VMs figyelés telepítésének engedélyezése](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Egyetlen virtuálisgép-méretezési csoport figyelésének engedélyezése

Az Azure virtuálisgép-méretezési csoport figyelésének engedélyezéséhez tegye a következőket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza a **Virtual Machine Scale sets**lehetőséget.

3. A listából válassza ki a virtuálisgép-méretezési csoport elemet.

4. A virtuálisgép-méretezési csoport lap **figyelés** területén válassza az eredmények elemet, **majd az** **Engedélyezés**lehetőséget.

5. Ha meglévő Log Analytics munkaterületet szeretne használni, a megállapítások **lapon válassza** ki azt a legördülő listából.

    A lista előadja az alapértelmezett munkaterületet és helyet, amelyre a virtuális gép telepítve van az előfizetésben. 

    ![Virtuálisgép-méretezési csoport Azure Monitor for VMsának engedélyezése](media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Ha új Log Analytics munkaterületet szeretne létrehozni a figyelési adatoknak a virtuálisgép-méretezési csoportból való tárolásához, tekintse meg [a log Analytics munkaterület létrehozása](../learn/quick-create-workspace.md)című témakört. A Log Analytics-munkaterületnek a [támogatott régiók](vminsights-enable-overview.md#log-analytics)egyikéhez kell tartoznia.

6. Az állapotüzenetek a konfiguráció elvégzése után kapják meg az üzeneteket.

    >[!NOTE]
    >Ha a méretezési csoporthoz manuális frissítési modellt használ, frissítse a példányokat a telepítés befejezéséhez. A frissítések elindításához a **példányok** lapon, a **Beállítások** szakaszban olvashat.
    
    ![Azure Monitor for VMs figyelés telepítésének engedélyezése](media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status.png)

Most, hogy engedélyezte a virtuális gép vagy a virtuálisgép-méretezési csoport figyelését, a figyelési információk a Azure Monitor for VMs elemzéséhez érhetők el. 

## <a name="next-steps"></a>További lépések

* A felderített alkalmazások függőségeinek megtekintéséhez lásd: [Azure monitor for VMS Térkép használata](vminsights-maps.md). 
* A szűk keresztmetszetek, a teljes kihasználtság és a virtuális gép teljesítményének meghatározása: az Azure-beli [virtuális gépek teljesítményének megtekintése](vminsights-performance.md).
