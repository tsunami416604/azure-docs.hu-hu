---
title: Az Azure Automation-fiók áthelyezése egy másik előfizetésre
description: Ez a cikk bemutatja az Automation-fiók áthelyezése egy másik előfizetésre
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59d433bfb888eaa41cc8f66bdf3ad28c16efbe5c
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225960"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Az Azure Automation-fiók áthelyezése egy másik előfizetésre

Az Azure lehetővé teszi bizonyos erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe. Továbbléphet az erőforrásokat az Azure portal, PowerShell, az Azure CLI vagy a REST API használatával. A folyamattal kapcsolatos további tudnivalókért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../../azure-resource-manager/resource-group-move-resources.md). 

Azure Automation-fiókok áthelyezhető erőforrások tartoznak. Ebből a cikkből megismerheti a lépéseket az Automation-fiókok áthelyezése egy másik erőforrás vagy előfizetésbe.

Az Automation-fiók áthelyezése magas szintű lépései a következők:

1. Távolítsa el a megoldásokat.
2. A munkaterület leválasztása.
3. Az Automation-fiók áthelyezése.
4. Törölje és hozza létre újból a futtató fiókokat.
5. A megoldások engedélyezze újra.

## <a name="remove-solutions"></a>Megoldások eltávolítása

Az Automation-fiókjából a munkaterületet leválasztása le, ezek a megoldások el kell távolítani a munkaterülethez:
- **Change Tracking and Inventory**
- **Frissítéskezelés** 
- **Gépek indítása/leállítása közben csúcsidőn kívüli órákra** 

Az erőforráscsoportban, keresse meg az egyes megoldások, és válassza ki **törlése**. Az a **erőforrások törlése** lapon hagyja jóvá a távolítható el, és válassza ki az erőforrásokat **törlése**.

![Törölje a megoldások az Azure Portalról](../media/move-account/delete-solutions.png)

Ugyanez a feladat egy végezheti a [Remove-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) parancsmag az alábbi példában látható módon:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>További lépéseket a virtuális gépek indítása/leállítása

Az a **virtuális gépek indítása/leállítása** megoldásban is el kell távolítania a megoldás által létrehozott riasztási szabályok.

Az Azure Portalon nyissa meg az erőforráscsoport, és válassza ki **figyelés** > **riasztások** > **riasztási szabályok kezelése**.

![Riasztások kezelése riasztási szabályok lapot ábrázoló kiválasztása](../media/move-account/alert-rules.png)

Az a **szabályok** oldalon azt kell látnia, az erőforráscsoport konfigurálva a riasztások listáját. A **virtuális gépek indítása/leállítása** megoldás három riasztási szabályokat hoz létre:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Válassza a három riasztási szabályok, és válassza ki **törlése**. Ez a művelet eltávolítja a riasztási szabályok.

![A kiválasztott szabályok törlésekor megerősítést kér szabályok lap](../media/move-account/delete-rules.png)

> [!NOTE]
> Ha bármely riasztási szabályok nem jelenik meg a **szabályok** lapon, módosítsa a **állapota** megjeleníthető **le van tiltva** riasztást küld, mivel előfordulhat, hogy letiltotta azokat.

A riasztási szabályok el lesznek távolítva, távolítsa el a műveletcsoport létrehozott címtárakat a **virtuális gépek indítása/leállítása** megoldás értesítések.

Az Azure Portalon válassza ki a **figyelő** > **riasztások** > **Műveletcsoportok kezelése**.

Válassza ki **StartStop_VM_Notification** a listából. A műveleti csoport oldalán válassza **törlése**.

![A művelet lapján válassza a Törlés](../media/move-account/delete-action-group.png)

Hasonlóképpen törölheti a műveletcsoport a PowerShell használatával a [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) parancsmag, az alábbi példában látható módon:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>A munkaterület leválasztása

Az Azure Portalon válassza ki a **Automation-fiók** > **kapcsolódó erőforrások** > **csatolt munkaterület**. Válassza ki **munkaterület leválasztása** leválasztani a munkaterületet Automation-fiókját.

![Egy munkaterületet egy Automation-fiók leválasztása](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Az Automation-fiók áthelyezése

Az előző elemek eltávolítása, után továbbra is eltávolítja az Automation-fiók és a runbookok. Az Azure Portalon keresse meg az erőforráscsoport, az Automation-fiók. Válassza ki **áthelyezése** > **Áttérés másik előfizetésre**.

![Erőforráscsoport oldalán áthelyezése egy másik előfizetésre](../media/move-account/move-resources.png)

Válassza ki az erőforrásokat az erőforráscsoportban, amelyet át szeretne. Győződjön meg arról, akkor a **Automation-fiók**, **Runbook**, és **Log Analytics-munkaterület** erőforrásokat.

Az Áthelyezés befejezése után nincsenek további lépéseket kell végrehajtani, minden rendben működik.

## <a name="re-create-run-as-accounts"></a>Hozza létre újból a futtató fiókok

[Futtató fiókok](../manage-runas-account.md) egyszerű szolgáltatás létrehozása az Azure Active Directory Azure-erőforrásokkal való hitelesítéshez. Amikor módosítja az előfizetések, az Automation-fiók már nem használja a meglévő futtató fiókot.

Nyissa meg az Automation-fiók, az új előfizetés, és válassza ki **futtató fiókok** alatt **fiókbeállításokat**. Látni fogja, hogy a futtató fiókok megjelenítése, hiányos most.

![Futtató fiókok nem teljesek.](../media/move-account/run-as-accounts.png)

Válasszon ki minden futtató fiókot. Az a **tulajdonságok** lapon jelölje be **törlése** a futtató fiók törlése.

> [!NOTE]
> Ha nem rendelkezik engedélyekkel hozhatók létre vagy tekinthetők meg a futtató fiókokat, a következő üzenetet láthatja: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` A futtató fiók konfigurálásához szükséges engedélyekkel kapcsolatos további információkért lásd: [futtató fiókok konfigurálásához szükséges engedélyeket](../manage-runas-account.md#permissions).

A futtató fiókok törlését követően válassza ki a **létrehozás** alatt **Azure-beli futtató fiók**. Az a **hozzáadása Azure-beli futtató fiók** lapon jelölje be **létrehozás** hozhat létre a futtató fiók és a szolgáltatás egyszerű. Ismételje meg az előző lépésekben a **Azure-beli klasszikus futtató fiók**.

## <a name="enable-solutions"></a>Megoldások engedélyezése

Miután újra hozza létre a futtató fiókokat, újra engedélyeznie kell a megoldások az áthelyezés előtt eltávolított. Bekapcsolása **Change Tracking and Inventory** és **az Update Management**, válassza ki a megfelelő funkció az Automation-fiókban. A Log Analytics-munkaterületet, akkor átkerül, és válasszon **engedélyezése**.

![Engedélyezze újra az áthelyezett Automation-fiók-megoldások](../media/move-account/reenable-solutions.png)

Gépek, amelyek előkészítve a megoldásaival való látható lesz, ha csatlakozott a meglévő Log Analytics-munkaterület.

Bekapcsolása a **virtuális gépek indítása/leállítása** munkaidőn kívül megoldás, szüksége lesz a megoldás újbóli üzembe helyezéséhez. A **kapcsolódó erőforrások**válassza **virtuális gépek indítása/leállítása** > **tudjon meg többet, és a megoldás engedélyezéséhez** > **létrehozása** a üzembe helyezésének megkezdéséhez.

Az a **megoldás hozzáadása** lapon, válassza ki a Log Analytics-munkaterületet és Automation-fiókot.  

![Adja hozzá a megoldás menü](../media/move-account/add-solution-vm.png)

A megoldás konfigurálásáról részletes utasításokért lásd: [virtuális gépek indítása/leállítása munkaidőn kívül megoldás az Azure Automationben](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Ellenőrzési utáni áthelyezése

Az áthelyezés befejeződése után ellenőrizze a feladatokat, amelyek ellenőrizni kell a következők közül:

|Képesség|Tesztek|Kapcsolat hibaelhárítása|
|---|---|---|
|Runbookok|Egy runbook sikeresen futtathatja és Azure-erőforrásokhoz való csatlakozáshoz.|[Runbookok hibaelhárítása](../troubleshoot/runbooks.md)
| Verziókövetés|Manuális szinkronizálás futtathatja a Forrásvezérlő tárházat.|[Forráskezelés integrálása](../source-control-integration.md)|
|A Change tracking és inventory|Győződjön meg arról, láthatja az aktuális leltári adatok a gépekről.|[A change tracking hibaelhárítása](../troubleshoot/change-tracking.md)|
|Frissítéskezelés|Ellenőrizze a gépek látja, és azok kifogástalan állapotú.</br>Egy teszt szoftverfrissítések központi telepítésének futtatásához.|[Az update management hibaelhárítása](../troubleshoot/update-management.md)|

## <a name="next-steps"></a>További lépések

Erőforrások áthelyezése az Azure-ban kapcsolatos további információkért lásd: [erőforrások áthelyezése az Azure-ban](../../azure-resource-manager/move-support-resources.md).
