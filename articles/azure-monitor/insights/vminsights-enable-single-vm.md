---
title: Az Azure Monitor-beli virtuális gépek (előzetes verzió) engedélyezése értékelés |} A Microsoft Docs
description: Megtudhatja, hogyan értékelheti ki az Azure Monitor-beli virtuális gépek, egy Azure virtuális gépen vagy egy virtuálisgép-méretezési csoportot.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: ec909bcd16f923bbd7036f6a69df2bbb07e561b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122532"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Az Azure Monitor engedélyezése a virtuális gépek (előzetes verzió) az értékeléshez

Értékelődik ki az Azure Monitor-beli virtuális gépek (előzetes verzió) az Azure-beli virtuális gépek (VM) kis számú, vagy egy egyetlen virtuális gép vagy virtuálisgép-méretezési állítsa be. A legegyszerűbb és leggyakrabban közvetlen engedélyezze a monitorozást módja az Azure Portalról. A cél, hogy a virtuális gépek figyelése és a teljesítmény vagy a rendelkezésre állási problémák felderítése. 

Mielőtt elkezdené, tekintse át a [Előfeltételek](vminsights-enable-overview.md) és győződjön meg arról, hogy az előfizetés és az erőforrások megfelelnek a követelményeknek.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Engedélyezze a monitorozást az egy Azure virtuális Gépen
Az Azure virtuálisgép-figyelés engedélyezése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **virtuális gépek**.

1. Válasszon ki egy virtuális gépet a listából.

1. A virtuális gép lapon található a **figyelés** szakaszban jelölje be **Insights (előzetes verzió)** .

1. Az a **Insights (előzetes verzió)** lapon jelölje be **kipróbálása**.

    ![A virtuális gépek az Azure Monitor engedélyezése a virtuális gép](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. Az a **Azure Monitor Insights felvételi** lapon, ha rendelkezik egy meglévő Log Analytics munkaterület ugyanabban az előfizetésben, válassza a legördülő listában.  

    A listában az alapértelmezett munkaterületre és a hely, ahol a virtuális gép az előfizetésben üzembe helyezett preselects. 

    >[!NOTE]
    >A virtuális gépről a monitorozási adatok tárolásához egy új Log Analytics-munkaterület létrehozásához lásd: [hozzon létre egy Log Analytics-munkaterület](../../azure-monitor/learn/quick-create-workspace.md). A Log Analytics-munkaterületre kell tartoznia az egyik a [támogatott régiók](vminsights-enable-overview.md#log-analytics).

Miután engedélyezte a figyelés, szüksége lehet várjon körülbelül 10 percet a virtuális gép a mérőszámok megtekintéséhez.

![Az Azure Monitor engedélyezése a virtuális gépek figyelése a központi telepítés feldolgozása](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Engedélyezze a monitorozást az egyetlen virtuális gép méretezési

Ahhoz, hogy az Azure-beli virtuálisgép-méretezési készlet figyelése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza ki **Virtual Machine Scale Sets**.

3. A listában jelölje ki a virtuálisgép-méretezési csoportot.

4. A virtuális gép méretezési csoport lapján, a a **figyelés** szakaszban jelölje be **Insights (előzetes verzió)** .

5. Az a **Insights (előzetes verzió)** lapon, ha szeretné használni egy meglévő Log Analytics-munkaterületet, válassza ki a legördülő listából.

    A lista preselects az alapértelmezett munkaterületre és a helyre, amely a virtuális gép telepítve van az előfizetésben. 

    ![Az Azure Monitor egy virtuálisgép-méretezési csoportot a virtuális gépek előkészítése](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >A virtuálisgép-méretezési csoportban a monitorozási adatok tárolásához egy új Log Analytics-munkaterület létrehozásához lásd: [hozzon létre egy Log Analytics-munkaterület](../learn/quick-create-workspace.md). A Log Analytics-munkaterületre kell tartoznia az egyik a [támogatott régiók](vminsights-enable-overview.md#log-analytics).

Miután engedélyezte a figyelés, szüksége lehet várjon körülbelül 10 percet a méretezési csoport esetében a figyelési adatok megtekintéséhez.

>[!NOTE]
>Ha a méretezési csoporthoz a manuális frissítési modellt használ, frissítse a példányok a telepítés befejezéséhez. Történő frissítés megkezdése a **példányok** lap a **beállítások** szakaszban.

![Az Azure Monitor engedélyezése a virtuális gépek figyelése a központi telepítés feldolgozása](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

Most, hogy engedélyezte, hogy a virtuális gép vagy virtuálisgép-méretezési csoporthoz figyelése, elemezhetők az Azure Monitor-beli virtuális gépek a monitorozási információkhoz érhető el. 

## <a name="next-steps"></a>További lépések

* Az állapotfigyelő szolgáltatás használatával kapcsolatban lásd: [megismerheti az Azure virtuális gépek figyelése állapotát](vminsights-health.md). 
* Felderített alkalmazások függőségeinek megtekintése: [használata az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md). 
* Azonosíthatja a szűk keresztmetszeteket, teljes kihasználtság és a virtuális gép teljesítményét, lásd: [megtekintése az Azure virtuális gép teljesítménye](vminsights-performance.md).