---
title: Log Analytics munkaterület áthelyezése Azure Monitorban | Microsoft Docs
description: Megtudhatja, hogyan helyezheti át Log Analytics munkaterületét egy másik előfizetésbe vagy erőforráscsoporthoz.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: 4baa65ca5dda6b266cd6c739225ebd01d011268c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980089"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Log Analytics munkaterület áthelyezése másik előfizetésre vagy erőforráscsoport-csoportba

Ebből a cikkből megtudhatja, hogyan helyezhet át Log Analytics munkaterületet egy másik erőforráscsoporthoz vagy előfizetésbe ugyanabban a régióban. Az Azure-erőforrások Azure Portal, a PowerShell, az Azure CLI vagy a REST API használatával történő áthelyezéséről bővebben is tájékozódhat. az [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../../azure-resource-manager/management/move-resource-group-and-subscription.md). 

> [!IMPORTANT]
> Munkaterület nem helyezhető át másik régióba.

## <a name="verify-active-directory-tenant"></a>Active Directory bérlő ellenőrzése
A munkaterület forrás-és célhely-előfizetésének ugyanabban a Azure Active Directory bérlőn belül kell lennie. Azure PowerShell használatával ellenőrizze, hogy mindkét előfizetés ugyanazzal a bérlői AZONOSÍTÓval rendelkezik-e.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>A munkaterület áthelyezésével kapcsolatos megfontolások
A munkaterületen telepített felügyelt megoldások a Log Analytics munkaterület áthelyezési művelettel lesznek áthelyezve. A csatlakoztatott ügynökök továbbra is csatlakoztatva maradnak, és az áthelyezés után megőrzik az adatküldést a munkaterületre. Mivel az áthelyezési művelet megköveteli, hogy a munkaterületről semmilyen Automation-fiókra ne legyen hivatkozás, a hivatkozásra támaszkodó megoldásokat el kell távolítani.

Olyan megoldások, amelyeket el kell távolítani az Automation-fiók csatolásának megszüntetése előtt:

- Frissítéskezelés
- Változáskövetés
- Virtuális gépek munkaidőn kívüli elindítása/leállítása


### <a name="delete-in-azure-portal"></a>Törlés az Azure Portalon
A következő eljárással távolíthatja el a megoldásokat a Azure Portal használatával:

1. Nyissa meg annak az erőforráscsoportnak a menüjét, amelyre a megoldások telepítve vannak.
2. Válassza ki az eltávolítandó megoldásokat.
3. Kattintson az **erőforrások törlése** elemre, majd erősítse meg az eltávolítandó erőforrásokat a **delete (Törlés**) gombra kattintva.

![Megoldások törlése](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>Törlés a PowerShell használatával

A megoldások PowerShell használatával történő eltávolításához használja a [Remove-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) parancsmagot az alábbi példában látható módon:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules"></a>Riasztási szabályok eltávolítása
A **virtuális gépek indítása/leállítása** megoldáshoz el kell távolítania a megoldás által létrehozott riasztási szabályokat is. A következő eljárással távolíthatja el ezeket a szabályokat a Azure Portal.

1. Nyissa meg a **figyelés** menüt, majd válassza a **riasztások**lehetőséget.
2. Kattintson a **riasztási szabályok kezelése**lehetőségre.
3. Válassza ki a következő három riasztási szabályt, majd kattintson a **Törlés**gombra.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Szabályok törlése](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Automation-fiók leválasztása
Az Automation-fiók a munkaterületről a Azure Portal használatával való leválasztásához kövesse az alábbi eljárást:

1. Nyissa meg az **Automation-fiókok** menüt, majd válassza ki az eltávolítandó fiókot.
2. A menü **kapcsolódó erőforrások** szakaszában válassza a **csatolt munkaterület**elemet. 
3. Kattintson a **munkaterület leválasztása** elemre a munkaterület Automation-fiókból való leválasztásához.

    ![Munkaterület leválasztása](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Munkaterület áthelyezése

### <a name="azure-portal"></a>Azure portál
A következő eljárás használatával helyezheti át a munkaterületet a Azure Portal használatával:

1. Nyissa meg a **log Analytics munkaterületek** menüt, majd válassza ki a munkaterületet.
2. Az **Áttekintés** lapon kattintson az **erőforráscsoport** vagy **előfizetés**melletti **módosítás** elemre.
3. Megnyílik egy új lap a munkaterülethez kapcsolódó erőforrások listájával. Válassza ki azokat az erőforrásokat, amelyeket ugyanahhoz a cél-előfizetéshez és erőforráscsoporthoz szeretne áthelyezni a munkaterületen. 
4. Válassza ki a cél- **előfizetést** és az **erőforráscsoportot**. Ha a munkaterületet ugyanabba az előfizetésbe helyezi egy másik erőforráscsoporthoz, nem jelenik meg az **előfizetés** lehetőség.
5. A munkaterület és a kiválasztott erőforrások áthelyezéséhez kattintson **az OK** gombra.

    ![Portál](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Ha a munkaterületet a PowerShell használatával szeretné áthelyezni, használja a [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) az alábbi példában látható módon:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> Az áthelyezési művelet után a rendszer újrakonfigurálja a megoldásokat és az Automation-fiók hivatkozását, hogy a munkaterület visszaálljon a korábbi állapotába.


## <a name="next-steps"></a>Következő lépések
- Az áthelyezni kívánt erőforrások listáját itt tekintheti meg: a [műveletek támogatásának áthelyezése az erőforrásokhoz](../../azure-resource-manager/management/move-support-resources.md).
