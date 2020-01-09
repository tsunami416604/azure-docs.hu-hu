---
title: Azure Monitor for VMs (előzetes verzió) engedélyezése a kiértékeléshez | Microsoft Docs
description: Megtudhatja, hogyan értékelheti ki Azure Monitor for VMs egyetlen Azure-beli virtuális gépen vagy virtuálisgép-méretezési csoporton.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: c77fabf0220ecfcb889ce7cb71fa075fde27226f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75400596"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Azure Monitor for VMs (előzetes verzió) engedélyezése kiértékeléshez

Kiértékelheti Azure Monitor for VMs (előzetes verzió) szolgáltatást kis számú Azure-beli virtuális gépen (VM), vagy egyetlen virtuális gépen vagy virtuálisgép-méretezési csoporton. A figyelés engedélyezésének legegyszerűbb és legközvetlenebb módja a Azure Portal. A cél a virtuális gépek monitorozása és a teljesítmény-vagy rendelkezésre állási problémák felderítése. 

Mielőtt elkezdené, tekintse át az [előfeltételeket](vminsights-enable-overview.md) , és győződjön meg arról, hogy az előfizetése és erőforrásai megfelelnek a követelményeknek.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Egyetlen Azure-beli virtuális gép figyelésének engedélyezése
Az Azure-beli virtuális gép monitorozásának engedélyezése:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

1. Válassza a **Virtual Machines**lehetőséget.

1. Válasszon ki egy virtuális gépet a listából.

1. A virtuális gép lap **figyelés** területén válassza az eredmények **(előzetes verzió)** lehetőséget.

1. Az **áttekintések (előzetes verzió)** lapon válassza a **kipróbálás most**lehetőséget.

    ![Virtuális gép Azure Monitor for VMsának engedélyezése](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Ha egy meglévő Log Analytics munkaterülettel rendelkezik ugyanabban az előfizetésben, akkor a **Azure monitor** megállapítások bevezetése lapon válassza ki azt a legördülő listából.  

    A lista előjelöli az alapértelmezett munkaterületet és helyet, ahol a virtuális gép üzembe lett helyezve az előfizetésben. 

    >[!NOTE]
    >Ha új Log Analytics munkaterületet szeretne létrehozni a figyelési adatok virtuális gépről való tárolásához, tekintse meg a [log Analytics munkaterület létrehozása](../../azure-monitor/learn/quick-create-workspace.md)című témakört. A Log Analytics-munkaterületnek a [támogatott régiók](vminsights-enable-overview.md#log-analytics)egyikéhez kell tartoznia.

A figyelés engedélyezése után előfordulhat, hogy körülbelül 10 percet várnia kell, mielőtt megtekintheti a virtuális gép állapotának mérőszámait.

![Azure Monitor for VMs figyelés telepítésének engedélyezése](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Egyetlen virtuálisgép-méretezési csoport figyelésének engedélyezése

Az Azure virtuálisgép-méretezési csoport figyelésének engedélyezéséhez tegye a következőket:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza a **Virtual Machine Scale sets**lehetőséget.

3. A listából válassza ki a virtuálisgép-méretezési csoport elemet.

4. A virtuálisgép-méretezési csoport lap **figyelés** területén válassza az eredmények **(előzetes verzió)** lehetőséget.

5. Ha meglévő Log Analytics munkaterületet szeretne használni a megállapítások **(előzetes verzió)** lapon, válassza ki azt a legördülő listából.

    A lista előadja az alapértelmezett munkaterületet és helyet, amelyre a virtuális gép telepítve van az előfizetésben. 

    ![Virtuálisgép-méretezési csoport Azure Monitor for VMsának engedélyezése](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Ha új Log Analytics munkaterületet szeretne létrehozni a figyelési adatoknak a virtuálisgép-méretezési csoportból való tárolásához, tekintse meg [a log Analytics munkaterület létrehozása](../learn/quick-create-workspace.md)című témakört. A Log Analytics-munkaterületnek a [támogatott régiók](vminsights-enable-overview.md#log-analytics)egyikéhez kell tartoznia.

A figyelés engedélyezése után előfordulhat, hogy körülbelül 10 percet várnia kell, mielőtt megtekinti a méretezési csoport figyelési adatait.

>[!NOTE]
>Ha a méretezési csoporthoz manuális frissítési modellt használ, frissítse a példányokat a telepítés befejezéséhez. A frissítések elindításához a **példányok** lapon, a **Beállítások** szakaszban olvashat.

![Azure Monitor for VMs figyelés telepítésének engedélyezése](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

Most, hogy engedélyezte a virtuális gép vagy a virtuálisgép-méretezési csoport figyelését, a figyelési információk a Azure Monitor for VMs elemzéséhez érhetők el. 

## <a name="next-steps"></a>Következő lépések

* A felderített alkalmazások függőségeinek megtekintéséhez lásd: [Azure monitor for VMS Térkép használata](vminsights-maps.md). 
* A szűk keresztmetszetek, a teljes kihasználtság és a virtuális gép teljesítményének meghatározása: az Azure-beli [virtuális gépek teljesítményének megtekintése](vminsights-performance.md).
