---
title: Ismerje meg, hogyan készíthet Update Management, Change Tracking és leltározási megoldásokat több virtuális géphez a Azure Automation
description: Ismerje meg, hogyan készíthet Azure-beli virtuális gépeket a Azure Automation részét képező Update Management-, Change Tracking-és leltározási megoldásokkal.
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 188306ca02cee05f17514826ae6d973a17e6bb97
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421862"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Update Management-, Change Tracking-és leltározási megoldások engedélyezése több virtuális gépen

Azure Automation megoldásokat kínál az operációs rendszer biztonsági frissítéseinek kezelésére, a változások nyomon követésére és a számítógépekre telepített termékek leltározására. A gépek több módon is üzembe helyezhetők, így a megoldás [egy virtuális gépről](automation-onboard-solutions-from-vm.md), az [Automation-fiókból](automation-onboard-solutions-from-automation-account.md), a virtuális gépek böngészése vagy a [runbook](automation-onboard-solutions.md). Ez a cikk ismerteti ezeket a megoldásokat az Azure-beli virtuális gépek böngészése során.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba a https://portal.azure.com címen.

## <a name="enable-solutions"></a>Megoldások engedélyezése

A Azure Portal navigáljon a **virtuális gépekhez**.

A jelölőnégyzetek használatával válassza ki azokat a virtuális gépeket, amelyeknek a bevezetéséhez Change Tracking és leltárt vagy Update Management szeretne készíteni. A bevezetése egyszerre legfeljebb három különböző erőforráscsoport számára érhető el. Az Azure-beli virtuális gépek bármely régióban létezhetnek, függetlenül az Automation-fiókja helyétől.

![Virtuális gépek listája](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> A szűrő vezérlők használatával módosíthatja a virtuális gépek listáját, majd a felső jelölőnégyzetre kattintva kiválaszthatja a listában szereplő összes virtuális gépet.

A parancssorban kattintson a **szolgáltatások** elemre, és válassza a **change Tracking**, a **Inventory**vagy a **Update Management**lehetőséget.

> [!NOTE]
> A **change Tracking** and **Inventory** ugyanazt a megoldást használja, ha az egyik engedélyezve van, és a másik is engedélyezve van.

A következő rendszerkép a Update Management. A Change Tracking and Inventory formátuma megegyezik az elrendezéssel és a viselkedéssel.

A virtuális gépek listája úgy van szűrve, hogy csak az azonos előfizetésben és helyen lévő virtuális gépeket jelenítse meg. Ha a virtuális gépek több mint három erőforráscsoporthoz vannak, akkor az első három erőforráscsoport van kiválasztva.

### <a name="resource-group-limit"></a>Bevezetési korlátozások

Az előkészítéshez használható erőforráscsoportok számát a Resource Manager-alapú üzemelő [példányok](../azure-resource-manager/resource-manager-cross-resource-group-deployment.md)korlátozzák. A Resource Manager-alapú üzemelő példányok, amelyek nem tévesztendő össze a frissítési környezetekkel, üzembe helyezésük legfeljebb 5 erőforráscsoport lehet. A bevezetésének integritásának biztosítása érdekében az erőforráscsoportok közül 2 az Log Analytics munkaterület, az Automation-fiók és a kapcsolódó erőforrások konfigurálására van fenntartva. Így 3 erőforráscsoport közül választhat az üzembe helyezéshez. Ez a korlát csak az egyidejű bevezetésre vonatkozik, nem az Automation-megoldással felügyelhető erőforráscsoportok számára.

Emellett runbook is használhat a bevezetéshez, további információért lásd: az [Update és a Change Tracking megoldások](automation-onboard-solutions.md)bevezetésének Azure Automation.

A szűrési vezérlőkkel különböző előfizetések, helyszínek és erőforráscsoportok alapján választhatja ki a virtuális gépeket.

![Az Update Management megoldás előkészítése](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Tekintse át a Log Analytics munkaterület és az Automation-fiók választási lehetőségeit. Alapértelmezés szerint egy meglévő munkaterület és Automation-fiók van kiválasztva. Ha más Log Analytics munkaterületet és Automation-fiókot szeretne használni, kattintson az **Egyéni** elemre az **Egyéni konfiguráció** lapon való kiválasztásához. Ha Log Analytics munkaterületet választ, ellenőrizze, hogy van-e egy Automation-fiókkal társítva. Ha a rendszer csatolt Automation-fiókot talál, a következő képernyő jelenik meg. Ha elkészült, kattintson **az OK**gombra.

![Munkaterület és fiók kiválasztása](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Ha a kiválasztott munkaterület nincs Automation-fiókhoz kapcsolva, a következő képernyő jelenik meg. Válasszon ki egy Automation-fiókot, majd kattintson **az OK gombra** a befejezéshez.

![Nincs munkaterület](media/automation-onboard-solutions-from-browse/no-workspace.png)

> [!NOTE]
> A megoldások engedélyezésekor csak bizonyos régiók esetén lehet összekapcsolni egy Log Analytics-munkaterületet és egy Automation-fiókot.
>
> A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](how-to/region-mappings.md).

Törölje a jelölőnégyzet jelölését azon virtuális gépek mellett, amelyeket nem szeretne engedélyezni. A nem engedélyezhető virtuális gépek már ki vannak választva.

A megoldás engedélyezéséhez kattintson az **Engedélyezés** gombra. A megoldás engedélyezése akár 15 percet is igénybe vehet.

## <a name="unlink-workspace"></a>Munkaterület leválasztása

A következő megoldások Log Analytics munkaterülettől függenek:

* [Frissítéskezelés](automation-update-management.md)
* [Változáskövetés](automation-change-tracking.md)
* [Start/Stop VMs during off-hours](automation-solution-vm-management.md)

Ha úgy dönt, hogy már nem szeretné integrálni az Automation-fiókot egy Log Analytics munkaterülettel, közvetlenül a Azure Portalból is leválaszthatja a fiókját. Mielőtt továbblépne, először el kell távolítania a korábban említett megoldásokat, ellenkező esetben a folyamat nem fog folytatódni. Tekintse át az importált konkrét megoldásról szóló cikket az eltávolításához szükséges lépések megismeréséhez.

A megoldások eltávolítása után a következő lépések végrehajtásával leválaszthatja az Automation-fiókját.

> [!NOTE]
> Előfordulhat, hogy néhány megoldás, például az Azure SQL-figyelési megoldás korábbi verziói automatizálási eszközöket hoztak létre, és a munkaterület leválasztása előtt is el kell távolítani őket.

1. A Azure Portal nyissa meg az Automation-fiókját, és az Automation-fiók lapon válassza a **csatolt munkaterület** lehetőséget a bal oldalon található **kapcsolódó erőforrások** szakaszban.

2. A munkaterület leválasztása lapon kattintson a **munkaterület leválasztása**elemre.

   ![Munkaterület leválasztása lap](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   A rendszer felkéri, hogy erősítse meg, valóban folytani kívánja-e.

3. Míg Azure Automation megkísérli leválasztani a fiókot a Log Analytics munkaterületen, nyomon követheti a menü **értesítések** részén látható előrehaladást.

Ha a Update Management megoldást használta, érdemes lehet eltávolítani a következő elemeket, amelyekre már nincs szükség a megoldás eltávolítása után.

* Frissítési ütemtervek – minden olyan névvel rendelkezik, amely megfelel a létrehozott frissítési központi telepítésnek.

* A megoldáshoz létrehozott hibrid feldolgozói csoportok – mindegyik neve hasonló lesz a machine1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Ha a Start/Stop VMs during off-hours megoldást használta, érdemes lehet eltávolítani a következő elemeket, amelyekre már nincs szükség a megoldás eltávolítása után.

* VM runbook-ütemtervek elindítása és leállítása
* VM-runbookok elindítása és leállítása
* Változók

Azt is megteheti, hogy kikapcsolja a munkaterületet az Automation-fiókjából a Log Analytics munkaterületről. A munkaterületen válassza az **Automation-fiók** lehetőséget a **kapcsolódó erőforrások**területen. Az Automation-fiók lapon válassza a **fiók megszüntetése**lehetőséget.

## <a name="troubleshooting"></a>Hibaelhárítás

Több gép bevezetéséhez előfordulhat, hogy a **nem engedélyezhető**gépek. Különböző okok miatt előfordulhat, hogy egyes gépek nem engedélyezettek. Az alábbi részekben láthatók a **nem engedélyezhető** állapotra vonatkozó lehetséges okok a beléptetési kísérlet során.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>A virtuális gép jelentései egy másik munkaterületre mutatnak: "\<workspaceName\>".  Konfiguráció módosítása az engedélyezéshez

**OK**: Ez a hiba azt mutatja, hogy a virtuális gép, amelyhez jelentést szeretne készíteni egy másik munkaterületre.

**Megoldás**: a cél Automation-fiók és a log Analytics munkaterület módosításához kattintson a **használati konfiguráció** lehetőségre.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>Virtuálisgép-jelentések olyan munkaterületre, amely nem érhető el ebben az előfizetésben

**OK**: a virtuális gép által a következőként jelentett munkaterület:

* Egy másik előfizetésben található, vagy
* Már nem létezik, vagy
* Olyan erőforráscsoporthoz van, amelyhez nincs hozzáférési engedélye

**Megoldás**: keresse meg a munkaterülethez társított Automation-fiókot, amelyet a virtuális gép a hatókör konfigurációjának módosításával jelent a virtuális gép számára.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>A virtuális gép operációs rendszerének verziója vagy eloszlása nem támogatott

**OK:** A megoldás a Linux-disztribúciók vagy a Windows összes verziója esetében nem támogatott.

**Megoldás:** Tekintse meg a megoldás [támogatott ügyfeleinek listáját](automation-update-management.md#clients) .

### <a name="classic-vms-cannot-be-enabled"></a>A klasszikus virtuális gépek nem engedélyezhetők

**OK**: a klasszikus üzemi modellt használó virtuális gépek nem támogatottak.

**Megoldás**: telepítse át a virtuális gépet a Resource Manager-alapú üzemi modellbe. Ennek módjáról a [klasszikus üzembehelyezési modell erőforrásainak áttelepítése](../virtual-machines/windows/migration-classic-resource-manager-overview.md)című témakörben olvashat bővebben.

### <a name="vm-is-stopped-deallocated"></a>A virtuális gép le van állítva. deallocated

**OK**: a virtuális gép nem **futó** állapotban van.

**Megoldás**: ahhoz, hogy egy virtuális gépet egy megoldásba lehessen bevezetni, a virtuális gépnek futnia kell. A virtuális **gép elindítása** hivatkozásra kattintva megkezdheti a virtuális gép elindítását anélkül, hogy el kellene navigálnia az oldalról.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Virtuális gép eltávolítása Update Managementról:

* A Log Analytics munkaterületen távolítsa el a virtuális gépet a hatókör-konfigurációs `MicrosoftDefaultScopeConfig-Updates`mentett keresésével. A mentett keresések a munkaterület **általános** területén találhatók.
* Távolítsa el a [Microsoft monitoring agentet](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) vagy a [Linux rendszerhez készült log Analytics-ügynököt](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Következő lépések

Most, hogy a megoldás engedélyezve van a virtuális gépek számára, látogasson el a Update Management áttekintő cikkbe, amelyből megtudhatja, hogyan hozhat létre **frissítési központi telepítést** a gépekhez.

> [!div class="nextstepaction"]
> [Update Management – az Azure-beli virtuális gépek frissítéseinek és javításának kezelése](./automation-tutorial-update-management.md)

További oktatóanyagok a megoldásokról és azok használatáról:

* [Oktatóanyag – a virtuális gép frissítéseinek kezelése](automation-tutorial-update-management.md)

* [Oktatóanyag – szoftverek azonosítása virtuális gépen](automation-tutorial-installed-software.md)

* [Oktatóanyag – virtuális gépek változásainak megoldása](automation-tutorial-troubleshoot-changes.md)
