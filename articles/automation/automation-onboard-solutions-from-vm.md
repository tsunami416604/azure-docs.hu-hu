---
title: Azure-beli virtuális gépről származó Update Management-, Change Tracking-és leltározási megoldások
description: Ismerje meg, hogyan készíthet Azure-beli virtuális gépeket a Azure Automation részét képező Update Management-, Change Tracking-és leltározási megoldásokkal.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: f33f829b6cb86cb01c848e5fc48e1618a3e00a2c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537032"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Update Management-, Change Tracking-és leltári megoldások előkészítése Azure-beli virtuális gépekről

Azure Automation megoldásokat kínál az operációs rendszer biztonsági frissítéseinek kezeléséhez, a változások nyomon követéséhez és a számítógépeken telepített termékek leltározásához. A gépek több módon is bekészíthetők. A megoldást egy virtuális gépről, [az Automation-fiókból](automation-onboard-solutions-from-automation-account.md), több gépről való [böngészésből](automation-onboard-solutions-from-browse.md)vagy [runbook](automation-onboard-solutions.md)használatával is elvégezheti. Ez a cikk a megoldások Azure-beli virtuális gépekről történő előkészítését ismerteti.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="enable-the-solutions"></a>A megoldások engedélyezése

Először engedélyezzen egy vagy mindhárom megoldást a virtuális gépen:

1. A [Azure Portal](https://portal.azure.com)válassza a **virtuális gépek** lehetőséget, vagy keresse meg és válassza ki a **virtuális gépek** lehetőséget a kezdőlapon.
2. Válassza ki azt a virtuális gépet, amelyhez engedélyezni kívánja a megoldást.
3. A virtuális gép lap **műveletek**területén válassza a **kezelés**, **leltár**vagy **változások követése**lehetőséget. A virtuális gép bármely régióban létezhet, függetlenül az Automation-fiókjának helyétől. Amikor egy virtuális gépről telepít egy megoldást, rendelkeznie kell az `Microsoft.OperationalInsights/workspaces/read` engedéllyel annak megállapításához, hogy a virtuális gép be van-e telepítve egy munkaterületre. A szükséges további engedélyekkel kapcsolatos további információkért lásd: [a gépek](automation-role-based-access-control.md#onboarding-permissions)bevezetéséhez szükséges engedélyek. Ha többet szeretne megtudni arról, hogyan lehet egyszerre több gépet bevezetni, tekintse meg a következő témakört: [Update Management, Change Tracking és leltározási megoldások](automation-onboard-solutions-from-automation-account.md).

4. Válassza ki az Azure Log Analytics-munkaterület és az Automation-fiókot, majd kattintson az **Engedélyezés** gombra a megoldás engedélyezéséhez. A megoldás engedélyezése akár 15 percet is igénybe vehet.

![A Update Management-megoldás előkészítése](media/automation-tutorial-update-management/manageupdates-update-enable.png)

5. Lépjen a többi megoldáshoz, majd válassza az **Engedélyezés**lehetőséget. A Log Analytics munkaterület és az Automation-fiók legördülő listája le van tiltva, mert ezek a megoldások ugyanazt a munkaterületet és Automation-fiókot használják, mint a korábban engedélyezett megoldás.

> [!NOTE]
> A Change Tracking and Inventory ugyanazt a megoldást használja. Ha az egyik megoldás engedélyezve van, a másik is engedélyezve lesz.

## <a name="scope-configuration"></a>Hatókör-konfiguráció

Mindegyik megoldás egy hatókör-konfigurációt használ a munkaterületen a megoldást futtató számítógépek célzásához. A hatókör-konfiguráció egy vagy több mentett keresés csoportja, amely a megoldás hatókörének meghatározott számítógépekre való korlátozására szolgál. A hatókör-konfigurációk elérése:

1. Az Automation-fiókban a **kapcsolódó erőforrások**területen válassza a **munkaterület**lehetőséget. 
2. A munkaterületen, a **munkaterület-adatforrások**területen válassza a **hatókör-konfigurációk**elemet.
3. Ha a kiválasztott munkaterület még nem rendelkezik Update Management vagy Change Tracking megoldással, a rendszer a következő hatókör-konfigurációkat hozza létre:

    * `MicrosoftDefaultScopeConfig-ChangeTracking`
    * `MicrosoftDefaultScopeConfig-Updates`

    Ha a kiválasztott munkaterület már rendelkezik a megoldással, a rendszer nem telepíti újra a megoldást, és a hatókör-konfiguráció nincs hozzáadva.

4. Jelölje ki az ellipsziseket (**..**.) bármelyik konfiguráción, majd kattintson a **Szerkesztés**gombra. 
5. A **hatókör-konfiguráció szerkesztése** panelen válassza a **számítógépcsoportok kiválasztása**lehetőséget. A **számítógépcsoportok** ablaktáblán láthatók a hatókör-konfiguráció létrehozásához használt mentett keresések.

## <a name="saved-searches"></a>Mentett keresések

Ha egy számítógépet hozzáadnak a Update Managementhoz, Change Trackinghoz vagy leltározási megoldáshoz, a rendszer hozzáadja a számítógépet a munkaterület két mentett keresésének egyikéhez. A mentett keresések olyan lekérdezések, amelyek tartalmazzák azokat a számítógépeket, amelyek ezekre a megoldásokra vannak rendelve.

Lépjen a munkaterülethez. Az **általános**területen válassza a **mentett keresések**lehetőséget. A megoldások által használt két mentett keresés a következő táblázatban látható:

|Name (Név)     |Kategória  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Változáskövetési       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Frissítések        | Updates__MicrosoftDefaultComputerGroup         |

Válassza ki a mentett keresések valamelyikét, és tekintse meg a csoport feltöltéséhez használt lekérdezést. Az alábbi ábrán a lekérdezés és annak eredményei láthatók:

![Mentett keresések](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Munkaterület leválasztása

A következő megoldások Log Analytics munkaterülettől függenek:

* [Frissítéskezelés](automation-update-management.md)
* [Változások követése](automation-change-tracking.md)
* [Virtuális gépek indítása és leállítása munkaidőn kívül](automation-solution-vm-management.md)

Ha úgy dönt, hogy már nem szeretné integrálni az Automation-fiókot egy Log Analytics munkaterülettel, közvetlenül a Azure Portalból is leválaszthatja a fiókját.  Mielőtt továbblépne, először el kell távolítania a korábban említett megoldásokat, ellenkező esetben a folyamat nem fog folytatódni. Tekintse át az importált konkrét megoldásról szóló cikket az eltávolításához szükséges lépések megismeréséhez.

A megoldások eltávolítása után a következő lépések végrehajtásával leválaszthatja az Automation-fiókját.

> [!NOTE]
> Előfordulhat, hogy néhány megoldás, például az Azure SQL-figyelési megoldás korábbi verziói automatizálási eszközöket hoztak létre, és a munkaterület leválasztása előtt is el kell távolítani őket.

1. A Azure Portalban nyissa meg az Automation-fiókját, és válassza a **csatolt munkaterület** elemet a bal oldalon található **kapcsolódó erőforrások** szakaszban.

2. A munkaterület leválasztása lapon kattintson a **munkaterület leválasztása**elemre.

   ![Munkaterület leválasztása lap](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   A rendszer felkéri, hogy erősítse meg, valóban folytani kívánja-e.

3. Míg Azure Automation megkísérli leválasztani a fiókot a Log Analytics munkaterületen, nyomon követheti a menü **értesítések** részén látható előrehaladást.

Ha a Update Management megoldást használta, érdemes lehet eltávolítani a következő elemeket, amelyekre már nincs szükség a megoldás eltávolítása után.

* Frissítési ütemtervek – minden olyan névvel rendelkezik, amely megfelel a létrehozott frissítési példányoknak.

* A megoldáshoz létrehozott hibrid feldolgozói csoportok – mindegyik neve hasonló lesz a machine1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Ha a Start/Stop VMs during off-hours megoldást használta, érdemes lehet eltávolítani a következő elemeket, amelyekre már nincs szükség a megoldás eltávolítása után.

* VM runbook-ütemtervek elindítása és leállítása
* VM-runbookok elindítása és leállítása
* Változók

Azt is megteheti, hogy kikapcsolja a munkaterületet az Automation-fiókjából a Log Analytics munkaterületről. A munkaterületen válassza az **Automation-fiók** lehetőséget a **kapcsolódó erőforrások**területen. Az Automation-fiók lapon válassza a **fiók megszüntetése**lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Virtuális gép eltávolítása Update Managementról:

* A Log Analytics munkaterületen távolítsa el a virtuális gépet a hatókör-konfiguráció `MicrosoftDefaultScopeConfig-Updates`mentett keresésével. A mentett keresések a munkaterület **általános** területén találhatók.
* Távolítsa el a [Windows log Analytics Agent ügynököt](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) vagy a Linux rendszerhez készült [log Analytics-ügynököt](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>További lépések

Folytassa a megoldásokkal kapcsolatos oktatóanyagokkal, hogy megtudja, hogyan használhatja őket:

* [Oktatóanyag – a virtuális gép frissítéseinek kezelése](automation-tutorial-update-management.md)

* [Oktatóanyag – szoftverek azonosítása virtuális gépen](automation-tutorial-installed-software.md)

* [Oktatóanyag – virtuális gépek változásainak megoldása](automation-tutorial-troubleshoot-changes.md)
