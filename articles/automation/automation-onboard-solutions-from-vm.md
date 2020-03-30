---
title: Beépített frissítéskezelés, változáskövetés és készletmegoldások Azure-beli virtuális gépről
description: Ismerje meg, hogyan lehet az Azure-ban virtuális gépeket az Azure Automation részét beálló frissítéskezelési, változáskövetési és készletmegoldásokkal beszállni.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 621b429f5dc3a6b6620e4d41ad46763e1d4fa226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299526"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Beépített frissítéskezelés, változáskövetés és készletmegoldások azure-beli virtuális gépről

Az Azure Automation olyan megoldásokat kínál, amelyek segítenek az operációs rendszer biztonsági frissítéseinek kezeléséhez, a változások nyomon követéséhez és a számítógépekre telepített telepítések leltározásához. A gépek et többféleképpen is belehet rakni. A megoldást virtuális gépről, [az Automation-fiókból,](automation-onboard-solutions-from-automation-account.md) [több gép böngészéséből](automation-onboard-solutions-from-browse.md)vagy runbook használatával is beviheti. [runbook](automation-onboard-solutions.md) Ez a cikk ezeket a megoldásokat egy Azure virtuális gépről történő bevezetéssel ismerteti.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="enable-the-solutions"></a>A megoldások engedélyezése

Először engedélyezze a virtuális gép en lévő megoldások egyikét vagy mindhárom megoldását:

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali ablaktáblában válassza **a Virtuális gépek,** vagy keressen, és válassza a **virtuális gépek** a **kezdőlapon.**
2. Válassza ki azt a virtuális gép, amelyhez engedélyezni szeretné a megoldást.
3. A Virtuálisgép lap **Műveletek**területén válassza a **Frissítéskezelés**, **Készlet**vagy **Változáskövetés**lehetőséget. A virtuális gép létezhet bármely régióban, függetlenül az Automation-fiók helyét. Amikor egy virtuális gépről a dedikál `Microsoft.OperationalInsights/workspaces/read` egy megoldást, engedélyt kell kérnie annak megállapítására, hogy a virtuális gép egy munkaterületre van-e bedolgozva. A szükséges további engedélyekről a [gépekhez szükséges engedélyek](automation-role-based-access-control.md#onboarding)című témakörben olvashat.

Ha meg szeretné tudni, hogyan lehet egyszerre több gépet beszállni, olvassa el [az Alaplapi frissítéskezelés, a változáskövetés és a Készletmegoldások témakört.](automation-onboard-solutions-from-automation-account.md)

Válassza ki az Azure Log Analytics munkaterület és automation-fiók, majd válassza **lehetővé teszi** a megoldás engedélyezéséhez. A megoldás engedélyezése akár 15 percet is igénybe vehet.

![Az Update Management megoldás alaplapja](media/automation-tutorial-update-management/manageupdates-update-enable.png)

Nyissa meg a többi megoldást, és válassza az **Engedélyezés lehetőséget.** A Log Analytics munkaterület és az Automation-fiók legördülő listái le vannak tiltva, mert ezek a megoldások ugyanazt a munkaterületet és automation-fiókot használják, mint a korábban engedélyezett megoldás.

> [!NOTE]
> **A változáskövetés** és a **készlet** ugyanazt a megoldást használja. Ha ezen megoldások egyike engedélyezve van, a másik is engedélyezve van.

## <a name="scope-configuration"></a>Hatókör konfigurációja

Minden megoldás hatókörkonfigurációt használ a munkaterületen a megoldást kapó számítógépek megcélzásához. A hatókör konfigurációja egy vagy több mentett keresés csoportja, amely a megoldás hatókörének adott számítógépekre való korlátozására szolgál. A hatókörkonfigurációk eléréséhez az Automation-fiók **Kapcsolódó erőforrások**területén válassza a **Munkaterület**lehetőséget. A munkaterület Munkaterület **adatforrásai**csoportban válassza a **Hatókörkonfigurációk**lehetőséget.

Ha a kijelölt munkaterület még nem rendelkezik frissítéskezelés vagy változáskövetési megoldásokkal, a következő hatókörkonfigurációk jönnek létre:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-frissítések**

Ha a kiválasztott munkaterület már rendelkezik a megoldással, a megoldás nem lesz újratelepítve, és a hatókör konfigurációja nem kerül hozzáadásra.

Jelölje ki a három pontot (**...**) bármelyik konfigurációban, majd kattintson a **Szerkesztés gombra.** A **Hatókör konfigurációjának szerkesztése** ablaktáblán válassza **a Számítógépcsoportok kijelölése**lehetőséget. A **Számítógépcsoportok** ablaktábla a hatókörkonfiguráció létrehozásához használt mentett kereséseket jeleníti meg.

## <a name="saved-searches"></a>Mentett keresések

Amikor hozzáad egy számítógépet a frissítéskezelés, a változáskövetés vagy a készletmegoldásokhoz, a számítógép hozzáadódik a munkaterület két mentett keresésének egyikéhez. A mentett keresések olyan lekérdezések, amelyek a megoldásokhoz megcélzott számítógépeket tartalmazzák.

Lépjen a munkaterülethez. **Az Általános csoportban**válassza a **Mentett keresések lehetőséget.** Az ezen megoldások által használt két mentett keresés az alábbi táblázatban látható:

|Név     |Kategória  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Változáskövetése       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Frissítések        | Updates__MicrosoftDefaultComputerGroup         |

Jelölje ki a mentett keresések egyikét a csoport feltöltéséhez használt lekérdezés megtekintéséhez. Az alábbi képen a lekérdezés és annak eredményei láthatók:

![Mentett keresések](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Munkaterület leválasztása

A következő megoldások a Log Analytics-munkaterülettől függenek:

* [Frissítéskezelés](automation-update-management.md)
* [Változáskövetés](automation-change-tracking.md)
* [Virtuális gépek indítása/leállítása munkaidőn kívül](automation-solution-vm-management.md)

Ha úgy dönt, hogy már nem szeretné integrálni az Automation-fiókot egy Log Analytics-munkaterülettel, leválaszthatja fiókját közvetlenül az Azure Portalról.  A folytatás előtt először el kell távolítania a korábban említett megoldásokat, különben ez a folyamat nem folytatódik. Tekintse át a cikket az adott megoldás importált, hogy megértsék a szükséges lépéseket, hogy távolítsa el azt.

Miután eltávolította ezeket a megoldásokat, az automation-fiók leválasztásához hajtsa végre az alábbi lépéseket.

> [!NOTE]
> Egyes megoldások, beleértve az Azure SQL figyelési megoldás korábbi verzióit, előfordulhat, hogy automatizálási eszközöket hoztak létre, és előfordulhat, hogy a munkaterület leválasztása előtt el kell távolítani őket.

1. Az Azure Portalon nyissa meg az Automation-fiókot, és az Automation-fiók lapon válassza a **Csatolt munkaterület** lehetőséget a bal oldali **Kapcsolódó erőforrások** szakaszban.

2. A munkaterület leválasztása lapon kattintson a **Munkaterület leválasztása gombra.**

   ![Munkaterületi lap leválasztása](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   A rendszer felkéri, hogy erősítse meg, valóban folytani kívánja-e.

3. Bár az Azure Automation megpróbálja leválasztani a fiókot a Log Analytics-munkaterületről, nyomon követheti a folyamatot a menü **értesítések** csoportban.

Ha az Update Management megoldást használta, szükség esetén a megoldás eltávolítása után a következő elemeket is eltávolíthatja.

* Ütemezések frissítése – Mindegyiknek olyan neve lesz, amely megfelel a létrehozott frissítési központi telepítéseknek.

* A megoldáshoz létrehozott hibrid munkavégző csoportok – Mindegyik neve a machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8) hasonlóan lesz elnevezve.

Ha a virtuális gépek indítása/leállítása munkaidőn kívüli megoldás során, szükség esetén érdemes lehet eltávolítani a következő elemeket, amelyek már nincs szükség a megoldás eltávolítása után.

* Virtuálisgép-runbook-ütemezések indítása és leállítása
* Virtuálisgép-runbookok indítása és leállítása
* Változók

Azt is megteheti, hogy leválassza a munkaterületet az Automation-fiókról a Log Analytics-munkaterületről. A munkaterületen válassza az **Automatizálási fiók** lehetőséget a **Kapcsolódó erőforrások csoportban.** Az Automatizálási fiók lapon válassza a **Fiók leválasztása**lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Virtuális gép eltávolítása az Update Management szolgáltatásból:

* A Log Analytics-munkaterületen távolítsa el a virtuális gép `MicrosoftDefaultScopeConfig-Updates`a mentett keresés a Hatókör konfigurációja. A mentett keresések a munkaterület **Általános** területén találhatók.
* Távolítsa el a [Microsoft Monitoring ügynököt](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) vagy [a Log Analytics ügynököt Linuxra.](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

Folytassa az oktatóanyagokat a megoldásokhoz, hogy megtudja, hogyan kell használni őket:

* [Oktatóanyag – A virtuális gép frissítéseinek kezelése](automation-tutorial-update-management.md)

* [Oktatóanyag – Szoftver azonosítása virtuális gépen](automation-tutorial-installed-software.md)

* [Oktatóanyag – A virtuális gépek módosítási hibáinak elhárítása](automation-tutorial-troubleshoot-changes.md)
