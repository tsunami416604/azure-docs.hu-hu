---
title: Azure Automation-fiók áthelyezése másik előfizetésre
description: Ez a cikk azt ismerteti, hogyan helyezheti át az Automation-fiókját egy másik előfizetésbe.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bfb2f2d1d0f6a0d11784847344cd3dbcafdb0959
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900997"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Azure Automation-fiók áthelyezése másik előfizetésre

Azure Automation lehetővé teszi egyes erőforrások áthelyezését egy új erőforráscsoporthoz vagy előfizetésbe. Az erőforrásokat a Azure Portal, a PowerShell, az Azure CLI vagy a REST API használatával helyezheti át. További információ a folyamatról: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Az Automation-fiók az egyik áthelyezhető erőforrás. Ebből a cikkből megtudhatja, hogyan helyezheti át az Automation-fiókokat egy másik erőforrásba vagy előfizetésbe. Az Automation-fiók áthelyezésének magas szintű lépései a következők:

1. Távolítsa el a megoldásait.
2. Munkaterület leválasztása.
3. Helyezze át az Automation-fiókot.
4. Törölje, majd hozza létre újra a futtató fiókokat.
5. Engedélyezze újra a megoldásokat.

>[!NOTE]
>Ebben a cikkben a Azure PowerShell az Module paranccsal dolgozhat. Továbbra is használhatja a AzureRM modult. Az az modul és a AzureRM kompatibilitásával kapcsolatos további információkért lásd: [az új Azure PowerShell bemutatása az Module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](../automation-update-azure-modules.md).

## <a name="remove-solutions"></a>Megoldások eltávolítása

A munkaterület Automation-fiókból való leválasztásához el kell távolítania ezeket a megoldásokat a munkaterületről:

- Change Tracking és Inventory
- Frissítéskezelés
- Virtuális gépek indítása és leállítása munkaidőn kívül

1. Keresse meg az erőforráscsoportot az Azure Portalon.
2. Keresse meg az egyes megoldásokat, majd válassza a **Törlés** lehetőséget az **erőforrások törlése** lapon.

    ![Képernyőkép a megoldások törléséről a Azure Portal](../media/move-account/delete-solutions.png)

Ha szeretné, törölheti a megoldásokat a [Remove-AzResource](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0) parancsmag használatával:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>A "virtuális gépek indítása/leállítása a munkaidőn kívül" megoldás riasztási szabályainak eltávolítása

Ehhez a megoldáshoz el kell távolítania a megoldás által létrehozott riasztási szabályokat is.

1. A Azure Portal nyissa meg az erőforráscsoportot, és válassza a **figyelési** > **riasztások** > **kezelése riasztási szabályokat**.

   ![Képernyőkép a riasztások oldaláról, amely a riasztási szabályok kezelése lehetőséget mutatja](../media/move-account/alert-rules.png)

2. A **szabályok** lapon az adott erőforráscsoporthoz konfigurált riasztások listája látható. A megoldás a következő szabályokat hozza létre:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Egyszerre válassza ki a szabályokat, majd a **Törlés** gombra kattintva távolítsa el őket.

    ![Képernyőkép a szabályok lapról, amely a kijelölt szabályok törlésének megerősítését kéri](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Ha nem lát riasztási szabályt a **szabályok** lapon, a letiltott riasztások megjelenítéséhez módosítsa az **állapot** mezőt **Letiltva** értékre. Lehetséges, hogy letiltotta őket.

4. Ha eltávolítja a riasztási szabályokat, el kell távolítania a "virtuális gépek indítása/leállítása a munkaidőn kívül" megoldás értesítéseiben létrehozott műveleti csoportot. A Azure Portal válassza a**riasztások** >  **figyelése** > **műveleti csoportok kezelése**lehetőséget.

5. Válassza a **StartStop_VM_Notification**lehetőséget. 

6. A műveleti csoport lapon válassza a **Törlés**lehetőséget.

    ![A műveleti csoport oldalának képernyőképe](../media/move-account/delete-action-group.png)

Ha szeretné, törölheti a műveleti csoportot a [Remove-AzActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0) parancsmag használatával:

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Munkaterület leválasztása

Most már leválaszthatja a munkaterületet:

1. A Azure Portal válassza az **Automation-fiókhoz** > **kapcsolódó erőforrások** > **csatolt munkaterület**elemet. 

2. Válassza a **munkaterület** megszüntetése lehetőséget a munkaterület Automation-fiókból való leválasztásához.

    ![Képernyőfelvétel a munkaterület Automation-fiókból való leválasztásáról](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Az Automation-fiók áthelyezése

Most már áthelyezheti az Automation-fiókját és a runbookok is. 

1. A Azure Portal tallózással keresse meg az Automation-fiókja erőforrás-csoportját. Válassza az áthelyezés**másik előfizetésre**lehetőséget. **Move** > 

    ![Képernyőkép az erőforráscsoport oldaláról, áthelyezés másik előfizetésre](../media/move-account/move-resources.png)

2. Válassza ki az áthelyezni kívánt erőforráscsoport erőforrásait. Győződjön meg arról, hogy az Automation-fiókja, a runbookok és a Log Analytics munkaterület erőforrásai is megtalálhatók.

## <a name="re-create-run-as-accounts"></a>Futtató fiókok újbóli létrehozása

A [futtató fiókok](../manage-runas-account.md) az Azure-erőforrásokkal való hitelesítéshez Azure Active Directory egyszerű szolgáltatásnevet hoznak létre. Az előfizetések módosításakor az Automation-fiók már nem használja a meglévő futtató fiókot. A futtató fiókok újbóli létrehozása:

1. Nyissa meg az Automation-fiókját az új előfizetésben, és válassza a **fiók beállításai**alatt a **futtató fiókok** elemet. Láthatja, hogy a futtató fiókok már nem teljesek.

    ![Képernyőkép a futtató fiókokról, hiányos megjelenítéssel](../media/move-account/run-as-accounts.png)

2. Törölje a futtató fiókokat egy időben, a **Tulajdonságok** lapon a **Törlés** lehetőség kiválasztásával. 

    > [!NOTE]
    > Ha nem rendelkezik a futtató fiókok létrehozásához vagy megtekintéséhez szükséges engedélyekkel, tekintse meg a következő üzenetet `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` : további információért lásd a [futtató fiókok konfigurálásához szükséges engedélyeket](../manage-runas-account.md#permissions).

3. Miután törölte a futtató fiókokat, válassza a **Létrehozás** az Azure-beli **futtató fiókban**lehetőséget. 

4. Az Azure-beli **futtató fiók hozzáadása** lapon válassza a **Létrehozás** lehetőséget a futtató fiók és az egyszerű szolgáltatásnév létrehozásához. 

5. Ismételje meg a fenti lépéseket a klasszikus Azure-beli futtató fiókkal.

## <a name="enable-solutions"></a>Megoldások engedélyezése

A futtató fiókok újbóli létrehozása után újra engedélyeznie kell azokat a megoldásokat, amelyeket az áthelyezés előtt eltávolítottak: 

1. A "Change Tracking és leltár" megoldás bekapcsolásához válassza a **change Tracking és a leltár** elemet az Automation-fiókban. Válassza ki az áthelyezett Log Analytics munkaterületet, majd válassza az **Engedélyezés**lehetőséget.

2. Ismételje meg az 1. lépést a "Update Management" megoldás esetében.

    ![Képernyőkép az áthelyezett Automation-fiókban lévő megoldások ismételt engedélyezéséről](../media/move-account/reenable-solutions.png)

3. A megoldásokkal bekészített gépek a meglévő Log Analytics munkaterület csatlakoztatásakor láthatók. A "virtuális gépek indítása és leállítása a munkaidőn kívül" megoldás bekapcsolásához újra kell telepítenie a megoldást. A **kapcsolódó erőforrások**területen válassza > a **virtuális gépek indítása/leállítása**további**információk és a megoldás** > **létrehozásának** engedélyezése lehetőséget a telepítés elindításához.

4. A **megoldás hozzáadása** lapon válassza ki a log Analytics-munkaterületet és az Automation-fiókot.

    ![Képernyőfelvétel a megoldás hozzáadása menüről](../media/move-account/add-solution-vm.png)

5. Konfigurálja a megoldást a [virtuális gépek indítása/leállítása a Azure Automation munkaidőn kívüli megoldásban](../automation-solution-vm-management.md)című témakörben leírtak szerint.

## <a name="verify-the-move"></a>Az áthelyezés ellenőrzése

Az áthelyezés befejeződése után ellenőrizze, hogy az alábbi képességek engedélyezve vannak-e. 

|Képesség|Tesztek|Hibaelhárítás|
|---|---|---|
|Runbookok|Egy runbook sikeresen futtatható és kapcsolódhat az Azure-erőforrásokhoz.|[Runbookok hibaelhárítása](../troubleshoot/runbooks.md)
|Verziókövetés|A verziókövetés adattárán manuális szinkronizálást is futtathat.|[Verziókövetés integrálása](../source-control-integration.md)|
|Változáskövetés és leltár|Ellenőrizze, hogy az aktuális leltári adatok megjelennek-e a gépekről.|[Változások követésének hibakeresése](../troubleshoot/change-tracking.md)|
|Frissítéskezelés|Ellenőrizze, hogy látja-e a gépeket, és hogy kifogástalanok-e.</br>Futtasson egy teszt szoftverfrissítés központi telepítését.|[Az Update Management hibáinak megoldása](../troubleshoot/update-management.md)|
|Megosztott erőforrások|Győződjön meg arról, hogy az összes megosztott erőforrás, például a [hitelesítő adatok](../shared-resources/credentials.md) és a [változók](../shared-resources/variables.md)láthatók.|

## <a name="next-steps"></a>További lépések

Az erőforrások Azure-ban való áthelyezésével kapcsolatos további információkért lásd: [erőforrások áthelyezése az Azure-ban](../../azure-resource-manager/management/move-support-resources.md).
