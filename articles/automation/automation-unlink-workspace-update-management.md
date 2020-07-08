---
title: Munkaterület leválasztása az Automation-fiókból Update Management
description: Ez a cikk azt ismerteti, hogyan lehet lemondani egy Log Analytics munkaterület összekapcsolását az Automation-fiókból a Update Management
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 9129d10071a4c8da0376cbad3d64c10cbaceb8b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83835784"
---
# <a name="unlink-workspace-from-automation-account-for-update-management"></a>Munkaterület leválasztása az Automation-fiókból Update Management

[Update Management](automation-update-management.md) műveletek során dönthet úgy, hogy nem integrálja az Automation-fiókját egy log Analytics munkaterülettel. Ebből a cikkből megtudhatja, hogy leválasztja a munkaterületet a fiókjából.

1. Jelentkezzen be az Azure-ba https://portal.azure.com .

2. Távolítsa el Update Management a virtuális gépek számára. Lásd: [virtuális gépek eltávolítása Update Managementról](automation-remove-vms-from-update-management.md).

3. Ha Update Management az Azure SQL monitoring korábbi verzióit tartalmazza, akkor előfordulhat, hogy a szolgáltatás telepítője olyan Automation-eszközöket hozott létre, amelyeket el kell távolítania. Update Management esetén előfordulhat, hogy el kívánja távolítani a következő elemeket, amelyekre már nincs szükség:

   * Frissítési ütemtervek – mindegyiknek van olyan neve, amely megfelel a létrehozott frissítési telepítésnek.
   * Update Managementhoz létrehozott hibrid feldolgozói csoportok – mindegyik neve hasonló a machine1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

4. Nyissa meg az Automation-fiókját, és a bal oldali **kapcsolódó erőforrások** területen válassza a **csatolt munkaterület** lehetőséget.

5. A munkaterület leválasztása lapon kattintson a **munkaterület leválasztása** elemre, és válaszoljon a felszólításokra.

   ![Munkaterület leválasztása lap](media/automation-unlink-workspace-update-management/automation-unlink-workspace-blade.png).

6. Míg Azure Automation megpróbálja leválasztani a Log Analytics munkaterületet, nyomon követheti a menü **értesítések** részén látható előrehaladást.

Azt is megteheti, hogy leválasztja az Log Analytics munkaterületet az Automation-fiókjából a munkaterületen belülről:

1. A munkaterületen válassza az **Automation-fiók** lehetőséget a **kapcsolódó erőforrások**területen. 
2. Az Automation-fiók lapon válassza a **fiók megszüntetése**lehetőséget.

## <a name="next-steps"></a>További lépések

* A szolgáltatással való együttműködéshez tekintse meg az Azure-beli [virtuális gépek frissítéseinek és javításának kezelése](automation-tutorial-update-management.md)című témakört.
* A szolgáltatással kapcsolatos hibák elhárításához tekintse meg [Update Management problémák elhárítása](troubleshoot/update-management.md)című témakört.
* A Windows Update Agent hibáinak elhárításához tekintse meg a [Windows Update Agent problémáinak elhárítása](troubleshoot/update-agent-issues.md)című témakört.
* A Linux frissítési ügynök hibáinak elhárításához tekintse meg a [Linux frissítési ügynökkel kapcsolatos problémák elhárítása](troubleshoot/update-agent-issues-linux.md)című témakört.
