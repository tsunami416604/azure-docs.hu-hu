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
ms.openlocfilehash: f1607a7d795e3934881429feb18c711a75995e31
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062944"
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

Tekintse át az egyik lehetőséget a Log analytics-munkaterületet és Automation-fiók. Egy meglévő munkaterületet és Automation-fiók ki van jelölve, alapértelmezés szerint. Ha egy másik Log Analytics-munkaterületet és Automation-fiókot használni szeretne, kattintson a **egyéni** válassza ki azokat a **egyéni konfiguráció** lapot. Ha úgy dönt, hogy a Log Analytics-munkaterülettel, egy ellenőrzési kérés érkezett határozza meg, ha egy olyan Automation-fiókkal társított. Ha egy összekapcsolt Automation-fiókot talál, látni fogja az alábbi képernyőn. Ha elkészült, kattintson a **OK**.

![Válassza ki a munkaterületet és fiókot](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Ha a kiválasztott munkaterület nincs Automation-fiók van csatolva, látni fogja az alábbi képernyőn. Válasszon egy Automation-fiókot, és kattintson a **OK** teljes.

![Nincs munkaterület](media/automation-onboard-solutions-from-browse/no-workspace.png)

Ha engedélyezte a megoldásokat, csak bizonyos régiókban támogatottak egy Log Analytics-munkaterületet és Automation-fiók összekapcsolása.

Az alábbi táblázat bemutatja a támogatott leképezések:

|**Log Analytics-munkaterület régiója**|**Az Azure Automation-régió**|
|---|---|
|Délkelet-Ausztrália|Délkelet-Ausztrália|
|CanadaCentral|CanadaCentral|
|CentralIndia|CentralIndia|
|USA keleti régiója|EastUS2|
|JapanEast|JapanEast|
|SoutheastAsia|SoutheastAsia|
|Régiója|Régiója|
|WestEurope|WestEurope|
|UKSouth|UKSouth|
|USGovVirginia|USGovVirginia|
|EastUS2EUAP|CentralUSEUAP|

Kapcsolja ki bármelyik virtuális gépet, amely nem szeretné engedélyezni, jelölőnégyzetét. Virtuális gépek, amelyek nem lehet engedélyezni a rendszer már sincs kijelölve.

Kattintson a **engedélyezése** a megoldás engedélyezéséhez. A megoldás engedélyezése akár 15 percet is igénybe vehet.

## <a name="unlink-workspace"></a>Munkaterület leválasztása

A következő megoldásokat a Log Analytics-munkaterület függnek:

* [Frissítéskezelés](automation-update-management.md)
* [Változáskövetés](automation-change-tracking.md)
* [Virtuális gépek indítása/leállítása munkaidőn kívül](automation-solution-vm-management.md)

Ha úgy dönt, hogy már nem szeretne az Automation-fiók integrálása a Log Analytics-szel, megszüntetheti a fiók közvetlenül az Azure Portalról. Mielőtt továbblépne, először el kell távolítania a megoldások azt korábban említettük, ellenkező esetben ez a folyamat megakadályozza a folytatás. Tekintse át a cikk az adott megoldás importált távolítsa el a szükséges lépések megértéséhez.

Miután eltávolítja ezeket a megoldásokat, az Automation-fiók leválasztása a következő lépéseket végezheti.

> [!NOTE]
> Egyes megoldások, beleértve az Azure SQL-figyelési megoldás a korábbi lehet, hogy létre automation-adategységeket, és is szükség lehet a munkaterület leválasztása előtt el kell távolítani.

1. Az Azure Portalról nyissa meg az Automation-fiókját, és a az Automation-fiók oldalon válassza ki **csatolt munkaterület** a szakaszában **kapcsolódó erőforrások** a bal oldalon.

2. A leválasztás munkaterület oldalán kattintson a **munkaterület leválasztása**.

   ![Munkaterület oldalán leválasztása](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   A rendszer felkéri, hogy erősítse meg, valóban folytani kívánja-e.

3. Bár az Azure Automation próbál meg a fiók leválasztása a Log Analytics-munkaterület, nyomon követheti a folyamat állapotát **értesítések** a menüből.

Az Update Management megoldás használata esetén igény szerint, előfordulhat, hogy el kívánja távolítani a következő elemek, amelyek a megoldás eltávolítása után már nem szükséges.

* Frissítés ütemezése – a neve megegyezik a létrehozott frissítéstelepítések lesz)

* A megoldás - számára létrehozott hibrid feldolgozócsoportok egyes lesznek elnevezve hasonlóan a: gép1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Virtuális gépek indítása/leállítása munkaidőn kívül megoldás használata esetén igény szerint, előfordulhat, hogy el kívánja távolítani a következő elemek, amelyek a megoldás eltávolítása után már nem szükséges.

* Elindíthatja és leállíthatja a virtuális gép runbook ütemezése
* Virtuális gép runbookok elindítása és leállítása
* Változók

## <a name="troubleshooting"></a>Hibaelhárítás

Amikor bevezetése több gépet, előfordulhat, hogy azt mutatják be, mint gépek **nem lehet engedélyezni a**. Másik oka miért bizonyos számítógépeken nem lehet engedélyezni. A következő szakaszok bemutatják a hiba lehetséges okai a **nem lehet engedélyezni a** állapot, a virtuális gép előkészítése tett kísérlet közben.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>Virtuális gép a jelentéseket egy másik munkaterülethez: "\<workspaceName\>".  Engedélyezésének használandó konfigurációjának módosítása

**OK**: Ez a hiba azt mutatja, hogy a virtuális gép, amely egy másik munkaterülethez előkészítése jelentések kívánt.

**Megoldás**: Kattintson a **konfiguráció használható** a megcélzott Automation-fiók és a Log Analytics-munkaterületen módosíthatja.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>VM-jelentést a munkaterület, amely nem érhető el ebben az előfizetésben

**OK**: A munkaterület, amely a virtuális gép a jelentéseket:

* Egy másik előfizetésben található, vagy
* Már nem létezik, vagy
* Egy erőforráscsoportban található, nem rendelkezik a hozzáférési engedélyek

**Megoldás**: Keresse meg az automation-fiók társítva, amely a virtuális gép a jelentéseket a munkaterületen, és előkészítheti a virtuális gép a hatókör-konfiguráció módosításával.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>Virtuális gép operációs rendszer verziója vagy a disztribúció nem támogatott

**OK:** A megoldás az összes Linux-disztribúciók vagy a Windows összes verziója nem támogatott.

**Megoldás:** Tekintse meg a [támogatott ügyfelek listáját](automation-update-management.md#clients) a megoldáshoz.

### <a name="classic-vms-cannot-be-enabled"></a>Klasszikus virtuális gépeket nem lehet engedélyezni.

**OK**: A klasszikus üzemi modellt használó virtuális gépek nem támogatottak.

**Megoldás**: A virtuális gép áttelepítése a resource manager-alapú üzemi modellbe. Ezzel kapcsolatban lásd: [klasszikus üzemi modell erőforrások áttelepítése](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>Virtuális gép leállt. (felszabadítva)

**OK**: A virtuális gép nem található egy **futó** állapota.

**Megoldás**: Annak érdekében, hogy készítse elő egy megoldást a virtuális gép virtuális Gépen kell futnia. Kattintson a **VM indítása** szövegbeli hivatkozást anélkül szeretné elindítani a virtuális gép a lapra navigál.

## <a name="next-steps"></a>További lépések

Most, hogy a megoldás engedélyezve van a virtuális gépek, látogasson el a frissítéskezelési áttekintő cikkből megtudhatja, a gépek a frissítésfelmérés megtekintése.

> [!div class="nextstepaction"]
> [Az Update Management – a frissítésfelmérés megtekintése](./automation-update-management.md#viewing-update-assessments)

További oktatóanyagok a megoldásokat és azok használatát:

* [Oktatóanyag – a virtuális gép frissítéseinek kezelése](automation-tutorial-update-management.md)

* [Oktatóanyag – virtuális gépek szoftvereinek azonosítása](automation-tutorial-installed-software.md)

* [Oktatóanyag – a virtuális gép módosításainak hibaelhárítása](automation-tutorial-troubleshoot-changes.md)