---
title: Az Azure Automation-fiók áthelyezése egy másik előfizetésbe
description: Ez a cikk bemutatja, hogyan helyezheti át az Automation-fiókot egy másik előfizetésbe.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2dbe7dc171b6e0ec81c99a460a4f997eeb9e27a5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681894"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Az Azure Automation-fiók áthelyezése egy másik előfizetésbe

Az Azure Automation lehetővé teszi, hogy bizonyos erőforrásokat áthelyezzen egy új erőforráscsoportba vagy előfizetésbe. Erőforrások áthelyezése az Azure Portalon, a PowerShell, az Azure CLI vagy a REST API-n keresztül. Ha többet szeretne megtudni a folyamatról, olvassa el az [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe című témakört.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

Az Azure Automation-fiók az egyik olyan erőforrás, amely áthelyezhető. Ebben a cikkben megtudhatja, hogyan helyezheti át az Automation-fiókokat egy másik erőforrásba vagy előfizetésbe. Az Automation-fiók áthelyezésének magas szintű lépései a következők:

1. Távolítsa el a megoldásokat.
2. A munkaterület leválasztása.
3. Az Automation-fiók áthelyezése.
4. Törölje, majd hozza létre újra a Futtatás másként fiókokat.
5. Engedélyezze újra a megoldásokat.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](../automation-update-azure-modules.md)

## <a name="remove-solutions"></a>Megoldások eltávolítása

Ha le szeretné választani a munkaterületet az Automation-fiókról, el kell távolítania ezeket a megoldásokat a munkaterületről:

- Change Tracking és Inventory
- Frissítéskezelés
- Virtuális gépek indítása és leállítása munkaidőn kívül

1. Keresse meg az erőforráscsoportot az Azure Portalon.
2. Keresse meg az egyes **megoldásokat,** és kattintson az Erőforrások törlése lapon a Törlés gombra.

    ![Megoldások törlése az Azure Portalról](../media/move-account/delete-solutions.png)

    Ha szeretné, törölheti a megoldásokat az [Remove-AzResource](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0) parancsmag használatával:

    ```azurepowershell-interactive
    $workspaceName = <myWorkspaceName>
    $resourceGroupName = <myResourceGroup>
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
    ```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>A virtuális gépek indítási/leállítási szabályainak eltávolítása munkaidőn kívüli megoldás esetén

A virtuális gépek indítása/leállítása munkaidőn kívüli megoldás esetén is el kell távolítania a megoldás által létrehozott riasztási szabályokat.

1. Az Azure Portalon nyissa meg az erőforráscsoportot, és válassza a > **figyelési riasztások riasztási** > **szabályok kezelése**lehetőséget. **Monitoring**

![A Riasztáskezelése szabályok at megjelenítő riasztások lap](../media/move-account/alert-rules.png)

2. A Szabályok lapon az adott erőforráscsoportban konfigurált riasztások listáját kell látnia. A megoldás a következő szabályokat hozza létre:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Egyenként jelölje ki a szabályokat, és a **Törlés gombra** kattintva távolítsa el őket.

    ![A kijelölt szabályok törlésének megerősítését kérő szabályok oldal](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Ha a Szabályok lapon nem jelennek meg riasztási szabályok, módosítsa az **Állapot** mezőt Letiltva értékre a letiltott riasztások megjelenítéséhez, mert lehet, hogy letiltotta őket.

4. A riasztási szabályok eltávolításakor el kell távolítania a virtuális gépek indítási/leállítási műveletcsoportját a megoldásértesítései során. Az Azure Portalon válassza a Riasztások > **figyelése műveletcsoportok kezelése** **lehetőséget.** > **Alerts**

5. Válassza a **StartStop_VM_Notification**lehetőséget. 

6. A műveletcsoport lapon válassza a **Törlés**lehetőséget.

    ![Műveletcsoport lapja](../media/move-account/delete-action-group.png)

    Ha szeretné, törölheti a műveletcsoportot az [Remove-AzActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0) parancsmag használatával:

    ```azurepowershell-interactive
    Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
    ```

## <a name="unlink-your-workspace"></a>Munkaterület leválasztása

Most már leválaszthatja a munkaterületet:

1. Az Azure Portalon válassza az **Automation-fiókkal** > **összekapcsolt erőforrásokkal** > **kapcsolatos munkaterületet.** 

2. Válassza **a munkaterület leválasztása** lehetőséget a munkaterület automation-fiókról való leválasztásához.

    ![Munkaterület leválasztása automation-fiókról](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Automatizálási fiók áthelyezése

Most már áthelyezheti az Automation-fiókot és a runbookokat. 

1. Az Azure Portalon keresse meg az Automation-fiók erőforráscsoportját. Válassza **Move** > **az Áthelyezés másik előfizetésbe**lehetőséget.

    ![Erőforráscsoport lap, áthelyezés másik előfizetésre](../media/move-account/move-resources.png)

2. Jelölje ki az áthelyezni kívánt erőforrásokat az erőforráscsoportban. Győződjön meg arról, hogy tartalmazza az Automation-fiók, runbookok és a Log Analytics munkaterületi erőforrásokat.

## <a name="recreate-run-as-accounts"></a>Futtatás futtatása fiókokként

[Futtatás: Fiókok létrehozása](../manage-runas-account.md) egyszerű szolgáltatás az Azure Active Directoryban az Azure-erőforrásokhitelesítéshez. Az előfizetések módosításakor az Automation-fiók már nem használja a meglévő Futtatás másként fiókot. A Futtatás másként fiókok újbóli létrehozása:

1. Nyissa meg az Automation-fiókot az új előfizetésben, és válassza a **Futtatás fiókként** lehetőséget a **Fiókbeállítások területen.** Látni fogja, hogy a Futtatás másként fiókok most már hiányosként jelennek meg.

    ![Futtatás, mivel a fiókok hiányosak](../media/move-account/run-as-accounts.png)

2. Törölje a Futtatás másként fiókokat egyenként a Tulajdonságok lap **Törlés** gombjával. 

    > [!NOTE]
    > Ha nincs engedélye a Futtatás másként fiókok létrehozásához vagy megtekintéséhez, a következő üzenet jelenik meg: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` A Futtatás másként fiók konfigurálásához szükséges engedélyekről a Futtatás [másként fiókok konfigurálásához szükséges engedélyek](../manage-runas-account.md#permissions)című témakörben olvashat.

3. Miután törölte a Futtatás másként fiókokat, válassza a **Létrehozás** lehetőséget az **Azure Futtatás másként fiók csoportban.** 

4. Az Azure Run As fiók hozzáadása lapon válassza a **Létrehozás** lehetőséget a Futtatás fiókként és egyszerű szolgáltatás létrehozásához. 

5. Ismételje meg a fenti lépéseket az Azure Classic Run As fiókkal.

## <a name="enable-solutions"></a>Megoldások engedélyezése

Miután újra létrehozta a Futtatás másként fiókokat, újra engedélyeznie kell az áthelyezés előtt eltávolított megoldásokat: 

1. A Változáskövetés és készletmegoldás bekapcsolásához válassza a Változáskövetés és Készlet lehetőséget az Automation-fiókban. Válassza ki az áthelyezett Log Analytics munkaterületet, és válassza **az Engedélyezés lehetőséget.**

2. Ismételje meg az 1.

    ![Megoldások újbóli engedélyezése az áthelyezett Automation-fiókban](../media/move-account/reenable-solutions.png)

3. A megoldásokkal beépített gépek akkor láthatók, ha csatlakoztatta a meglévő Log Analytics-munkaterületet. A virtuális gépek indítása/leállítása munkaidőn kívüli megoldás közben bekapcsolásához újra kell telepítenie a megoldást. A **Kapcsolódó erőforrások csoportban**válassza a Virtuális gépek >  **indítása/leállítása****További információ, és a** > **Create** megoldás engedélyezése a központi telepítés elindításához lehetőséget.

4. A Megoldás hozzáadása lapon válassza ki a Log Analytics-munkaterületi és Automation-fiókját.

    ![Megoldás hozzáadása menü](../media/move-account/add-solution-vm.png)

5. Konfigurálja a megoldást a [virtuális gépek indítása/leállítása az Azure Automation munkaidőn kívüli megoldásában](../automation-solution-vm-management.md)leírtak szerint.

## <a name="verify-the-move"></a>Az áthelyezés ellenőrzése

Amikor az áthelyezés befejeződött, ellenőrizze, hogy az alább felsorolt képességek engedélyezve vannak-e. 

|Képesség|Tesztek|Hibaelhárítás|
|---|---|---|
|Runbookok|A runbookok sikeresen futtathatók, és csatlakozhatnak az Azure-erőforrásokhoz.|[Runbookok hibaelhárítása](../troubleshoot/runbooks.md)
|Verziókövetés|Manuális szinkronizálást futtathat a forrásvezérlő tárházán.|[Verziókövetés integrálása](../source-control-integration.md)|
|Változások nyomon követése és készlete|Ellenőrizze, hogy a gépek aktuális készletadatai láthatók-e.|[Változások követésének elhárítása](../troubleshoot/change-tracking.md)|
|Frissítéskezelés|Ellenőrizze, hogy látja-e a gépeket, és hogy azok kifogástalanok.Verify that you see your machines and they're healthy.</br>Futtasson tesztszoftver-frissítési központi telepítést.|[Frissítéskezelés – problémamegoldás](../troubleshoot/update-management.md)|
|Megosztott erőforrások|Ellenőrizze, hogy az összes megosztott erőforrás, például [a hitelesítő adatok,](../shared-resources/credentials.md)a változók és [hasonlók láthatók-e.](../shared-resources/variables.md)|

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni az erőforrások Azure-beli áthelyezéséről, olvassa el [az Erőforrások áthelyezése az Azure-ban](../../azure-resource-manager/management/move-support-resources.md).
