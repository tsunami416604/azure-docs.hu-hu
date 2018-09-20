---
title: Ismerje meg, hogyan kell előkészíteni az Update Management, Change Tracking and Inventory megoldások több virtuális gép az Azure Automationben
description: Ismerje meg, hogyan üzembe helyezni egy Azure-beli virtuális gépek az Azure Automation részét képező az Update Management, Change Tracking and Inventory megoldásaival
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: c326232e0fc8f5f878241186eac8ae5ed23f0958
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366760"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Az Update Management, Change Tracking és Inventory megoldások a több virtuális gépen

Az Azure Automation segítségével a megoldások kezelése az operációs rendszer biztonsági frissítéseit, nyomon követéséhez és szoftverleltár, a számítógépeken telepítve legyen. Gépek előkészítésének több módja van, a segítségével készítheti elő a megoldás [a virtuális gépről](automation-onboard-solutions-from-vm.md), az a [Automation-fiók](automation-onboard-solutions-from-automation-account.md), amikor a virtuális gépek tallózásával vagy által [runbook](automation-onboard-solutions.md). Ez a cikk előkészítést részletezi ezeket a megoldásokat az Azure virtual machines böngészésekor.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-ba a https://portal.azure.com címen

## <a name="enable-solutions"></a>Megoldások engedélyezése

Az Azure Portalon lépjen **virtuális gépek**.

Használja a jelölőnégyzeteket, válassza ki a virtuális gépek felvétele a Change Tracking és Inventory vagy az Update Management pedig kíván. Bevezetési egyszerre akár három különböző erőforráscsoportokban érhető el.

![Virtuális gépek listája](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> A szűrővezérlők használatával módosítsa a virtuális gépek listáját, és kattintson a listában jelölje ki az összes virtuális gép a felső jelölőnégyzet bejelölésével.

A parancssávon kattintson **szolgáltatások** válassza **a Change tracking**, **készlet**, vagy **az Update Management**.

> [!NOTE]
> **A Change tracking** és **készlet** használja ugyanazt a megoldást, ha engedélyezve van egy másik engedélyezve van, valamint.

Az alábbi képen az Update Management van. A Change tracking és Inventory ugyanazon elrendezés és viselkedés rendelkezik.

A virtuális gépek listájának csak az azonos előfizetésben és helyen lévő virtuális gépek van szűrve. Ha a virtuális gépek legfeljebb három erőforráscsoportokban, az első három erőforráscsoportok ki van jelölve.

### <a name="resource-group-limit"></a> Regisztrációs korlátozások

A bevezetési használható erőforráscsoportok száma korlátozza a [Resource Manager üzembe helyezési korlátok](../azure-resource-manager/resource-manager-cross-resource-group-deployment.md). Resource Manager üzembe helyezések, a nem tévesztendő össze a központi telepítések, 5 erőforráscsoportok üzemelő példányonként korlátozódnak. Ahhoz, hogy a bevezetési integritását, 2 ezeket az erőforrás-csoportok konfigurálása a Log Analytics-munkaterületet, az Automation-fiók és a kapcsolódó erőforrások vannak fenntartva. Kihasználatlan marad, az üzembe helyezéshez válassza ki a 3 erőforráscsoportokhoz.

A szűrővezérlők segítségével válassza ki a virtuális gépek különböző előfizetésekben találhatóak, helyek és erőforráscsoportok.

![Az Update management megoldás előkészítése](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Tekintse át az egyik lehetőséget a Log analytics-munkaterületet és Automation-fiók. Egy új munkaterületet és Automation-fiók ki van jelölve, alapértelmezés szerint. Ha rendelkezik egy meglévő Log Analytics-munkaterületet és Automation-fiókot szeretné-e használni, kattintson a **módosítása** válassza ki azokat a **konfigurációs** lapot. Ha befejezte, kattintson a **Mentés** gombra.

![Válassza ki a munkaterületet és fiókot](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Kapcsolja ki bármelyik virtuális gépet, amely nem szeretné engedélyezni, jelölőnégyzetét. Virtuális gépek, amelyek nem lehet engedélyezni a rendszer már sincs kijelölve.

Kattintson a **engedélyezése** a megoldás engedélyezéséhez. A megoldás engedélyezése akár 15 percet is igénybe vehet.

## <a name="troubleshooting"></a>Hibaelhárítás

Amikor bevezetése több gépet, előfordulhat, hogy azt mutatják be, mint gépek **nem lehet engedélyezni a**. Másik oka miért bizonyos számítógépeken nem lehet engedélyezni. A következő szakaszok bemutatják a hiba lehetséges okai a **nem lehet engedélyezni a** állapot, a virtuális gép előkészítése tett kísérlet közben.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>Virtuális gép a jelentéseket egy másik munkaterülethez: "\<workspaceName\>".  Engedélyezésének használandó konfigurációjának módosítása

**OK**: Ez a hiba azt mutatja, hogy a virtuális gép, amely egy másik munkaterülethez előkészítése jelentések kívánt.

**Megoldás**: kattintson a **konfiguráció használható** a megcélzott Automation-fiók és a Log Analytics-munkaterületen módosíthatja.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>VM-jelentést a munkaterület, amely nem érhető el ebben az előfizetésben

**OK**: A munkaterület, amely a virtuális gép a jelentéseket:

* Egy másik előfizetésben található, vagy
* Már nem létezik, vagy
* Egy erőforráscsoportban található, nem rendelkezik a hozzáférési engedélyek

**Megoldás**: keresse meg az automation-fiókhoz társítva, amely a virtuális gép a jelentéseket a munkaterületen, és előkészítheti a virtuális gép a hatókör-konfiguráció módosításával.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>Virtuális gép operációs rendszer verziója vagy a disztribúció nem támogatott

**OK:** a megoldás az összes Linux-disztribúciók vagy a Windows összes verziója nem támogatott.

**Megoldás:** tekintse meg a [támogatott ügyfelek listáját](automation-update-management.md#clients) a megoldáshoz.

### <a name="classic-vms-cannot-be-enabled"></a>Klasszikus virtuális gépeket nem lehet engedélyezni.

**OK**: a klasszikus üzemi modellt használó virtuális gépek nem támogatottak.

**Megoldás**: a virtuális gép áttelepítése a resource manager-alapú üzemi modellbe. Ezzel kapcsolatban lásd: [klasszikus üzemi modell erőforrások áttelepítése](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>Virtuális gép leállt. (felszabadítva)

**OK**: A virtuális gép nem található egy **futó** állapota.

**Megoldás**: annak érdekében, hogy készítse elő egy megoldást a virtuális gép virtuális Gépen kell futnia. Kattintson a **VM indítása** szövegbeli hivatkozást anélkül szeretné elindítani a virtuális gép a lapra navigál.

## <a name="next-steps"></a>További lépések

Most, hogy a megoldás engedélyezve van a virtuális gépek, látogasson el a frissítéskezelési áttekintő cikkből megtudhatja, a gépek a frissítésfelmérés megtekintése.

> [!div class="nextstepaction"]
> [Az Update Management – a frissítésfelmérés megtekintése](./automation-update-management.md#viewing-update-assessments)

További oktatóanyagok a megoldásokat és azok használatát:

* [Oktatóanyag – a virtuális gép frissítéseinek kezelése](automation-tutorial-update-management.md)

* [Oktatóanyag – virtuális gépek szoftvereinek azonosítása](automation-tutorial-installed-software.md)

* [Oktatóanyag – a virtuális gép módosításainak hibaelhárítása](automation-tutorial-troubleshoot-changes.md)