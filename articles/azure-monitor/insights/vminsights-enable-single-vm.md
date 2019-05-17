---
title: Az Azure Monitor-beli virtuális gépek (előzetes verzió) engedélyezése értékelés |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan engedélyezi az Azure Monitor-beli virtuális gépek egyetlen Azure virtuális gép vagy virtuálisgép-méretezési értékelési célokra.
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
ms.openlocfilehash: 673f153b551e4b0c89a564c96d6bd9819ca26f5d
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524085"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Az Azure Monitor engedélyezése a virtuális gépek (előzetes verzió) az értékeléshez

Az Azure Monitor kiértékelendő kis számú Azure-beli virtuális gépek vagy egyetlen virtuális gép vagy virtuálisgép-méretezési csoportot (előzetes verzió) egy virtuális gépnek, engedélyezze a monitorozást a legegyszerűbb és legközvetlenebb módja van az Azure Portalról. Ez a folyamat végén már fog sikeresen elkezdte figyelési őket, és ismerje meg, ha a teljesítménybeli vagy rendelkezésre állási problémákat tapasztal. 

Mielőtt az első lépések, mindenképpen tekintse át a [Előfeltételek](vminsights-enable-overview.md) , és ellenőrizze, hogy az előfizetés és az erőforrások megfelelnek a követelményeknek.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Engedélyezze a monitorozást az egy Azure virtuális Gépen
Ha engedélyezni szeretné az Azure Portalon az Azure Virtuálisgép-monitorozási, tegye a következőket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **virtuális gépek**.

1. Válasszon ki egy virtuális gépet a listából.

1. A virtuális gép lapon található a **figyelés** szakaszban jelölje be **Insights (előzetes verzió)**.

1. Az a **Insights (előzetes verzió)** lapon jelölje be **kipróbálása**.

    ![A virtuális gépek az Azure Monitor engedélyezése a virtuális gép](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. Az a **Azure Monitor Insights felvételi** lapon, ha rendelkezik egy meglévő Log Analytics munkaterület ugyanabban az előfizetésben, válassza a legördülő listában.  

    A lista preselects az alapértelmezett munkaterületre és a helyre, amely a virtuális gép telepítve van az előfizetésben. 

    >[!NOTE]
    >Ha azt szeretné, a virtuális gépről a monitorozási adatok tárolására szolgáló új Log Analytics-munkaterület létrehozásához kövesse a [hozzon létre egy Log Analytics-munkaterület](../../azure-monitor/learn/quick-create-workspace.md) szerepel a támogatott régiók egyikében [Itt](vminsights-enable-overview.md#log-analytics).

Miután engedélyezte a figyelés, előtt megtekintheti a mérőszámok a virtuális gép körülbelül 10 percet vehet igénybe.

![Az Azure Monitor engedélyezése a virtuális gépek figyelése a központi telepítés feldolgozása](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Engedélyezze a monitorozást az egyetlen virtuális gép méretezési

Ahhoz, hogy a Azure-beli virtuálisgép-méretezési csoportot az Azure Portalon figyelését, tegye a következőket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza ki **Virtual Machine Scale Sets**.

3. A listában jelölje ki a virtuálisgép-méretezési csoportot.

4. A virtuális gép méretezési csoport lapján, a a **figyelés** szakaszban jelölje be **Insights (előzetes verzió)**.

5. Az a **Insights (előzetes verzió)** lapon, ha rendelkezik egy meglévő Log Analytics-munkaterület szeretné használni, válassza ki a legördülő listából.

    A lista preselects az alapértelmezett munkaterületre és a helyre, amely a virtuális gép telepítve van az előfizetésben. 

    ![Az Azure Monitor egy virtuálisgép-méretezési csoportot a virtuális gépek előkészítése](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >Ha azt szeretné, a virtuális gépről a monitorozási adatok tárolására szolgáló új Log Analytics-munkaterület létrehozásához kövesse a [hozzon létre egy Log Analytics-munkaterület](../learn/quick-create-workspace.md) szerepel a támogatott régiók egyikében [Itt](vminsights-enable-overview.md#log-analytics).

Miután engedélyezte a figyelés, előtt megtekintheti a méretezési csoport esetében a monitorozási adatok körülbelül 10 percet vehet igénybe.

>[!NOTE]
>Ha a méretezési csoporthoz a manuális frissítési modellt használja, kell frissítenie a példányokat a telepítés befejezéséhez.  Ez alatt a példányok lapon végezhető a **beállítások** szakaszban.

![Az Azure Monitor engedélyezése a virtuális gépek figyelése a központi telepítés feldolgozása](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

## <a name="next-steps"></a>További lépések

Most, hogy a figyelés engedélyezve van a virtuális gép vagy virtuálisgép-méretezési csoportot, akkor ezt az információt és az Azure Monitor-beli virtuális gépek elemzési érhető el. Az állapotfigyelő szolgáltatás használatával kapcsolatban lásd: [a virtuális gépek állapotának megtekintése az Azure Monitor](vminsights-health.md). Felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md). Azonosíthatja a szűk keresztmetszeteket és a virtuális gépek teljesítményét a teljes kihasználtság, lásd: [megtekintése az Azure virtuális gép teljesítménye](vminsights-performance.md), vagy a felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md).