---
title: Azure Automation-fiók leválasztása a Log Analyticsről
description: Ez a cikk áttekintést az Azure Automation-fiók a Naplóelemzési munkaterület választható módjáról.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 2e6f887a7684ae995e9b7da84dace523843ff555
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-unlink-your-automation-account-from-a-log-analytics-workspace"></a>Az Automation-fiók a Naplóelemzési munkaterület választható hogyan

Azure Automation szolgáltatásbeli integrálható, nem csak a runbook-feladatok az Automation-fiók összes figyelési támogatásához Naplóelemzési, de is szükség a Naplóelemzési függő következő megoldások importálásakor:

* [Frissítéskezelés](../operations-management-suite/oms-solution-update-management.md)
* [Változáskövetés](../log-analytics/log-analytics-change-tracking.md)
* [Indítása/leállítása virtuális gépek munkaidőn kívüli során](automation-solution-vm-management.md)
 
Ha úgy dönt, hogy már nem szeretne az Automation-fiók integrálása Naplóelemzési, megszüntetheti a fiók közvetlenül az Azure portálról.  Mielőtt továbblépne, először távolítsa el a korábban említett megoldások, ellenkező esetben ez a folyamat nem lesz lehetséges a folytatás.  Tekintse át a témakör az adott megoldás importálását megérteni, hogy eltávolítsa a szükséges lépéseket.  

Ezek a megoldások eltávolítása után az Automation-fiók leválasztása a következő lépésekkel végezheti el.

## <a name="unlink-workspace"></a>Munkaterület választható

1. Azure-portálról, nyissa meg az Automation-fiók, és a lapon válassza ki az automatizálási fiókot **munkaterület választható** szakaszban **kapcsolódó erőforrások** a bal oldalon.<br><br> ![Munkaterület beállítás leválasztása](media/automation-unlink-from-log-analytics/automation-unlink-workspace-option.png)<br><br>  
2. A szétkapcsolás munkaterület lapján kattintson a **munkaterület választható**.<br><br> ![Lap munkaterület választható](media/automation-unlink-from-log-analytics/automation-unlink-workspace-blade.png).<br><br>  A rendszer felkéri, hogy erősítse meg, valóban folytani kívánja-e.<br><br>
3. Azure Automation kísérli meg a fiók a Naplóelemzési munkaterület választható, amíg a folyamat állapotát követheti **értesítések** a menüből.

Ha a frissítés felügyeleti megoldás, opcionálisan előfordulhat, hogy szeretné eltávolítani a következő elemek, a megoldás eltávolítása után már nem szükséges.

* Ütemezés frissítésére.  Minden egyes lesz melyek nevei egyeznek a létrehozott központi telepítések)

* Hibrid dolgozó csoportok létrehozása a megoldáshoz.  Minden egyes lesznek elnevezve hasonlóan a machine1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Munkaidőn kívüli megoldás során használt virtuális gépek indítása/leállítása, ha szükség lehet, hogy szeretné eltávolítani a következő elemek, a megoldás eltávolítása után már nem szükséges.

* Elindítása és leállítása a virtuális gép runbook ütemezése 
* Virtuális gép runbookok elindítása és leállítása
* Változók   

## <a name="next-steps"></a>További lépések

Konfigurálja újra az Automation-fiók OMS Naplóelemzési integrálása, lásd: [továbbítása feladat állapotát és a feladat adatfolyamok Automation való Naplóelemzés (OMS)](automation-manage-send-joblogs-log-analytics.md). 