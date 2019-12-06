---
title: Azure Automation-fiók áthelyezése másik előfizetésre
description: Ez a cikk azt ismerteti, hogyan helyezheti át az Automation-fiókját egy másik előfizetésbe
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2d1c747a52a1e8dedd0b5ba411b673eee463a2b6
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849581"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Azure Automation-fiók áthelyezése másik előfizetésre

Az Azure lehetővé teszi bizonyos erőforrások áthelyezését egy új erőforráscsoporthoz vagy előfizetésbe. Az erőforrásokat a Azure Portal, a PowerShell, az Azure CLI vagy a REST API használatával helyezheti át. További információ a folyamatról: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../../azure-resource-manager/resource-group-move-resources.md).

A Azure Automation fiókok az egyik áthelyezhető erőforrás. Ebben a cikkben megismerheti az Automation-fiókok másik erőforrásba vagy előfizetésbe való áthelyezésének lépéseit.

Az Automation-fiók áthelyezésének magas szintű lépései a következők:

1. Távolítsa el a megoldásait.
2. Munkaterület leválasztása.
3. Helyezze át az Automation-fiókot.
4. Törölje, majd hozza létre újra a futtató fiókokat.
5. Engedélyezze újra a megoldásokat.

## <a name="remove-solutions"></a>Megoldások eltávolítása

A munkaterület Automation-fiókból való megszüntetéséhez ezeket a megoldásokat el kell távolítani a munkaterületről:
- **Change Tracking és leltár**
- **Frissítéskezelés**
- **Virtuális gépek indítása/leállítása munkaidőn kívül**

Az erőforráscsoporthoz keresse meg az egyes megoldásokat, majd válassza a **Törlés**lehetőséget. Az **erőforrások törlése** lapon erősítse meg az eltávolítandó erőforrásokat, majd válassza a **Törlés**lehetőséget.

![Megoldások törlése a Azure Portal](../media/move-account/delete-solutions.png)

Ugyanezt a feladatot a [Remove-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) parancsmaggal is elvégezheti, ahogy az az alábbi példában is látható:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>A virtuális gépek indítására és leállítására vonatkozó további lépések

A **virtuális gépek indítása/leállítása** megoldáshoz el kell távolítania a megoldás által létrehozott riasztási szabályokat is.

A Azure Portal nyissa meg az erőforráscsoportot, és válassza a **figyelés** > **riasztások** > a **riasztási szabályok kezelése**lehetőséget.

![A riasztási szabályok kezelésére szolgáló riasztások lapja](../media/move-account/alert-rules.png)

A **szabályok** lapon az adott erőforráscsoporthoz konfigurált riasztások listája látható. A **virtuális gépek indítása/leállítása** megoldás három riasztási szabályt hoz létre:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Válassza ki ezt a három riasztási szabályt, majd válassza a **Törlés**lehetőséget. Ez a művelet eltávolítja ezeket a riasztási szabályokat.

![A kijelölt szabályok törlésének megerősítését kérő szabályok lap](../media/move-account/delete-rules.png)

> [!NOTE]
> Ha nem lát riasztási szabályt a **szabályok** lapon, módosítsa az **állapotot** a **letiltott** riasztások megjelenítéséhez, mert lehetséges, hogy letiltotta őket.

A riasztási szabályok eltávolításakor távolítsa el a **virtuális gépek indítása/leállítása** megoldás értesítéseihez létrehozott műveleti csoportot.

A Azure Portal válassza a **figyelő** > **riasztások** > a **műveleti csoportok kezelése**lehetőséget.

Válassza ki **StartStop_VM_Notification** a listából. A műveleti csoport lapon válassza a **Törlés**lehetőséget.

![Műveleti csoport lap, válassza a Törlés lehetőséget.](../media/move-account/delete-action-group.png)

Hasonlóképpen törölheti a műveleti csoportot a PowerShell és a [Remove-azurermactiongroup parancsmag](/powershell/module/azurerm.insights/remove-azurermactiongroup) parancsmag használatával is, az alábbi példában látható módon:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Munkaterület leválasztása

A Azure Portal válassza az **Automation-fiók** > **kapcsolódó erőforrások** > **csatolt munkaterület**elemet. Válassza a **munkaterület** megszüntetése lehetőséget a munkaterület Automation-fiókból való leválasztásához.

![Munkaterület leválasztása Automation-fiókból](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Az Automation-fiók áthelyezése

Az előző elemek eltávolítása után továbbra is eltávolíthatja az Automation-fiókját és annak runbookok. A Azure Portal tallózással keresse meg az Automation-fiókja erőforrás-csoportját. Válassza az **áthelyezés** > **Áthelyezés másik előfizetésre**lehetőséget.

![Erőforráscsoport oldal, áthelyezés másik előfizetésre](../media/move-account/move-resources.png)

Válassza ki az áthelyezni kívánt erőforráscsoport erőforrásait. Győződjön meg arról, hogy az **Automation-fiókja**, a **Runbook**és a **log Analytics munkaterület** erőforrásai is megtalálhatók.

Az áthelyezés befejezését követően további lépések szükségesek a munka elvégzéséhez.

## <a name="re-create-run-as-accounts"></a>Futtató fiókok újbóli létrehozása

A [futtató fiókok](../manage-runas-account.md) az Azure-erőforrásokkal való hitelesítéshez Azure Active Directory egyszerű szolgáltatásnevet hoznak létre. Az előfizetések módosításakor az Automation-fiók már nem használja a meglévő futtató fiókot.

Nyissa meg az Automation-fiókját az új előfizetésben, és válassza a **fiók beállításai**alatt a **futtató fiókok** elemet. Láthatja, hogy a futtató fiókok már nem teljesek.

![A futtató fiókok hiányosak](../media/move-account/run-as-accounts.png)

Válassza ki az egyes futtató fiókokat. A **Tulajdonságok** lapon válassza a **Törlés** lehetőséget a futtató fiók törléséhez.

> [!NOTE]
> Ha nincs engedélye a futtató fiókok létrehozására vagy megtekintésére, a következő üzenet jelenik meg: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` a futtató fiók konfigurálásához szükséges engedélyek megismeréséhez lásd a [futtató fiókok konfigurálásához szükséges](../manage-runas-account.md#permissions)engedélyeket.

A futtató fiókok törlése után válassza a **Létrehozás** az Azure-beli **futtató fiókban**lehetőséget. Az Azure-beli **futtató fiók hozzáadása** lapon válassza a **Létrehozás** lehetőséget a futtató fiók és az egyszerű szolgáltatásnév létrehozásához. Ismételje meg az előző lépéseket a **klasszikus Azure-beli futtató fiókkal**.

## <a name="enable-solutions"></a>Megoldások engedélyezése

Miután újra létrehozta a futtató fiókokat, újra engedélyezni fogja azokat a megoldásokat, amelyeket az áthelyezés előtt eltávolítottak. A **change Tracking és a leltár** és a **Update Management**bekapcsolásához válassza ki a megfelelő képességet az Automation-fiókjában. Válassza ki az áthelyezett Log Analytics munkaterületet, és válassza az **Engedélyezés**lehetőséget.

![Az áthelyezett Automation-fiókban lévő megoldások ismételt engedélyezése](../media/move-account/reenable-solutions.png)

A megoldásokkal bekészített gépek a meglévő Log Analytics munkaterület csatlakoztatásakor lesznek láthatók.

Ha be szeretné kapcsolni a **virtuális gépek elindítása/leállítása** a munkaidőn kívüli megoldásban, újra kell telepítenie a megoldást. A **kapcsolódó erőforrások**területen válassza a **virtuális gépek indítása/leállítása** > további **információ: és a megoldás engedélyezése** > **létrehozásához** a telepítés elindításához.

A **megoldás hozzáadása** lapon válassza ki a log Analytics-munkaterületet és az Automation-fiókot.

![Megoldás hozzáadása menü](../media/move-account/add-solution-vm.png)

A megoldás konfigurálásának részletes ismertetését lásd: [Start/Stop VMS During off-hours megoldás a Azure Automationban](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Áthelyezés utáni ellenőrzés

Ha az áthelyezés befejeződött, ellenőrizze az alábbi, ellenőrzött feladatok listáját:

|Szolgáltatás|Tesztek|Hibaelhárítási hivatkozás|
|---|---|---|
|Runbookok|Egy runbook sikeresen futtatható és kapcsolódhat az Azure-erőforrásokhoz.|[Runbookok hibaelhárítása](../troubleshoot/runbooks.md)
|Verziókövetés|A verziókövetés tárházán manuális szinkronizálást is futtathat.|[Forráskezelés integrálása](../source-control-integration.md)|
|Change Tracking and Inventory|Ellenőrizze, hogy az aktuális leltározási adatok láthatók-e a gépekről.|[Változások követésének hibakeresése](../troubleshoot/change-tracking.md)|
|Frissítéskezelés|Ellenőrizze, hogy látja-e a gépeket, és hogy kifogástalanok-e.</br>Futtasson egy teszt szoftverfrissítés központi telepítését.|[Az Update Management hibáinak megoldása](../troubleshoot/update-management.md)|
|Közös erőforrások|Ellenőrizze, hogy látható-e az összes megosztott erőforrás, például a [hitelesítő adatok](../shared-resources/credentials.md), a [változók](../shared-resources/variables.md)stb.|

## <a name="next-steps"></a>Következő lépések

További információ az erőforrások Azure-beli áthelyezéséről: [erőforrások áthelyezése az Azure-ban](../../azure-resource-manager/move-support-resources.md).
