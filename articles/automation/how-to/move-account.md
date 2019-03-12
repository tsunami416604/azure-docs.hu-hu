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
ms.openlocfilehash: 01f3995a80375f2deada13c36c48600243a17623
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57733370"
---
# <a name="move-your-automation-account-to-another-subscription"></a>Az Automation-fiók áthelyezése egy másik előfizetésre

Az Azure lehetővé teszi bizonyos erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe az ugyanahhoz a bérlőhöz natív módon az Azure portal, PowerShell, az Azure CLI vagy REST API használatával. A folyamattal kapcsolatos további tudnivalókért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../../azure-resource-manager/resource-group-move-resources.md). Az Automation-fiókok tartoznak az erőforrásokat, áthelyezhetők, de van szükség, amikor az Automation-fiók áthelyezése speciális lépéseket.

Az Automation-fiókot, áthelyezése magas szintű lépései a következők:

* A megoldások eltávolítása
* A munkaterület leválasztása
* Az Automation-fiók áthelyezése
* Törölje, majd újra létrehozni a futtató fiókok
* Engedélyezze újra a megoldások

## <a name="remove-solutions"></a>Megoldások eltávolítása

Az Automation-fiók, a változás- és szoftverleltár, a munkaterületet leválasztása le, az Update Management és a gépek indítása/leállítása közben ki óra megoldások el kell távolítani a munkaterületet.

Válassza ki az erőforráscsoport egyes **megoldás** kattintson **törlése**. Az a **erőforrások törlése** lapon hagyja jóvá, el kell távolítani, majd kattintson az erőforrások **törlése**.

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

Az a **indítása és leállítása a virtuális gép** megoldásban is el kell távolítania a megoldás által létrehozott riasztási szabályok.

Az Azure Portalon keresse meg az erőforráscsoportot, és válassza ki **riasztások** alatt **figyelés**. Az a **riasztások** lapon jelölje be **riasztási szabályok kezelése**

![Riasztások kezelése riasztási szabályok kattintva megjelenítő lap](../media/move-account/alert-rules.png)

Az a **szabályok** lapon kell megjelennie a riasztásokat konfigurálni az adott erőforráscsoportba tartozó összes listája. A **virtuális gépek indítása/leállítása** megoldás 3 riasztási szabályokat hoz létre.

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Válassza ki a 3 riasztási szabályok, és kattintson a **törlése**. Ez a művelet eltávolítja a riasztási szabályok.

![Kiválasztott szabályok tartalmazó oldalt, és szabályok törlése](../media/move-account/delete-rules.png)

> [!NOTE]
> Ha nem látja az naplóriasztási szabályok a a **szabályok** lapon, módosítsa a **állapot** megjeleníthető **le van tiltva** riasztást küld, mivel előfordulhat, hogy letiltotta azokat.

Miután a riasztási szabályok el lesznek távolítva, kell a virtuális gép indítása és leállítása a megoldás az értesítésekhez létrehozott műveletcsoport eltávolításához.

Az Azure Portalon lépjen a **figyelő**, jelölje be **riasztások**, és kattintson a **Műveletcsoportok kezelése**.

A műveletcsoport kiválasztása a listából, a név fog rendelkezni **StartStop_VM_Notification**. A művelet csoportok lapon kattintson a **törlése**

![A művelet lapján Törlés gombra való kattintás](../media/move-account/delete-action-group.png)

Hasonlóképpen törölheti a műveletcsoport, a PowerShell-lel. Ez a művelet használatával történik a [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) parancsmag az alábbi példában látható módon:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>A munkaterület leválasztása

Az Azure Portalon nyissa meg a **Automation-fiók**. A **kapcsolódó erőforrások**, kattintson a **csatolt munkaterület**. Kattintson a **munkaterület leválasztása** leválasztani a munkaterületet Automation-fiókját.

![Egy munkaterületet egy Automation-fiók leválasztása](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Az Automation-fiók áthelyezése

Miután minden előző elem el lett távolítva, továbbra is eltávolítja az Automation-fiók és a runbookok. Az Azure Portalon lépjen az Automation-fiók erőforráscsoportja. Válassza ki **áthelyezése** , majd **Áttérés másik előfizetésre**.

![Erőforráscsoport lapján helyezze át egy másik előfizetés kiválasztása](../media/move-account/move-resources.png)

Válassza ki az erőforrásokat az erőforráscsoportban, amelyet át szeretne. Győződjön meg arról, akkor a **Automation-fiók**, **Runbook**, és **Log Analytics-munkaterület** erőforrásokat.

Az Áthelyezés befejezése után a nincsenek további lépéseket kell tenni, hogy minden működik.

## <a name="recreate-run-as-accounts"></a>Hozza létre újra a futtató fiókok

[Futtató fiókok](../manage-runas-account.md) egyszerű szolgáltatás létrehozása az Azure Active Directory Azure-erőforrásokkal való hitelesítéshez. Amikor módosítja az előfizetések, a meglévő futtató fiókot már nem használható által az Automation-fiókot.

Az Automation-fiók, az új előfizetés keresse meg és válassza **futtató fiókok** alatt **fiókbeállításokat**. Látni fogja, hogy a futtató fiókok megjelenítése, hiányos most.

![Futtató fiókok azt jelzi, hogy hiányos](../media/move-account/run-as-accounts.png)

Mindegyik Futtatás fiókként, majd a kattintson a **tulajdonságok** kattintson **törlése** a futtató fiók törlése.

> ! [MEGJEGYZÉS] Ha nincs engedélye létrehozni, vagy megtekintheti a futtató fiókokat, a következő üzenet jelenik meg `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.`. A futtató fiók konfigurálásához szükséges engedélyekkel kapcsolatos további információkért lásd: [futtató fiókok konfigurálásához szükséges engedélyeket](../manage-runas-account.md#permissions).

Kattintson a futtató fiókok törlését követően **létrehozás** a a **Azure futtató fiók**. Az a **Azure futtató fiók felvétele** kattintson **létrehozás** a futtató fiók és -szolgáltatásnév létrehozása. Ismételje meg az előző lépésekben a **Azure klasszikus futtató fiók**.

## <a name="enable-solutions"></a>Megoldások engedélyezése

Miután a futtató fiókok kell újból létrejött, újra engedélyeznie kell megoldást, akkor az áthelyezés előtt távolítsa el. Ahhoz, hogy **Change Tracking and Inventory** és **az Update Management**, válassza ki a megfelelő funkció az Automation-fiókban. Válassza ki a Log Analytics-munkaterületet, akkor átkerül, és kattintson a **engedélyezése**.

![Engedélyezze újra az áthelyezett Automation-fiók-megoldások](../media/move-account/reenable-solutions.png)

A gépek, amelyek a megoldásaival való előkészítve jelennek meg újra, mert a meglévő Log Analytics-munkaterülethez csatlakozik.

Kívánja újból engedélyezni a virtuális gépek indítása/leállítása munkaidőn kívül megoldás, szüksége lesz a megoldás újbóli üzembe helyezéséhez. A **kapcsolódó erőforrások**válassza **indítása és leállítása a virtuális gép**. Kattintson a **tudjon meg többet, és a megoldás engedélyezéséhez** kattintson **létrehozás** a üzembe helyezésének megkezdéséhez.

Az a **megoldás hozzáadása** lapon, válassza ki a Log Analytics-munkaterületet és Automation-fiók.  

![Futtató fiókok azt jelzi, hogy hiányos](../media/move-account/add-solution-vm.png)

A megoldás konfigurálásáról részletes utasításokért lásd: [virtuális gépek indítása/leállítása munkaidőn kívül megoldás az Azure Automationben](../automation-solution-vm-management.md)

## <a name="post-move-verification"></a>Bejegyzés áthelyezése ellenőrzése

Az áthelyezés után mindenképpen ellenőrizze a különböző forgatókönyvek az Automation-fiókban, annak érdekében, hogy minden a várt módon működik. Az alábbi táblázat a feladatlistát, amelyet érdemes ellenőrizni az Áthelyezés befejezése után:

|Képesség|Tesztek|Kapcsolat hibaelhárítása|
|---|---|---|
|Runbookok|Egy Runbook sikeresen futtathatja és Azure-erőforrásokhoz való csatlakozáshoz.|[Runbookok hibaelhárítása](../troubleshoot/runbooks.md)
| Verziókövetés|Manuális szinkronizálás futtathatja a Forrásvezérlő tárházat.|[Verziókövetés integrálása](../source-control-integration.md)|
|Change Tracking és Inventory|Győződjön meg arról, láthatja az aktuális leltári adatok a gépekről.|[A Change Tracking hibaelhárítása](../troubleshoot/change-tracking.md)|
|Frissítéskezelés|Győződjön meg arról, tekintse meg a gépek és azok kifogástalan állapotú</br>Egy teszt szoftverfrissítések központi telepítésének futtatásához.|[Az Update Management hibaelhárítása](../troubleshoot/update-management.md)|

## <a name="next-steps"></a>További lépések

Erőforrások áthelyezése az Azure-ban kapcsolatos további információkért lásd: [erőforrások áthelyezése az Azure-ban](../../azure-resource-manager/move-support-resources.md)
