---
title: Az Azure Automation-fiók áthelyezése egy másik előfizetésbe
description: Ez a cikk azt ismerteti, hogyan helyezheti át az Automation-fiókot egy másik előfizetésbe
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1aa759a2984764169eb28935e095d0f7c0f90c08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969831"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Az Azure Automation-fiók áthelyezése egy másik előfizetésbe

Az Azure lehetővé teszi bizonyos erőforrások áthelyezését egy új erőforráscsoportba vagy előfizetésbe. Erőforrások áthelyezése az Azure Portalon, a PowerShell, az Azure CLI vagy a REST API-n keresztül. Ha többet szeretne megtudni a folyamatról, olvassa el az [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe című témakört.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

Az Azure Automation-fiókok az egyik áthelyezhető erőforrás. Ebben a cikkben megtudhatja, hogy hogyan helyezheti át az Automation-fiókokat egy másik erőforrásba vagy előfizetésbe.

Az Automation-fiók áthelyezésének magas szintű lépései a következők:

1. Távolítsa el a megoldásokat.
2. A munkaterület leválasztása.
3. Az Automation-fiók áthelyezése.
4. Törölje, majd hozza létre újra a Futtatás másként fiókokat.
5. Engedélyezze újra a megoldásokat.

## <a name="remove-solutions"></a>Megoldások eltávolítása

A munkaterület Automation-fiókkal való leválasztásához ezeket a megoldásokat el kell távolítani a munkaterületről:
- **Change Tracking és Inventory**
- **Frissítéskezelés**
- **Virtuális gépek indítása/leállítása munkaidőn kívül**

Az erőforráscsoportban keresse meg az egyes megoldásokat, és válassza a **Törlés**lehetőséget. Az **Erőforrások törlése** lapon erősítse meg az eltávolítandó erőforrásokat, és válassza a **Törlés gombot.**

![Megoldások törlése az Azure Portalról](../media/move-account/delete-solutions.png)

Ugyanazt a feladatot az [Eltávolítás-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) parancsmaggal is elvégezheti, ahogy az a következő példában látható:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>További lépések a virtuális gépek indítása/leállítása esetén

A **virtuális gépek indítása/leállítása** megoldás esetén el kell távolítania a megoldás által létrehozott riasztási szabályokat is.

Az Azure Portalon nyissa meg az erőforráscsoportot, és válassza a > **figyelési riasztások riasztási** > **szabályok kezelése**lehetőséget. **Monitoring**

![A Riasztáskezelése szabályok at megjelenítő riasztások lap](../media/move-account/alert-rules.png)

A **Szabályok** lapon az adott erőforráscsoportban konfigurált riasztások listáját kell látnia. A **virtuális gépek indítási és leállítási** megoldása három riasztási szabályt hoz létre:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Jelölje ki ezt a három riasztási szabályt, majd kattintson a **Törlés gombra.** Ez a művelet eltávolítja ezeket a riasztási szabályokat.

![A kijelölt szabályok törlésének megerősítését kérő szabályok oldal](../media/move-account/delete-rules.png)

> [!NOTE]
> Ha nem jelennek meg riasztási szabályok a **Szabályok** lapon, módosítsa az **Állapot** ot a **Letiltott** riasztások megjelenítéséhez, mert lehet, hogy letiltotta őket.

A riasztási szabályok eltávolításakor távolítsa el a **virtuális gépek indítási/leállítási** megoldásértesítéseihez létrehozott műveletcsoportot.

Az Azure Portalon válassza a Riasztások > **figyelése műveletcsoportok kezelése** **lehetőséget.** > **Alerts**

Válassza ki **a StartStop_VM_Notification** a listából. A műveletcsoport lapon válassza a **Törlés**lehetőséget.

![Műveletcsoport lapja, törlés kiválasztása](../media/move-account/delete-action-group.png)

Hasonlóképpen törölheti a műveletcsoportot a PowerShell használatával az [Eltávolítás-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) parancsmaggal, ahogy az a következő példában látható:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Munkaterület leválasztása

Az Azure Portalon válassza az **Automation-fiókkal** > **összekapcsolt erőforrásokkal** > **kapcsolatos munkaterületet.** Válassza **a munkaterület leválasztása** lehetőséget a munkaterület automation-fiókról való leválasztásához.

![Munkaterület leválasztása automation-fiókról](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Automatizálási fiók áthelyezése

Az előző elemek eltávolítása után folytathatja az Automation-fiók és a runbookok eltávolítását. Az Azure Portalon keresse meg az Automation-fiók erőforráscsoportját. Válassza **Move** > **az Áthelyezés másik előfizetésbe**lehetőséget.

![Erőforráscsoport lap, áthelyezés másik előfizetésre](../media/move-account/move-resources.png)

Jelölje ki az áthelyezni kívánt erőforrásokat az erőforráscsoportban. Győződjön meg arról, hogy tartalmazza az **Automation-fiók**, **Runbook**és **Log Analytics munkaterületi** erőforrásokat.

Az áthelyezés befejezése után további lépésekszükségesek ahhoz, hogy minden működjön.

## <a name="re-create-run-as-accounts"></a>Futtatás másként fiókok újbóli létrehozása

[Futtatás: Fiókok létrehozása](../manage-runas-account.md) egyszerű szolgáltatás az Azure Active Directoryban az Azure-erőforrásokhitelesítéshez. Az előfizetések módosításakor az Automation-fiók már nem használja a meglévő Futtatás másként fiókot.

Nyissa meg az Automation-fiókot az új előfizetésben, és válassza a **Futtatás fiókként** lehetőséget a **Fiókbeállítások területen.** Látni fogja, hogy a Futtatás másként fiókok most már hiányosként jelennek meg.

![Futtatás, mivel a fiókok hiányosak](../media/move-account/run-as-accounts.png)

Válassza ki az egyes Futtatás másként-fiókot. A **Tulajdonságok** lapon válassza a **Törlés** lehetőséget a Futtatás másként fiók törléséhez.

> [!NOTE]
> Ha nem rendelkezik engedéllyel a Futtatás másként fiókok létrehozásához vagy `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` megtekintéséhez, a következő üzenet jelenik meg: A Futtatás másként fiók konfigurálásához szükséges engedélyekről a [Futtatás másként fiókok konfigurálásához szükséges engedélyek](../manage-runas-account.md#permissions)című témakörben olvashat.

A Futtatás másként fiókok törlése után válassza a **Létrehozás** lehetőséget az **Azure Futtatás másként fiók csoportban.** Az **Azure Run As fiók hozzáadása lapon** válassza a **Létrehozás** lehetőséget a Futtatás fiókként és egyszerű szolgáltatás létrehozásához. Ismételje meg az előző lépéseket az **Azure Classic Run As fiókkal.**

## <a name="enable-solutions"></a>Megoldások engedélyezése

Miután újra létrehozta a Futtatás másként fiókokat, újra engedélyezni fogja az áthelyezés előtt eltávolított megoldásokat. A **Változáskövetés és a Készlet- és** **Frissítéskezelés**bekapcsolásához válassza ki a megfelelő funkciót az Automation-fiókban. Válassza ki a log analytics munkaterületet, amely fölé került, és válassza **az Engedélyezés lehetőséget.**

![Megoldások újbóli engedélyezése az áthelyezett Automation-fiókban](../media/move-account/reenable-solutions.png)

A megoldásokkal beépített gépek akkor lesznek láthatók, ha csatlakoztatja a meglévő Log Analytics-munkaterületet.

A virtuális **gépek indítása/leállítása** munkaidőn kívüli megoldás során bekapcsolásához újra kell telepítenie a megoldást. A **Kapcsolódó erőforrások csoportban**válassza a Virtuális gépek >  **indítása/leállítása****További információ, és a** > **Create** megoldás engedélyezése a központi telepítés elindításához lehetőséget.

A **Megoldás hozzáadása** lapon válassza ki a Log Analytics-munkaterületi és automatizálási fiókját.

![Megoldás hozzáadása menü](../media/move-account/add-solution-vm.png)

A megoldás konfigurálásával kapcsolatos részletes útmutatásért tekintse meg a [virtuális gépek indítása/leállítása munkaidőn kívüli megoldás a Azure Automationben című témakört.](../automation-solution-vm-management.md)

## <a name="post-move-verification"></a>Áthelyezés utáni ellenőrzés

Amikor az áthelyezés befejeződött, ellenőrizze az ellenőrizendő feladatok alábbi listáját:

|Képesség|Tesztek|Hibaelhárítási hivatkozás|
|---|---|---|
|Runbookok|A runbookok sikeresen futtathatók, és csatlakozhatnak az Azure-erőforrásokhoz.|[Runbookok hibaelhárítása](../troubleshoot/runbooks.md)
|Verziókövetés|Manuális szinkronizálást futtathat a forrásvezérlő tártárán.|[Forrás-ellenőrzési integráció](../source-control-integration.md)|
|Változások nyomon követése és készlete|Ellenőrizze, hogy láthatja-e a gépek aktuális készletadatait.|[Változások követésének elhárítása](../troubleshoot/change-tracking.md)|
|Frissítéskezelés|Ellenőrizze, hogy látja-e a gépeit, és kifogástalan állapotban vannak.Verify you see you see your machines and they're healthy.</br>Futtasson tesztszoftver-frissítési központi telepítést.|[Frissítéskezelés – problémamegoldás](../troubleshoot/update-management.md)|
|Megosztott erőforrások|Ellenőrizze, hogy az összes megosztott erőforrás, például [a hitelesítő adatok,](../shared-resources/credentials.md) [a változók](../shared-resources/variables.md)stb.|

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni az erőforrások Azure-beli áthelyezéséről, olvassa el [az Erőforrások áthelyezése az Azure-ban](../../azure-resource-manager/management/move-support-resources.md).
