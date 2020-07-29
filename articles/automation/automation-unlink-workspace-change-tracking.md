---
title: Munkaterület leválasztása az Automation-fiókból a Change Tracking és a leltárhoz
description: Ez a cikk azt ismerteti, hogyan lehet lemondani egy Log Analytics munkaterület összekapcsolását az Automation-fiókból a Change Tracking és a leltárhoz
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 2be702ec6e820fe71dd8d2da7aa4cf831b52402e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83828253"
---
# <a name="unlink-workspace-from-automation-account"></a>Munkaterület leválasztása Automation-fiókról

Dönthet úgy, hogy nem integrálja az Automation-fiókját egy Log Analytics munkaterülettel [change Tracking és leltározási](change-tracking.md) műveletek engedélyezésekor. Ebből a cikkből megtudhatja, hogy leválasztja a munkaterületet a fiókjából.

1. Jelentkezzen be az Azure-ba https://portal.azure.com .

2. Távolítsa el Update Management a virtuális gépek számára. Lásd: [virtuális gépek eltávolítása Change Tracking és leltárból](automation-remove-vms-from-change-tracking.md).

3. Ha a Change Tracking és a leltár az Azure SQL monitoring korábbi verzióit tartalmazza, lehetséges, hogy a szolgáltatás telepítője olyan Automation-eszközöket hozott létre, amelyeket el kell távolítania. Keresse meg ezeket az eszközöket, és távolítsa el őket.

4. Nyissa meg az Automation-fiókját, és a bal oldali **kapcsolódó erőforrások** területen válassza a **csatolt munkaterület** lehetőséget.

5. A munkaterület leválasztása lapon kattintson a **munkaterület leválasztása** elemre, és válaszoljon a felszólításokra.

   ![Munkaterület leválasztása lap](media/automation-unlink-workspace-change-tracking/automation-unlink-workspace-blade.png).

6. Míg Azure Automation megpróbálja leválasztani a Log Analytics munkaterületet, nyomon követheti a menü **értesítések** részén látható előrehaladást.

Azt is megteheti, hogy leválasztja az Log Analytics munkaterületet az Automation-fiókjából a munkaterületen belülről:

1. A munkaterületen válassza az **Automation-fiók** lehetőséget a **kapcsolódó erőforrások**területen. 
2. Az Automation-fiók lapon válassza a **fiók megszüntetése**lehetőséget.

## <a name="next-steps"></a>További lépések

* A Change Tracking és a leltár kezelésével kapcsolatban lásd: a [change Tracking és a leltár kezelése](change-tracking-file-contents.md).
* Az általános szolgáltatással kapcsolatos problémák elhárításához lásd: [change Tracking és leltározási problémák elhárítása](troubleshoot/change-tracking.md).
