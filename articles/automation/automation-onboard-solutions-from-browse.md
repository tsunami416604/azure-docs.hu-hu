---
title: Megtudhatja, hogyan lehet több virtuális gép frissítéskezelési, változáskövetési és készletmegoldásait az Azure Automationben
description: Ismerje meg, hogyan lehet az Azure Automation részét használó frissítéskezelési, változáskövetési és készletmegoldásokkal rendelkező Azure-virtuális gépeket beszállni
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: f5f18e9365b09f06c1bd4f25a8efe909cc308dad
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537015"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Frissítéskezelés, változáskövetés és készletmegoldások engedélyezése több virtuális gépen

Az Azure Automation megoldásokat kínál az operációs rendszer biztonsági frissítéseinek kezelésére, a változások nyomon követésére és a számítógépekre telepített állapotok leltározására. A gépek et többféleképpen is belehet vezetni, a megoldást [egy virtuális gépről,](automation-onboard-solutions-from-vm.md)az [Automation-fiókból](automation-onboard-solutions-from-automation-account.md), a virtuális gépek böngészésekor vagy [a runbook segítségével](automation-onboard-solutions.md)is belehet vezetni. Ez a cikk ismerteti a bevezetésezeket a megoldásokat, amikor az Azure-ban lévő virtuális gépek böngészése.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba a https://portal.azure.com címen.

## <a name="enable-solutions"></a>Megoldások engedélyezése

Az Azure Portalon keresse meg a **virtuális gépek**.

A jelölőnégyzeteket használva jelölje ki azokat a virtuális gépeket, amelyeket a Változáskövetés és Készletkezelés vagy frissítéskezelés segítségével szeretne beszállni. Bevezetés érhető el legfeljebb három különböző erőforráscsoportok egy időben. Az Azure virtuális gépek létezhetnek bármely régióban, függetlenül az Automation-fiók helyétől.

![Virtuális gépek listája](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> A szűrővezérlők segítségével módosítsa a virtuális gépek listáját, majd kattintson a felső jelölőnégyzetre a listában szereplő összes virtuális gép kijelöléséhez.

A parancssávon kattintson a **Szolgáltatások** elemre, és válassza a **Követés módosítása**, **Készlet**vagy **Frissítéskezelés lehetőséget.**

> [!NOTE]
> A változáskövetés és a készlet ugyanazt a megoldást használja. Ha az egyik engedélyezve van, a másik is engedélyezve van.

Az alábbi kép az Update Management. A változáskövetés és a készlet elrendezése és viselkedése megegyezik.

A virtuális gépek listája szűrve van, hogy csak azokat a virtuális gépeket jelenítse meg, amelyek ugyanabban az előfizetésben és helyen vannak. Ha a virtuális gépek háromnál több erőforráscsoportban vannak, az első három erőforráscsoport lesz kiválasztva.

### <a name="onboarding-limitations"></a><a name="resource-group-limit"></a>Bevezetési korlátozások

A bevezetéshez használható erőforráscsoportok számát az [Erőforrás-kezelő telepítési korlátai korlátozzák.](../azure-resource-manager/templates/cross-resource-group-deployment.md) Az Erőforrás-kezelő központi telepítései, nem tévesztendők össze a frissítési központi telepítésekkel, telepítésenként legfeljebb 5 erőforráscsoportra korlátozódnak. A bevezetés integritásának biztosítása érdekében ezen erőforráscsoportok közül 2 van fenntartva a Log Analytics-munkaterület, az Automation-fiók és a kapcsolódó erőforrások konfigurálásához. Így 3 erőforráscsoportot választhat ki a központi telepítéshez. Ez a korlát csak az egyidejű bevezetésre vonatkozik, az Automation-megoldás által kezelhető erőforráscsoportok számára nem.

A bevezetéshez runbookot is használhat, további információt az [Azure Automation fedélzeti frissítési és változáskövetési megoldásai](automation-onboard-solutions.md)című témakörben talál.

A szűrővezérlők segítségével különböző előfizetésekből, helyekről és erőforráscsoportokból választhat virtuális gépeket.

![Beépített frissítés-kezelési megoldás](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Tekintse át a Log Analytics munkaterület és automation-fiók választási lehetőségeit. Alapértelmezés szerint egy meglévő munkaterület és automatizálási fiók van kiválasztva. Ha egy másik Log Analytics-munkaterületet és automatizálási fiókot szeretne használni, kattintson a **CUSTOM** gombra az **Egyéni konfiguráció** lapon való kijelöléshez. Ha a Log Analytics-munkaterületet választja, a rendszer ellenőrzi, hogy az automation-fiókhoz kapcsolódik-e. Ha csatolt automation-fiókot talál, a következő képernyő jelenik meg. Ha elkészült, kattintson **az OK**gombra.

![Munkaterület és fiók kiválasztása](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Ha a kiválasztott munkaterület nincs automation-fiókhoz kapcsolva, a következő képernyő jelenik meg. Jelöljön ki egy automatizálási fiókot, és ha befejeződött, kattintson **az OK** gombra.

![Nincs munkaterület](media/automation-onboard-solutions-from-browse/no-workspace.png)

> [!NOTE]
> A megoldások engedélyezésekor csak bizonyos régiók esetén lehet összekapcsolni egy Log Analytics-munkaterületet és egy Automation-fiókot.
>
> A támogatott leképezési párok listáját az [Automation-fiók és a Log Analytics munkaterület területleképezése című témakörben tájékírja.](how-to/region-mappings.md)

Törölje a jelet a jelölőnégyzetből minden olyan virtuális gép mellett, amelyet nem szeretne engedélyezni. Az engedélyezhető virtuális gépek már nincsenek bejelölve.

A megoldás engedélyezéséhez kattintson az **Engedélyezés** gombra. A megoldás engedélyezése akár 15 percet is igénybe vehet.

## <a name="unlink-workspace"></a>Munkaterület leválasztása

A következő megoldások a Log Analytics-munkaterülettől függenek:

* [Frissítéskezelés](automation-update-management.md)
* [Változások követése](automation-change-tracking.md)
* [Virtuális gépek indítása/leállítása munkaidőn kívül](automation-solution-vm-management.md)

Ha úgy dönt, hogy már nem szeretné integrálni az Automation-fiókot egy Log Analytics-munkaterülettel, leválaszthatja fiókját közvetlenül az Azure Portalról. A folytatás előtt először el kell távolítania a korábban említett megoldásokat, különben ez a folyamat nem folytatódik. Tekintse át a cikket az adott megoldás importált, hogy megértsék a szükséges lépéseket, hogy távolítsa el azt.

Miután eltávolította ezeket a megoldásokat, az automation-fiók leválasztásához hajtsa végre az alábbi lépéseket.

> [!NOTE]
> Egyes megoldások, beleértve az Azure SQL figyelési megoldás korábbi verzióit, előfordulhat, hogy automatizálási eszközöket hoztak létre, és előfordulhat, hogy a munkaterület leválasztása előtt el kell távolítani őket.

1. Az Azure Portalon nyissa meg az Automation-fiókot, és az Automation-fiók lapon válassza a **Csatolt munkaterület** lehetőséget a bal oldali **Kapcsolódó erőforrások** szakaszban.

2. A munkaterület leválasztása lapon kattintson a **Munkaterület leválasztása gombra.**

   ![Munkaterületi lap leválasztása](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   A rendszer felkéri, hogy erősítse meg, valóban folytani kívánja-e.

3. Bár az Azure Automation megpróbálja leválasztani a fiókot a Log Analytics-munkaterületről, nyomon követheti a folyamatot a menü **értesítések** csoportban.

Ha az Update Management megoldást használta, szükség esetén a megoldás eltávolítása után a következő elemeket is eltávolíthatja.

* Ütemezések frissítése – Mindegyiknek olyan neve lesz, amely megfelel a létrehozott frissítési központi telepítéseknek)

* A megoldáshoz létrehozott hibrid munkavégző csoportok – Mindegyik neve a machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8) hasonlóan lesz elnevezve.

Ha a virtuális gépek indítása/leállítása munkaidőn kívüli megoldás során, szükség esetén érdemes lehet eltávolítani a következő elemeket, amelyek már nincs szükség a megoldás eltávolítása után.

* Virtuálisgép-runbook-ütemezések indítása és leállítása
* Virtuálisgép-runbookok indítása és leállítása
* Változók

Azt is megteheti, hogy leválassza a munkaterületet az Automation-fiókról a Log Analytics-munkaterületről. A munkaterületen válassza az **Automatizálási fiók** lehetőséget a **Kapcsolódó erőforrások csoportban.** Az Automatizálási fiók lapon válassza a **Fiók leválasztása**lehetőséget.

## <a name="troubleshooting"></a>Hibaelhárítás

Több gép esetén előfordulhatnak olyan gépek, `Cannot enable`amelyek a . Különböző okok miatt előfordulhat, hogy egyes gépek nincsenek engedélyezve. A következő szakaszok bemutatják `Cannot enable` a virtuális gépen lévő állapot lehetséges okait a fedélzeti műveletek során.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>A virtuális gép egy másik\<munkaterületnek\>jelent: ' workspaceName '.  A konfiguráció módosítása az engedélyezéshez való használathoz

**Ok:** Ez a hiba azt mutatja, hogy a virtuális gép, amelyet megpróbál bedolgozni, egy másik munkaterületre jelentéseket.

**Megoldás:** Kattintson a **Használat konfigurációként** parancsra a megcélzott Automation-fiók és a Log Analytics-munkaterület módosításához.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>Virtuális gép olyan munkaterületnek jelent, amely nem érhető el ebben az előfizetésben

**Ok**: Az a munkaterület, amelynek a virtuális gép jelentést tesz:

* Másik előfizetésben van, vagy
* Már nem létezik, vagy
* Olyan erőforráscsoportban van, amelyhez nincs hozzáférési engedélye

**Megoldás:** Keresse meg a munkaterülethez társított automatizálási fiókot, amelyet a virtuális gép a virtuális gépnek és a virtuális gép fedélzetére jelent a hatókör konfigurációjának módosításával.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>A virtuális gép operációs rendszerének verziója vagy terjesztése nem támogatott

**Ok:** A megoldás nem minden Linux disztribúcióvagy a Windows összes verziója esetén támogatott.

**Megoldás:** Tekintse meg a [megoldás támogatott ügyfelek listáját.](automation-update-management.md#clients)

### <a name="classic-vms-cannot-be-enabled"></a>A klasszikus virtuális gépek nem engedélyezhetők

**Ok:** A klasszikus központi telepítési modellt használó virtuális gépek nem támogatottak.

**Megoldás**: Telepítse át a virtuális gépet az Erőforrás-kezelő telepítési modelljébe. Ennek módjáról a [Klasszikus telepítési modell erőforrásainak áttelepítése](../virtual-machines/windows/migration-classic-resource-manager-overview.md)című témakörben olvashat.

### <a name="vm-is-stopped-deallocated"></a>A virtuális gép leállt. (felszabadított)

**Ok**: A virtuális gép nem **futó** állapotban.

**Megoldás**: Annak érdekében, hogy egy virtuális gép egy megoldás a virtuális gép kell futnia. Kattintson a **virtuális gép vezetékbeli indítása** hivatkozásra a virtuális gép elindításához anélkül, hogy a lapról navigálna.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Virtuális gép eltávolítása az Update Management szolgáltatásból:

* A Log Analytics-munkaterületen távolítsa el a virtuális gép `MicrosoftDefaultScopeConfig-Updates`a mentett keresés a Hatókör konfigurációja. A mentett keresések a munkaterület **Általános** területén találhatók.
* Távolítsa el a [Windows Log Analytics-ügynökét](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) vagy [a Linuxos Log Analytics-ügynököt.](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

Most, hogy a megoldás engedélyezve van a virtuális gépek, keresse fel a frissítéskezelése áttekintést cikket, hogyan hozhat létre **egy frissítési központi telepítés** a gépekhez.

> [!div class="nextstepaction"]
> [Frissítéskezelés – Frissítések és javítások kezelése az Azure-beli virtuális gépekhez](./automation-tutorial-update-management.md)

Kiegészítés útmutatók a megoldásokat, és hogyan kell használni őket:

* [Oktatóanyag – Frissítések kezelése a virtuális géphez](automation-tutorial-update-management.md)

* [Oktatóanyag – Szoftver azonosítása virtuális gépen](automation-tutorial-installed-software.md)

* [Oktatóanyag – A virtuális gépek módosítási hibáinak elhárítása](automation-tutorial-troubleshoot-changes.md)
