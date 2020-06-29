---
title: Azure Monitor for VMs engedélyezése a Azure Portal
description: Megtudhatja, hogyan értékelheti ki Azure Monitor for VMs egyetlen Azure-beli virtuális gépen vagy virtuálisgép-méretezési csoporton.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 4cdb9390b3146df74f2cbe8eba7b170a5d11fb2c
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2020
ms.locfileid: "85507058"
---
# <a name="enable-azure-monitor-for-single-vm-or-vmss-in-the-azure-portal"></a>Azure Monitor engedélyezése egyetlen virtuális géphez vagy VMSS a Azure Portal
Ez a cikk azt ismerteti, hogyan engedélyezhető a Azure Monitor for VMs egy virtuális gép vagy virtuálisgép-méretezési csoport számára a Azure Portal használatával. Ez az eljárás a következő módon használható:

- Azure virtuális gép
- Azure virtuálisgép-méretezési csoport
- Azure arc-gép

Mielőtt elkezdené, tekintse át az [előfeltételeket](vminsights-enable-overview.md) , és győződjön meg arról, hogy az előfizetése és erőforrásai megfelelnek a követelményeknek.  

## <a name="enable-azure-monitor-for-vms"></a>Azure Monitor for VMs engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki a **virtuális gépeket**, a **virtuálisgép-méretezési csoportokat**vagy a **gépeket – Azure ív**.

1. Válasszon ki egy erőforrást a listából.

1. A menü **figyelés** területén válassza az **eredmények lehetőséget, majd** **engedélyezze**a beállítást. Az alábbi példa egy Azure-beli virtuális gépet mutat be, de a menü hasonló az Azure VMSS vagy az Azure archoz.

    ![Virtuális gép Azure Monitor for VMsának engedélyezése](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Ha a virtuális gép még nem csatlakozik Log Analytics munkaterülethez, a rendszer kérni fogja, hogy válasszon egyet. Ha korábban még nem [hozott létre munkaterületet](../../azure-monitor/learn/quick-create-workspace.md), akkor kiválaszthatja azt a helyet, ahol a virtuális gép vagy a VMSS telepítve van az előfizetésben. A rendszer létrehozza és konfigurálja ezt a munkaterületet, ha még nem létezik.

2. Az állapotüzenetek a konfiguráció elvégzése után kapják meg az üzeneteket.

    >[!NOTE]
    >Ha a méretezési csoporthoz manuális frissítési modellt használ, frissítse a példányokat a telepítés befejezéséhez. A frissítések elindításához a **példányok** lapon, a **Beállítások** szakaszban olvashat.

    ![Azure Monitor for VMs figyelés telepítésének engedélyezése](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Következő lépések

* A felderített alkalmazások függőségeinek megtekintéséhez lásd: [Azure monitor for VMS Térkép használata](vminsights-maps.md). 
* A szűk keresztmetszetek, a teljes kihasználtság és a virtuális gép teljesítményének meghatározása: az Azure-beli [virtuális gépek teljesítményének megtekintése](vminsights-performance.md).
