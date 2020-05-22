---
title: Munkaterület leválasztása az Automation-fiókból Update Management
description: Ez a cikk azt ismerteti, hogyan lehet lemondani egy Log Analytics munkaterület összekapcsolását az Automation-fiókból a Update Management
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4261b3c3fa7aab830f5f57e86ee25f8ba5894849
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749066"
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

* [Azure-beli virtuális gépek frissítéseinek és javításának kezelése](automation-tutorial-update-management.md)
* [Virtuális gépek eltávolítása a Update Managementból](automation-remove-vms-from-update-management.md)
* [Update Management problémák elhárítása](troubleshoot/update-management.md)
