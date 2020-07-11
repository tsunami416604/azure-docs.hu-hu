---
title: Azure Automation Change Tracking és leltár engedélyezése a Azure Portal
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a Change Tracking és a leltár funkció a Azure Portal.
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 0d657dc38cd5b4149bcf7d1dbf1ad73afa75a5e6
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186299"
---
# <a name="enable-change-tracking-and-inventory-from-azure-portal"></a>Change Tracking és leltár engedélyezése Azure Portal

Ez a cikk bemutatja, hogyan engedélyezheti a virtuális gépek [change Tracking és leltározási](change-tracking.md) funkcióját a Azure Portal tallózásával. Az Azure-beli virtuális gépek méretének engedélyezéséhez Change Tracking és leltár használatával engedélyeznie kell egy meglévő virtuális gépet. 

A virtuális gépek kezeléséhez használható erőforráscsoportok számát a Resource Manager-alapú üzemelő [példányok](../azure-resource-manager/templates/cross-resource-group-deployment.md)korlátozzák. A Resource Manager-alapú üzemelő példányok, amelyek nem tévesztendő össze a frissítési környezetekkel, üzembe helyezésük legfeljebb öt erőforráscsoport lehet. Ezen erőforráscsoportok közül kettő a Log Analytics munkaterület, az Automation-fiók és a kapcsolódó erőforrások konfigurálására van fenntartva. Ez három erőforráscsoportot hagy el, amelyekkel kiválaszthatja a Change Tracking és a leltár általi felügyeletet. Ez a korlát csak egyidejű telepítésre vonatkozik, nem az Automation szolgáltatással felügyelhető erőforráscsoportok számára.

> [!NOTE]
> A Change Tracking és a leltár engedélyezésekor csak bizonyos régiók támogatottak Log Analytics munkaterület és egy Automation-fiók összekapcsolásához. A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](how-to/region-mappings.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](./index.yml) a gépek kezeléséhez.
* Egy [virtuális gép](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba https://portal.azure.com .

## <a name="enable-change-tracking-and-inventory"></a>A Change Tracking és az Inventory engedélyezése

1. A Azure Portal navigáljon a **virtuális gépekhez**.

2. A jelölőnégyzetekkel kiválaszthatja a Change Tracking és a leltárba felvenni kívánt virtuális gépeket. Egyszerre legfeljebb három különböző erőforráscsoport számára adhat hozzá gépeket. Az Azure-beli virtuális gépek bármelyik régióban létezhetnek, az Automation-fiókja helyétől függetlenül.

    ![Virtuális gépek listája](media/automation-enable-changes-from-browse/vmlist.png)

    > [!TIP]
    > A szűrési vezérlők használatával különböző előfizetések, helyszínek és erőforráscsoportok közül választhat ki virtuális gépeket. A felső jelölőnégyzetre kattintva kiválaszthatja a listában szereplő összes virtuális gépet.

3. Válassza a **change Tracking** vagy a **Inventory** elemet a **konfiguráció kezelése**területen.

4. A virtuális gépek listája úgy van szűrve, hogy csak az azonos előfizetésben és helyen lévő virtuális gépeket jelenítse meg. Ha a virtuális gépek több mint három erőforráscsoporthoz vannak, akkor az első három erőforráscsoport van kiválasztva.

5. Alapértelmezés szerint egy meglévő Log Analytics munkaterület és Automation-fiók van kiválasztva. Ha más Log Analytics munkaterületet és Automation-fiókot szeretne használni, kattintson az **Egyéni** elemre az egyéni konfiguráció lapon való kiválasztásához. Ha Log Analytics munkaterületet választ, ellenőrizze, hogy van-e egy Automation-fiókkal társítva. Ha a rendszer csatolt Automation-fiókot talál, a következő képernyő jelenik meg. Ha elkészült, kattintson **az OK**gombra.

    ![Munkaterület és fiók kiválasztása](media/automation-enable-changes-from-browse/selectworkspaceandaccount.png)

6. Ha a kiválasztott munkaterület nincs Automation-fiókhoz kapcsolva, a következő képernyő jelenik meg. Válasszon ki egy Automation-fiókot, és ha elkészült, kattintson **az OK** gombra.

    ![Nincs munkaterület](media/automation-enable-changes-from-browse/no-workspace.png)

7. Törölje a jelölőnégyzet jelölését azon virtuális gépek mellett, amelyeket nem szeretne engedélyezni. A nem engedélyezhető virtuális gépek már ki vannak választva.

8. Az **Engedélyezés** elemre kattintva engedélyezheti a kiválasztott funkciót. A telepítés elvégzése akár 15 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések

* A szolgáltatással kapcsolatos további részletekért tekintse meg a [change Tracking és a leltár kezelésével](change-tracking-file-contents.md)foglalkozó témakört.
* A szolgáltatással kapcsolatos általános problémák elhárításához tekintse meg a [change Tracking és a leltárral kapcsolatos problémák elhárítása](troubleshoot/change-tracking.md)című témakört.
