---
title: Azure-beli virtuális gépről származó Update Management-, Change Tracking-és leltározási megoldások
description: Ismerje meg, hogyan készíthet Azure-beli virtuális gépeket a Azure Automation részét képező Update Management-, Change Tracking-és leltározási megoldásokkal.
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 0069d2e8ccd3b4f65ced8b6e18ce568689f81e14
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374412"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Update Management-, Change Tracking-és leltári megoldások előkészítése Azure-beli virtuális gépekről

Azure Automation megoldásokat kínál az operációs rendszer biztonsági frissítéseinek kezeléséhez, a változások nyomon követéséhez és a számítógépeken telepített termékek leltározásához. A gépek több módon is bekészíthetők. A megoldást egy virtuális gépről, [az Automation-fiókból](automation-onboard-solutions-from-automation-account.md), több gépről való [böngészésből](automation-onboard-solutions-from-browse.md)vagy [runbook](automation-onboard-solutions.md)használatával is elvégezheti. Ez a cikk a megoldások Azure-beli virtuális gépekről történő előkészítését ismerteti.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="enable-the-solutions"></a>A megoldások engedélyezése

Nyissa meg a meglévő virtuális gépet. A **műveletek**területen válassza az **Update Management**, a **leltár**vagy a **change Tracking**elemet. A virtuális gép bármely régióban létezhet, függetlenül az Automation-fiókjának helyétől. Ha egy virtuális gépről telepít egy megoldást, rendelkeznie kell a `Microsoft.OperationalInsights/workspaces/read` engedéllyel annak megállapításához, hogy a virtuális gép be van-e telepítve a munkaterületre. Az általánosan szükséges további engedélyek megismeréséhez tekintse meg a gépek bevezetéséhez [szükséges engedélyeket](automation-role-based-access-control.md#onboarding).

Ha csak a virtuális gép számára kívánja engedélyezni a megoldást, győződjön meg arról, hogy a **virtuális gép engedélyezése** beállítás ki van választva. Ha több gépet szeretne bevezetni a megoldásba, válassza a virtuális gépek engedélyezése ebben az **előfizetésben**lehetőséget, majd **kattintson az engedélyezés elemre**, és válassza ki a kívánt gépeket. Ha többet szeretne megtudni arról, hogyan lehet egyszerre több gépet bevezetni, tekintse meg a következő témakört: [Update Management, Change Tracking és leltározási megoldások](automation-onboard-solutions-from-automation-account.md).

Válassza ki az Azure Log Analytics-munkaterület és az Automation-fiókot, majd válassza az **Engedélyezés** lehetőséget a megoldás engedélyezéséhez. A megoldás engedélyezése akár 15 percet is igénybe vehet.

![A Update Management-megoldás előkészítése](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Lépjen a többi megoldáshoz, majd válassza az **Engedélyezés**lehetőséget. A Log Analytics munkaterület és az Automation-fiók legördülő listája le van tiltva, mert ezek a megoldások ugyanazt a munkaterületet és Automation-fiókot használják, mint a korábban engedélyezett megoldás.

> [!NOTE]
> A **change Tracking** and **Inventory** ugyanazt a megoldást használja. Ha az egyik megoldás engedélyezve van, a másik is engedélyezve lesz.

## <a name="scope-configuration"></a>Hatókör-konfiguráció

Mindegyik megoldás egy hatókör-konfigurációt használ a munkaterületen a megoldást futtató számítógépek célzásához. A hatókör-konfiguráció egy vagy több mentett keresés csoportja, amely a megoldás hatókörének meghatározott számítógépekre való korlátozására szolgál. A hatókör-konfigurációk eléréséhez az Automation-fiókban a **kapcsolódó erőforrások**területen válassza a **munkaterület**lehetőséget. A munkaterületen, a **munkaterület-ADATforrások**területen válassza a **hatókör-konfigurációk**elemet.

Ha a kiválasztott munkaterület még nem rendelkezik Update Management vagy Change Tracking megoldással, a rendszer a következő hatókör-konfigurációkat hozza létre:

* **MicrosoftDefaultScopeConfig – változáskövetési**

* **MicrosoftDefaultScopeConfig – frissítések**

Ha a kiválasztott munkaterület már rendelkezik a megoldással, a rendszer nem telepíti újra a megoldást, és a hatókör-konfiguráció nincs hozzáadva.

Jelölje ki az ellipsziseket ( **..** .) bármelyik konfiguráción, majd válassza a **Szerkesztés**lehetőséget. A **hatókör-konfiguráció szerkesztése** panelen válassza a **számítógépcsoportok kiválasztása**lehetőséget. A **számítógépcsoportok** ablaktáblán láthatók a hatókör-konfiguráció létrehozásához használt mentett keresések.

## <a name="saved-searches"></a>Mentett keresések

Ha a számítógép bekerül a Update Managementba, Change Tracking vagy leltározási megoldásba, a számítógép hozzá lesz adva a munkaterület két mentett keresésének egyikéhez. A mentett keresések olyan lekérdezések, amelyek tartalmazzák azokat a számítógépeket, amelyek ezekre a megoldásokra vannak rendelve.

Lépjen a munkaterülethez. Az **általános**területen válassza a **mentett keresések**lehetőséget. A megoldások által használt két mentett keresés a következő táblázatban látható:

|Név     |Kategória  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Változáskövetési       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Frissítések        | Updates__MicrosoftDefaultComputerGroup         |

Válassza ki a mentett keresések valamelyikét, és tekintse meg a csoport feltöltéséhez használt lekérdezést. Az alábbi ábrán a lekérdezés és annak eredményei láthatók:

![Mentett keresések](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Munkaterület leválasztása

A következő megoldások Log Analytics munkaterülettől függenek:

* [Frissítéskezelés](automation-update-management.md)
* [Változáskövetés](automation-change-tracking.md)
* [Start/Stop VMs during off-hours](automation-solution-vm-management.md)

Ha úgy dönt, hogy már nem szeretné integrálni az Automation-fiókot egy Log Analytics munkaterülettel, közvetlenül a Azure Portalból is leválaszthatja a fiókját.  Mielőtt továbblépne, először el kell távolítania a korábban említett megoldásokat, ellenkező esetben a folyamat nem fog folytatódni. Tekintse át az importált konkrét megoldásról szóló cikket az eltávolításához szükséges lépések megismeréséhez.

A megoldások eltávolítása után a következő lépések végrehajtásával leválaszthatja az Automation-fiókját.

> [!NOTE]
> Előfordulhat, hogy néhány megoldás, például az Azure SQL-figyelési megoldás korábbi verziói automatizálási eszközöket hoztak létre, és a munkaterület leválasztása előtt is el kell távolítani őket.

1. A Azure Portal nyissa meg az Automation-fiókját, és az Automation-fiók lapon válassza a **csatolt munkaterület** lehetőséget a bal oldalon található **kapcsolódó erőforrások** szakaszban.

2. A munkaterület leválasztása lapon kattintson a **munkaterület leválasztása**elemre.

   ![Munkaterület leválasztása lap](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   A rendszer felkéri, hogy erősítse meg, valóban folytani kívánja-e.

3. Míg Azure Automation megkísérli leválasztani a fiókot a Log Analytics munkaterületen, nyomon követheti a menü **értesítések** részén látható előrehaladást.

Ha a Update Management megoldást használta, érdemes lehet eltávolítani a következő elemeket, amelyekre már nincs szükség a megoldás eltávolítása után.

* Frissítési ütemtervek – minden olyan névvel rendelkezik, amely megfelel a létrehozott frissítési központi telepítésnek.

* A megoldáshoz létrehozott hibrid feldolgozói csoportok – mindegyik neve hasonló lesz a machine1. contoso. com _9ceb8108-26c9-4051-b6b3-227600d715c8).

Ha a Start/Stop VMs during off-hours megoldást használta, érdemes lehet eltávolítani a következő elemeket, amelyekre már nincs szükség a megoldás eltávolítása után.

* VM runbook-ütemtervek elindítása és leállítása
* VM-runbookok elindítása és leállítása
* Változók

Azt is megteheti, hogy kikapcsolja a munkaterületet az Automation-fiókjából a Log Analytics munkaterületről. A munkaterületen válassza az **Automation-fiók** lehetőséget a **kapcsolódó erőforrások**területen. Az Automation-fiók lapon válassza a **fiók megszüntetése**lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Virtuális gép eltávolítása Update Managementról:

* A Log Analytics munkaterületen távolítsa el a virtuális gépet a hatókör-konfiguráció `MicrosoftDefaultScopeConfig-Updates` mentett keresésből. A mentett keresések a munkaterület **általános** területén találhatók.
* Távolítsa el a [Microsoft monitoring agentet](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) vagy a [Linux rendszerhez készült log Analytics-ügynököt](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Következő lépések

Folytassa a megoldásokkal kapcsolatos oktatóanyagokkal, hogy megtudja, hogyan használhatja őket:

* [Oktatóanyag – a virtuális gép frissítéseinek kezelése](automation-tutorial-update-management.md)
* [Oktatóanyag – szoftverek azonosítása virtuális gépen](automation-tutorial-installed-software.md)
* [Oktatóanyag – virtuális gépek változásainak megoldása](automation-tutorial-troubleshoot-changes.md)
