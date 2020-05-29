---
title: Azure Automation Update Management engedélyezése a Azure Portal
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a Update Management a Azure Portal.
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: d0df98e76e8f0575f14794a53c6987e12a4d05d6
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170698"
---
# <a name="enable-update-management-from-the-azure-portal"></a>Az Update Management engedélyezése az Azure Portalról

Ez a cikk azt ismerteti, hogyan engedélyezheti a virtuális gépek [Update Management](automation-update-management.md) funkcióját a Azure Portal tallózásával. Az Azure-beli virtuális gépek méretének engedélyezéséhez Update Management használatával engedélyeznie kell egy meglévő virtuális gépet. 

A virtuális gépek kezeléséhez használható erőforráscsoportok számát a Resource Manager-alapú üzemelő [példányok](../azure-resource-manager/templates/cross-resource-group-deployment.md)korlátozzák. A Resource Manager-alapú üzemelő példányok, amelyek nem tévesztendő össze a frissítési környezetekkel, üzembe helyezésük legfeljebb öt erőforráscsoport lehet. Ezen erőforráscsoportok közül kettő a Log Analytics munkaterület, az Automation-fiók és a kapcsolódó erőforrások konfigurálására van fenntartva. Ez három erőforráscsoportot hagy el, amelyekkel kiválaszthatja a Update Management általi felügyeletet. Ez a korlát csak egyidejű telepítésre vonatkozik, nem az Automation szolgáltatással felügyelhető erőforráscsoportok számára.

> [!NOTE]
> A Update Management engedélyezésekor csak bizonyos régiók támogatottak Log Analytics munkaterület és egy Automation-fiók összekapcsolásához. A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](how-to/region-mappings.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](automation-offering-get-started.md) a gépek kezeléséhez.
* Egy [virtuális gép](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba https://portal.azure.com .

## <a name="enable-update-management"></a>Az Update Management engedélyezése

1. A Azure Portal navigáljon a **virtuális gépekhez**.

2. A jelölőnégyzetek használatával válassza ki a Update Management hozzáadni kívánt virtuális gépeket. Egyszerre legfeljebb három különböző erőforráscsoport számára adhat hozzá gépeket. Az Azure-beli virtuális gépek bármelyik régióban létezhetnek, az Automation-fiókja helyétől függetlenül.

    ![Virtuális gépek listája](media/automation-onboard-solutions-from-browse/vmlist.png)

    > [!TIP]
    > A szűrési vezérlők használatával különböző előfizetések, helyszínek és erőforráscsoportok közül választhat ki virtuális gépeket. A felső jelölőnégyzetre kattintva kiválaszthatja a listában szereplő összes virtuális gépet.

    ![Az Update Management engedélyezése](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

3. Kattintson a **szolgáltatások** lehetőségre, és válassza a **Update Management** lehetőséget a Update Management funkcióhoz. 

4. A virtuális gépek listája úgy van szűrve, hogy csak az azonos előfizetésben és helyen lévő virtuális gépeket jelenítse meg. Ha a virtuális gépek több mint három erőforráscsoporthoz vannak, akkor az első három erőforráscsoport van kiválasztva.

5. Alapértelmezés szerint egy meglévő Log Analytics munkaterület és Automation-fiók van kiválasztva. Ha más Log Analytics munkaterületet és Automation-fiókot szeretne használni, kattintson az **Egyéni** elemre az egyéni konfiguráció lapon való kiválasztásához. Ha Log Analytics munkaterületet választ, ellenőrizze, hogy van-e egy Automation-fiókkal társítva. Ha a rendszer csatolt Automation-fiókot talál, a következő képernyő jelenik meg. Ha elkészült, kattintson **az OK**gombra.

    ![Munkaterület és fiók kiválasztása](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

6. Ha a kiválasztott munkaterület nincs Automation-fiókhoz kapcsolva, a következő képernyő jelenik meg. Válasszon ki egy Automation-fiókot, és ha elkészült, kattintson **az OK** gombra.

    ![Nincs munkaterület](media/automation-onboard-solutions-from-browse/no-workspace.png)

7. Törölje a jelölőnégyzet jelölését azon virtuális gépek mellett, amelyeket nem szeretne engedélyezni. A nem engedélyezhető virtuális gépek már ki vannak választva.

8. Az **Engedélyezés** elemre kattintva engedélyezheti a kiválasztott funkciót. A telepítés elvégzése akár 15 percet is igénybe vehet.

## <a name="next-steps"></a>Következő lépések

* Az Update Management virtuális gépekhez való használatához lásd: [Az Azure-beli virtuális gépek frissítéseinek és javításának kezelése](automation-tutorial-update-management.md).
* Az általános Update Management hibák elhárításával kapcsolatban lásd: [Update Management problémák elhárítása](troubleshoot/update-management.md).
* A Windows Update agenttel kapcsolatos problémák elhárításához tekintse meg a [Windows Update Agent problémáinak elhárítása](troubleshoot/update-agent-issues.md)című témakört.
* A Linux Update agenttel kapcsolatos problémák elhárításához tekintse meg a[Linux frissítési ügynökkel kapcsolatos problémák elhárítása](troubleshoot/update-agent-issues-linux.md)című témakört.
