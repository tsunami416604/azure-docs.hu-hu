---
title: Azure Automation-fiók leválasztása a Log Analyticsről
description: Ez a cikk bemutatja, hogyan leválasztása a Log Analytics-munkaterületet az Azure Automation-fiók áttekintése.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1328ce8c306188c32bce5385f58f118a63c08deb
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426533"
---
# <a name="how-to-unlink-your-automation-account-from-a-log-analytics-workspace"></a>Hogyan lehet a Log Analytics-munkaterület Automation-fiók leválasztása

Az Azure Automation integrálható a Log Analyticsben, hogy csak nem támogatja az összes az Automation-fiókok a runbook-feladatok figyelése, de az is szükséges, amikor importálja a következő megoldásokat, amelyek a Log Analytics függenek:

* [Frissítéskezelés](../operations-management-suite/oms-solution-update-management.md)
* [Változáskövetés](../log-analytics/log-analytics-change-tracking.md)
* [Virtuális gépek indítása/leállítása munkaidőn kívül](automation-solution-vm-management.md)

Ha úgy dönt, hogy már nem szeretne az Automation-fiók integrálása a Log Analytics-szel, megszüntetheti a fiók közvetlenül az Azure Portalról.  Mielőtt továbblépne, először el kell távolítania a megoldások azt korábban említettük, ellenkező esetben ez a folyamat megakadályozza a folytatás. Tekintse át a cikk az adott megoldás importált távolítsa el a szükséges lépések megértéséhez.

Miután eltávolítja ezeket a megoldásokat, az Automation-fiók leválasztása a következő lépéseket végezheti.

> [!NOTE]
> Egyes megoldások, beleértve az Azure SQL-figyelési megoldás a korábbi lehet, hogy létre automation-adategységeket, és is szükség lehet a munkaterület leválasztása előtt el kell távolítani.

## <a name="unlink-workspace"></a>Munkaterület leválasztása

1. Az Azure Portalról nyissa meg az Automation-fiókját, és a az Automation-fiók oldalon válassza ki **csatolt munkaterület** a szakaszában **kapcsolódó erőforrások** a bal oldalon.

1. A leválasztás munkaterület oldalán kattintson a **munkaterület leválasztása**.

   ![Munkaterület oldalán leválasztása](media/automation-unlink-from-log-analytics/automation-unlink-workspace-blade.png).

   A rendszer felkéri, hogy erősítse meg, valóban folytani kívánja-e.

1. Bár az Azure Automation próbál meg a fiók leválasztása a Log Analytics-munkaterület, nyomon követheti a folyamat állapotát **értesítések** a menüből.

Az Update Management megoldás használata esetén igény szerint, előfordulhat, hogy el kívánja távolítani a következő elemek, amelyek a megoldás eltávolítása után már nem szükséges.

* Frissítés ütemezése – a neve megegyezik a létrehozott frissítéstelepítések lesz)

* A megoldás - számára létrehozott hibrid feldolgozócsoportok egyes lesznek elnevezve hasonlóan a: gép1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Virtuális gépek indítása/leállítása munkaidőn kívül megoldás használata esetén igény szerint, előfordulhat, hogy el kívánja távolítani a következő elemek, amelyek a megoldás eltávolítása után már nem szükséges.

* Elindíthatja és leállíthatja a virtuális gép runbook ütemezése
* Virtuális gép runbookok elindítása és leállítása
* Változók

## <a name="next-steps"></a>További lépések

Konfigurálja újra az Automation-fiók integrálása a Log Analytics, lásd: [feladat állapotát és a feladatstreamek automatizálást továbbítása a Log Analyticsbe](automation-manage-send-joblogs-log-analytics.md).