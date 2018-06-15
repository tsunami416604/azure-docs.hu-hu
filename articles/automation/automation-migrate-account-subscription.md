---
title: Automation-fiók és erőforrások áttelepítése
description: Ez a cikk ismerteti az Azure Automation és a kapcsolódó erőforrások Automation-fiók áthelyezése egy előfizetés másik.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 75d973388bb8a37cc0b796b1621f847b047d70f9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194110"
---
# <a name="migrate-automation-account-and-resources"></a>Automation-fiók és erőforrások áttelepítése
Az Automation-fiókok és a kapcsolódó erőforrások (Ez azt jelenti, eszközök, a runbookok, modulok, stb.), amely az Azure-portálon létrehozott, és szeretne áttérni az egyik erőforráscsoportból egy másikra, vagy egy előfizetésből az egy másik Ez a könnyen elvégezhető a [erőforrások áthelyezése](../azure-resource-manager/resource-group-move-resources.md) funkció érhető el az Azure portálon. Azonban ez a művelet folytatása előtt először tekintse át a következő [erőforrások áthelyezése előtt ellenőrzőlista](../azure-resource-manager/resource-group-move-resources.md#checklist-before-moving-resources) és emellett a következő lista az Automation adott.  

1. A célként megadott előfizetés-erőforráscsoport csoport forrásaként ugyanabban a régióban kell lennie. Tehát, Automation-fiók nem lehet áthelyezni régiók között.
2. Amikor helyezi át az erőforrásokat (például a runbookok, feladatok, stb.), mind a forrás-csoport és a célcsoport zárolva van a művelet időtartama. Írási és törlési műveletek blokkolják a csoportok csak az áthelyezés befejeződése után. 
3. Vannak forgatókönyvek vagy változók, amely hivatkozik egy erőforrás vagy előfizetési Azonosítóját a meglévő előfizetésből, áttelepítés befejezése után kell frissíteni.  

> [!NOTE]
> Ez a funkció nem támogatja a mozgóátlag klasszikus automation erőforrásokat.
>
>

## <a name="to-move-the-automation-account-using-the-portal"></a>A portál használatával Automation-fiók áthelyezése
1. Az Automation-fiók kattintson **áthelyezése** az oldal tetején.<br> ![Elem áthelyezése](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. Válassza ki az automation-fiók áthelyezése egy másik erőforráscsoportban vagy egy másik előfizetést.
3. Az a **erőforrások áthelyezése** ablaktáblán, akkor azt mutatja be mind az Automation-fiók, és az erőforrás (ok) ból erőforrásaihoz. Válassza ki a **előfizetés** és **erőforráscsoport** a legördülő listákból, vagy a lehetőséget választja **hozzon létre egy új erőforráscsoportot** , és írja be egy új erőforráscsoport neve mezőbe. 
4. Tekintse át, és a jelölőnégyzet bejelölésével igazolja, hogy *megérteni az eszközök és parancsfájlok kell frissíteni, hogy új erőforrás-azonosítók használata után erőforrások* , majd **OK**.<br> ![Erőforrások ablaktáblájának áthelyezése](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>   

Ez a művelet befejezéséhez több percig is eltarthat. A **értesítések**, lehetősége lesz, hogy megtörténik - érvényesítési, az áttelepítés állapotot, majd végül amikor elkészült.    

## <a name="to-move-the-automation-account-using-powershell"></a>A PowerShell használatával Automation-fiók áthelyezése
Használjon meglévő Automation-erőforrások áthelyezése egy másik erőforráscsoportba vagy előfizetésbe, a **Get-AzureRmResource** parancsmagot, hogy az adott Automation-fiókot, majd **Move-AzureRmResource** parancsmag az áthelyezés végrehajtásához.

Az első példában Automation-fiók áthelyezése egy új erőforráscsoportot.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ```

A fenti példa végrehajtása után a rendszer kéri a ellenőrizze akarja-e az adott művelet végrehajtására. Miután rákattintott **Igen** és a parancsfájl folytatja, amíg az áttelepítést hajt végre, nem kap belőle értesítéseket. 

Helyezze át az új előfizetés, tartalmazza a értéket a *DestinationSubscriptionId* paraméter.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ```

Csakúgy, mint az előző példában kéri az áthelyezés megerősítéséhez. 

## <a name="next-steps"></a>További lépések
* További információ az erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md)
* További információ a szerepköralapú hozzáférés-vezérlés az Azure Automationben: [szerepköralapú hozzáférés-vezérlés az Azure Automationben](automation-role-based-access-control.md).
* Az előfizetés kezelésére szolgáló PowerShell-parancsmagokkal kapcsolatban lásd: [Azure PowerShell használata a Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* Az előfizetés kezelésének portál funkciókkal kapcsolatos további tudnivalókért lásd: [-erőforrások kezeléséhez Azure portál használatával](../azure-resource-manager/resource-group-portal.md).
