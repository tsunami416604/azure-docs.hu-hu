---
title: Log Analytics-munkaterület áthelyezése az Azure Monitorban | Microsoft dokumentumok
description: Ismerje meg, hogyan helyezheti át a Log Analytics-munkaterületet egy másik előfizetésbe vagy erőforráscsoportba.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: 9213ddf034e725f6e31c9280d47bd13e4703b3f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659492"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>A Log Analytics-munkaterület áthelyezése másik előfizetésre vagy erőforráscsoportra

Ebben a cikkben megtudhatja, hogy hogyan helyezheti át a Log Analytics-munkaterületet egy másik erőforráscsoportba vagy előfizetésbe ugyanabban a régióban. Az Azure-erőforrások áthelyezéséről az Azure Portalon, a PowerShellen, az Azure CLI-n vagy a REST API-n keresztül. az [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../../azure-resource-manager/management/move-resource-group-and-subscription.md). 

> [!IMPORTANT]
> A munkaterület nem helyezhető át egy másik régióba.

## <a name="verify-active-directory-tenant"></a>Az Active Directory bérlőjének ellenőrzése
A munkaterületi forrás- és cél-előfizetéseknek ugyanabban az Azure Active Directory-bérlőn belül kell létezniük. Az Azure PowerShell segítségével ellenőrizze, hogy mindkét előfizetés azonos bérlői azonosítóval rendelkezik-e.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>Munkaterület áthelyezési szempontjai
A munkaterületre telepített felügyelt megoldások a Log Analytics munkaterület áthelyezési művelettel kerülnek áthelyezésre. A csatlakoztatott ügynökök kapcsolatban maradnak, és az áthelyezés után is megőrzik az adatokat a munkaterületre. Mivel az áthelyezési művelet megköveteli, hogy a munkaterületről ne legyen kapcsolat egyetlen automatizálási fiókhoz sem, a kapcsolaton alapuló megoldásokat el kell távolítani.

Az automatizálási fiók leválasztása előtt el kell távolítani azokat a megoldásokat:

- Frissítéskezelés
- Változások követése
- Virtuális gépek indítása és leállítása munkaidőn kívül


### <a name="delete-in-azure-portal"></a>Törlés az Azure Portalon
Az alábbi eljárással távolíthatja el a megoldásokat az Azure Portalhasználatával:

1. Nyissa meg annak az erőforráscsoportnak a menüjét, amelyben a megoldások telepítve vannak.
2. Válassza ki az eltávolítandó megoldásokat.
3. Kattintson **az Erőforrások törlése gombra,** majd a **Törlés**gombra kattintva erősítse meg az eltávolítandó erőforrásokat.

![Megoldások törlése](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>Törlés a PowerShell használatával

A megoldások PowerShell használatával történő eltávolításához használja az [Remove-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) parancsmalapot az alábbi példában látható módon:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules"></a>Riasztási szabályok eltávolítása
A **virtuális gépek indítása/leállítása** megoldás esetén el kell távolítania a megoldás által létrehozott riasztási szabályokat is. Az alábbi eljárás az Azure Portalon távolítsa el ezeket a szabályokat.

1. Nyissa **meg** a Monitor menüt, és válassza **a Riasztások parancsot.**
2. Kattintson **a Riasztási szabályok kezelése gombra.**
3. Jelölje ki a következő három riasztási szabályt, majd kattintson a **Törlés gombra.**

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Szabályok törlése](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Automatizálási fiók leválasztása
Az alábbi eljárással leválaszthatja az Automation-fiókot a munkaterületről az Azure Portal használatával:

1. Nyissa meg az **Automation-fiókok menüt,** és válassza ki az eltávolítani kívánt fiókot.
2. A menü **Kapcsolódó erőforrások** szakaszában válassza a Csatolt **munkaterület lehetőséget.** 
3. Kattintson **a munkaterület leválasztása** elemre a munkaterület automation-fiókról való leválasztásához.

    ![Munkaterület leválasztása](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Munkaterület áthelyezése

### <a name="azure-portal"></a>Azure portál
Az alábbi eljárással helyezheti át a munkaterületet az Azure Portalon:

1. Nyissa meg a **Log Analytics munkaterületeket menüt,** és válassza ki a munkaterületet.
2. Az **Áttekintés** lapon kattintson a **Módosítás** gombra az **Erőforrás csoport** vagy az **Előfizetés csoport**mellett.
3. Megnyílik egy új lap a munkaterülethez kapcsolódó erőforrások listájával. Válassza ki azokat az erőforrásokat, amelyeket a munkaterülettel azonos cél-előfizetésre és erőforráscsoportra szeretne áthelyezni. 
4. Válasszon egy cél **előfizetési** és **erőforráscsoportot**. Ha a munkaterületet ugyanabban az előfizetésben egy másik erőforráscsoportba helyezi át, nem fogja látni az **Előfizetés** lehetőséget.
5. A munkaterület és a kijelölt erőforrások áthelyezéséhez kattintson az **OK** gombra.

    ![Portál](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
A munkaterület PowerShell használatával történő áthelyezéséhez használja az [Move-AzResource programot](/powershell/module/AzureRM.Resources/Move-AzureRmResource) az alábbi példában:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> Az áthelyezési művelet után az eltávolított megoldásokat és az Automation-fiók kapcsolatot újra kell konfigurálni, hogy a munkaterület visszaálljon a korábbi állapotába.


## <a name="next-steps"></a>További lépések
- Az erőforrások at, amelyek támogatják az áthelyezést, olvassa el [az Erőforrások művelettámogatásának áthelyezése (Áthelyezése) témakört.](../../azure-resource-manager/management/move-support-resources.md)
