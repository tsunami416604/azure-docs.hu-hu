---
title: Megtudhatja, hogyan lehet az Azure Automation frissítéskezelési, változáskövetési és készletkezelési megoldásait bedolgozni
description: Ismerje meg, hogyan lehet az Azure Automation részét használó frissítéskezelési, változáskövetési és készletmegoldásokkal rendelkező Azure-virtuális gépeket beszállni
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: cf82dddf281e8e6f1348884702e32330dee4781b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278674"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Beépített frissítéskezelés, változáskövetés és készletmegoldások

Az Azure Automation megoldásokat kínál az operációs rendszer biztonsági frissítéseinek kezelésére, a változások nyomon követésére és a számítógépekre telepített állapotok leltározására. Számos módja van a gépek fedélzetére, akkor a fedélzeten a megoldás [egy virtuális gép,](automation-onboard-solutions-from-vm.md) [a böngészés több gép,](automation-onboard-solutions-from-browse.md)az Automation-fiók, vagy [a runbook](automation-onboard-solutions.md). Ez a cikk ismerteti ezeket a megoldásokat az Automation-fiókból.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba a https://portal.azure.com címen.

## <a name="enable-solutions"></a>Megoldások engedélyezése

Nyissa meg az Automation-fiókot, és válassza a **Készlet** vagy **a Változáskövetés lehetőséget** a **Konfigurációkezelés csoportban.**

Válassza a Log Analytics munkaterület és automation-fiók, és kattintson az **Engedélyezés** a megoldás engedélyezéséhez. A megoldás engedélyezése akár 15 percet is igénybe vehet.

![Fedélzeti készletmegoldás](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

> [!NOTE]
> A megoldások engedélyezésekor csak bizonyos régiók esetén lehet összekapcsolni egy Log Analytics-munkaterületet és egy Automation-fiókot.
>
> A támogatott leképezési párok listáját az [Automation-fiók és a Log Analytics munkaterület területleképezése című témakörben tájékírja.](how-to/region-mappings.md)

A Change Tracking és az Inventory megoldással [változáskövetési](automation-vm-change-tracking.md) és [leltárkészítési](automation-vm-inventory.md) képességeket biztosíthat a virtuális gépek számára. Ebben a lépésben engedélyezzük a megoldást egy virtuális gépen.

Amikor befejeződött a változáskövetési és készletmegoldás-bevezetési értesítés, válassza a Frissítéskezelés csoport **frissítéskezelés választógombot.** **Update management**

Az Update Management megoldás lehetővé teszi az Azure és a hibrid virtuális gépek frissítéseinek és javításainak kezelését. Felmérheti az elérhető frissítések állapotát, ütemezheti a szükséges frissítések telepítését, és áttekintheti a központi telepítés eredményeit, hogy ellenőrizze, hogy a frissítések sikeresen alkalmazva voltak-e.

A megoldás engedélyezése lapon a log analytics munkaterület kiválasztott ugyanaz a munkaterület, amelyet az előző lépésben használt. Kattintson az **Engedélyezés** gombra a Frissítéskezelés megoldás alaplapjának hozásához. A megoldás engedélyezése akár 15 percet is igénybe vehet.

![Beépített frissítési megoldás](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Hatókör konfigurációja

Minden megoldás hatókörkonfigurációt használ a munkaterületen belül a megoldást kapó számítógépek megcélzásához. A Hatókör konfigurációja egy vagy több mentett keresés csoportja, amely a megoldás hatókörének adott számítógépekre való korlátozására szolgál. A Hatókör-konfigurációk eléréséhez az Automation-fiók **Kapcsolódó erőforrások**csoportban válassza **a Munkaterület**lehetőséget. Ezután a **Munkaterület adatforrásai**csoportban válassza a **Hatókörkonfigurációk**lehetőséget.

Ha a kijelölt munkaterület még nem rendelkezik frissítéskezelés vagy változáskövetési megoldásokkal, a következő hatókörkonfigurációk jönnek létre:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-frissítések**

Ha a kiválasztott munkaterület már rendelkezik a megoldással, a megoldás nem lesz újratelepítve, és a hatókör konfigurációja nem kerül hozzáadásra.

## <a name="saved-searches"></a>Mentett keresések

Amikor hozzáad egy számítógépet a frissítéskezeléshez vagy a változáskövetés és leltározási megoldásokhoz, azok a munkaterület két mentett keresésének egyikéhez kerülnek. Ezek a mentett keresések olyan lekérdezések, amelyek a megoldásokhoz megcélzott számítógépeket tartalmazzák.

Nyissa meg a Log Analytics-munkaterületet, és válassza a **Mentett keresések** lehetőséget az **Általános**területen. Az ezen megoldások által használt két mentett keresés az alábbi táblázatban látható:

|Név     |Kategória  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Változáskövetése       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Frissítések        | Updates__MicrosoftDefaultComputerGroup         |

Jelölje be bármelyik mentett keresés lehetőséget a csoport feltöltéséhez használt lekérdezés megtekintéséhez. Az alábbi képen a lekérdezés és annak eredményei láthatók:

![Mentett keresések](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Beépített Azure-beli virtuális gépek

Az Automation-fiókban válassza a **Készlet** vagy **a Változáskövetés lehetőséget** a **Konfigurációkezelés**csoportban, vagy **a Frissítéskezelés csoportban** a **Frissítéskezelés**lehetőséget.

Kattintson **a + Add Azure virtuális gépek**, válasszon ki egy vagy több virtuális gépet a listából. Az engedélyezhető virtuális gépek szürkén jelennek meg, és nem választhatók ki. Az Azure virtuális gépek létezhetnek bármely régióban, függetlenül az Automation-fiók helyétől. A **Frissítéskezelés engedélyezése** lapon kattintson az **Engedélyezés gombra.** Ez a művelet hozzáadja a kijelölt virtuális gépeket a megoldás mentett számítógépcsoporthoz.

![Az Azure virtuális gépei engedélyezése](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Nem Azure-beli gép előkészítése

Az Azure-ban nem található gépeket manuálisan kell hozzáadni. Az Automation-fiókban válassza a **Készlet** vagy **a Változáskövetés lehetőséget** a **Konfigurációkezelés**csoportban, vagy **a Frissítéskezelés csoportban** a **Frissítéskezelés**lehetőséget.

Kattintson **a Nem Azure-gép hozzáadása gombra.** Ez a művelet megnyit egy új böngészőablakot a [Microsoft Monitoring Agent telepítésére és konfigurálására vonatkozó utasításokkal a számítógépen,](../azure-monitor/platform/log-analytics-agent.md) hogy a gép megkezdhesse a megoldásnak való jelentéstételt. Ha olyan gépet ad ki, amelyet jelenleg a System Center Operations Manager kezel, nem szükséges új ügynök, a munkaterületi adatok bekerülnek a meglévő ügynökbe.

## <a name="onboard-machines-in-the-workspace"></a>Fedélzeti gépek a munkaterületen

A megoldás engedélyezéséhez a manuálisan telepített gépeket vagy a munkaterületre már jelentést tevő gépeket hozzá kell adni az Azure Automationhez. Az Automation-fiókban válassza a **Készlet** vagy **a Változáskövetés lehetőséget** a **Konfigurációkezelés**csoportban, vagy **a Frissítéskezelés csoportban** a **Frissítéskezelés**lehetőséget.

Válassza **a Gépek kezelése**lehetőséget. Ez a művelet megnyitja a **Gépek kezelése** lapot. Ez az oldal lehetővé teszi, hogy a megoldás egy kiválasztott készlet gépek, az összes rendelkezésre álló gépek, vagy lehetővé teszi a megoldást az összes aktuális gépek, és lehetővé teszi az összes jövőbeli gépeken. A **Gépek kezelése** gomb szürkén jelenhet meg, ha korábban az Engedélyezés lehetőséget választotta az összes elérhető és jövőbeli **gépen.**

![Mentett keresések](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Minden rendelkezésre álló gép

Ha engedélyezni szeretné a megoldást az összes elérhető géphez, válassza **az Engedélyezés az összes elérhető gépen**lehetőséget. Ez a művelet letiltja a vezérlőt a gépek egyenkénti hozzáadásához. Ez a feladat hozzáadja a munkaterületre jelentést tevő gépek összes nevét a számítógépcsoport által mentett keresési lekérdezéshez. Ha be van jelölve, ez a művelet letiltja a **Gépek kezelése** gombot.

### <a name="all-available-and-future-machines"></a>Minden rendelkezésre álló és jövőbeli gép

Ha engedélyezni szeretné a megoldást az összes elérhető és jövőbeli géphez, válassza **az Engedélyezés lehetőséget az összes elérhető és jövőbeli gépen.** Ez a beállítás törli a mentett kereséseket és a Hatókör-konfigurációkat a munkaterületről. Ez a művelet megnyitja a megoldást az összes Azure-beli és nem Azure-beli gépszámára, amelyek a munkaterületre jelentést tesznek. Ha be van jelölve, ez a művelet véglegesen letiltja a **Gépek kezelése** gombot, mivel nem maradt hatókörkonfiguráció.

A hatókörkonfigurációkat a kezdeti mentett keresések visszaadásával újra hozzáadhatja. További információt a [Mentett keresések című témakörben talál.](#saved-searches)

### <a name="selected-machines"></a>Kiválasztott gépek

Ha engedélyezni szeretné a megoldást egy vagy több géphez, válassza **az Engedélyezés a kiválasztott gépeken** lehetőséget, és kattintson a **Hozzáadás** gombra a megoldáshoz hozzáadni kívánt gépek mellett. Ez a feladat hozzáadja a kijelölt számítógépneveket a megoldás számítógépcsoport által mentett keresési lekérdezéséhez.

## <a name="unlink-workspace"></a>Munkaterület leválasztása

A következő megoldások a Log Analytics-munkaterülettől függenek:

* [Frissítéskezelés](automation-update-management.md)
* [Változáskövetés](automation-change-tracking.md)
* [Virtuális gépek indítása/leállítása munkaidőn kívül](automation-solution-vm-management.md)

Ha úgy dönt, hogy már nem szeretné integrálni az Automation-fiókot egy Log Analytics-munkaterülettel, leválaszthatja fiókját közvetlenül az Azure Portalról.  A folytatás előtt először el kell távolítania a korábban említett megoldásokat, különben ez a folyamat nem folytatódik. Tekintse át a cikket az adott megoldás, amelyet importált, hogy megértsék a szükséges lépéseket, hogy távolítsa el.

Miután eltávolította ezeket a megoldásokat, az automation-fiók leválasztásához az alábbi lépéseket hajthatja végre.

> [!NOTE]
> Egyes megoldások, beleértve az Azure SQL figyelési megoldás korábbi verzióit, előfordulhat, hogy automatizálási eszközöket hoztak létre, és előfordulhat, hogy a munkaterület leválasztása előtt el kell távolítani őket.

1. Az Azure Portalon nyissa meg az Automation-fiókot, és az Automation-fiók lapon válassza a **Csatolt munkaterület** lehetőséget a bal oldalon **a Kapcsolódó erőforrások** feliratú szakaszban.

2. A munkaterület leválasztása lapon kattintson a **Munkaterület leválasztása gombra.**

   ![Munkaterületi lap leválasztása](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   A folytatást kérő gyors üzenet jelenik meg.

3. Miközben az Azure Automation megpróbálja leválasztani a fiókot a Log Analytics-munkaterületről, nyomon követheti a folyamatot a menü **értesítések** csoportban.

Ha az Update Management megoldást használta, szükség esetén a megoldás eltávolítása után a következő elemeket is eltávolíthatja.

* Ütemezések frissítése – Mindegyiknek olyan neve lesz, amely megfelel a létrehozott frissítési központi telepítéseknek.

* A megoldáshoz létrehozott hibrid munkavégző csoportok – Mindegyik neve a machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8) hasonlóan lesz elnevezve.

Ha a virtuális gépek indítása és leállítása munkaidőn kívüli megoldás során, szükség esetén érdemes eltávolítani a következő elemeket, amelyek már nincs szükség a megoldás eltávolítása után.

* Virtuálisgép-runbook-ütemezések indítása és leállítása
* Virtuálisgép-runbookok indítása és leállítása
* Változók

Azt is megteheti, hogy leválassza a munkaterületet az Automation-fiókról a Log Analytics-munkaterületről. A munkaterületen válassza az **Automatizálási fiók** lehetőséget a **Kapcsolódó erőforrások csoportban.** Az Automatizálási fiók lapon válassza a **Fiók leválasztása**lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Virtuális gép eltávolítása az Update Management szolgáltatásból:

* A Log Analytics-munkaterületen távolítsa el a virtuális gép `MicrosoftDefaultScopeConfig-Updates`a mentett keresés a Hatókör konfigurációja. A mentett keresések a munkaterület **Általános** területén találhatók.
* Távolítsa el a [Microsoft Monitoring ügynököt](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) vagy [a Log Analytics ügynököt Linuxra.](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

Folytassa az oktatóanyagokat a megoldásokról, hogy megtudja, hogyan kell használni őket.

* [Oktatóanyag – Frissítések kezelése a virtuális géphez](automation-tutorial-update-management.md)

* [Oktatóanyag – Szoftver azonosítása virtuális gépen](automation-tutorial-installed-software.md)

* [Oktatóanyag – A virtuális gépek módosítási hibáinak elhárítása](automation-tutorial-troubleshoot-changes.md)
