---
title: Megtudhatja, hogyan több virtuális gép az Azure Automationben előkészítésére frissítéskezelés, a változások követése, és a készlet megoldások
description: Ismerje meg, hogyan érheti egy Azure virtuális gép Azure Automation részét képező frissítéskezelés, a változások követése, és a készlet megoldásokkal
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 6d0109b9043b48fbbbeeaccea6c798eaa547a056
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839867"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Engedélyezze a frissítéskezelés, a változások követése és a készlet megoldások a több virtuális gépeken

Azure Automation-megoldások kezelése az operációs rendszer biztonsági frissítések, követni a változásokat, és készítsen leltárt a számítógépekre telepített biztosít. Többféleképpen is előkészítésére gépekre, akkor discoveryt a megoldás [egy virtuális gép](automation-onboard-solutions-from-vm.md), az a [Automation-fiók](automation-onboard-solutions-from-automation-account.md), virtuális gépek böngészésekor vagy által [runbook](automation-onboard-solutions.md). Ez a cikk foglalkozik bevezetési ezek a megoldások Azure virtuális gépek böngészésekor.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-ba a https://portal.azure.com címen

## <a name="enable-solutions"></a>Megoldások engedélyezése

Az Azure-portálon lépjen a **virtuális gépek**.

A négyzet jelölését, jelölje ki a virtuális gépeket szeretné készíteni a változások követése és a leltárral és a frissítéskezelés. Bevezetési érhető legfeljebb három különböző erőforráscsoportokra vonatkozó egyszerre.

![Virtuális gépek listája](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> A szűrő vezérlők segítségével módosíthatja a virtuális gépek listáját, és kattintson a felső jelölőnégyzetet, hogy a listában jelölje ki az összes virtuális gép.

A parancssávon kattintson **szolgáltatások** válassza **változáskövetés**, **készlet**, vagy **frissítéskezelés**.

> [!NOTE]
> **Változások követése** és **készlet** ugyanahhoz a megoldáshoz, használja, ha engedélyezve van a másik engedélyezve legyen.

Az alábbi képen van a kezeléséhez. Változáskövetés és a készlet ugyanazon elrendezés és viselkedésének rendelkezik.

A virtuális gépek listájának csak az ugyanazon előfizetésben és helyen lévő virtuális gépek van szűrve. Ha a virtuális gépek háromnál több erőforrás-csoportok, az első három erőforráscsoportok ki van jelölve.

A szűrő vezérlők segítségével válassza ki a virtuális gépek különböző előfizetések, helyek és erőforrás-csoportok.

![A bevezetni frissítés felügyeleti megoldás](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Tekintse át a naplóelemzési munkaterület és Automation-fiók. Egy új munkaterületet, és az Automation-fiók alapértelmezés szerint kiválasztva. Ha rendelkezik egy meglévő Naplóelemzési munkaterület és Automation-fiók használatához kattintson a kívánt **módosítása** és az választhatják a **konfigurációs** lap. Ha befejezte, kattintson a **Mentés** gombra.

![Válassza ki a munkaterületet, és a fiók](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Törölje a virtuális gépek adatait, amely nem kívánja engedélyezni a jelölőnégyzetét. Nem engedélyezhető a virtuális gépek már van bejelölve.

Kattintson a **engedélyezése** ahhoz, hogy a megoldás. A megoldás engedélyezése akár 15 percet is igénybe vehet.

## <a name="troubleshooting"></a>Hibaelhárítás

Amikor bevezetési több számítógépen, előfordulhat, hogy megjelenítése gépek **nem engedélyezhető a**. Másik oka miért bizonyos számítógépeken nem lehet engedélyezni. A következő szakaszok bemutatják a lehetséges okok a **nem engedélyezhető a** állapot, a virtuális gép előkészítésére tett kísérlet közben.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>Virtuális gép egy másik munkaterület jelent: "\<workspaceName\>".  A engedélyező használandó konfigurációjának módosítása

**OK**: Ez a hiba azt mutatja, hogy a virtuális Gépet, amely egy másik munkaterületet beépített jelentések kívánt.

**Megoldás**: módosítsa a munkaterület beágyazott hivatkozást, vagy kattintson a gombra kattintva megcélzott **módosítása**. Az alábbi parancsfájlok segítségével módosítsa a munkaterület az egyes gépek tervezett azt.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>Virtuális gép jelent, amely nem érhető el ebben az előfizetésben munkaterület

**OK**: A munkaterület, amely a virtuális gép jelent:

* Egy másik előfizetésben található, vagy
* Már nem létezik, vagy
* A hozzáférési engedélyeket nincs erőforráscsoportban található

**Megoldás**: található automation-fiók társítva, amely a virtuális gép jelent a munkaterület és a bevezetni a virtuális gép a hatókör konfigurációjának módosításával.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>Virtuális gép operációs rendszer verziójának vagy terjesztési nem támogatott

**OK:** a megoldás nem támogatott az összes Linux terjesztésekről vagy a Windows összes verzióján.

**Megoldás:** tekintse meg a [a támogatott ügyfelek listáját](automation-update-management.md#clients) a megoldáshoz.

### <a name="classic-vms-cannot-be-enabled"></a>Klasszikus virtuális gépeket nem lehet engedélyezni.

**OK**: a klasszikus üzembe helyezési modellt használó virtuális gépek nem támogatottak.

**Megoldás**: telepítse át a virtuális gépet a resource manager üzembe helyezési modellben. Ezzel kapcsolatban lásd: [klasszikus telepítési modell-erőforrások áttelepítése](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>Virtuális gép le van állítva. (felszabadítva)

**OK**: A virtuális gép nem található egy **futtató** állapotát.

**Megoldás**: annak érdekében, hogy bevezetésében egy virtuális Gépet a virtuális gép megoldáshoz futnia kell. Kattintson a **VM indítása** beágyazott hivatkozást a virtuális gép indítása nélkül lépjen másik lapra a lapról.

## <a name="next-steps"></a>További lépések

Most, hogy a megoldás a virtuális gépek számára engedélyezve van, látogasson el a felügyeleti frissítése – áttekintés áttekintésével megismerheti, hogyan megtekintéséhez a gépek számára a frissítések értékelését.

> [!div class="nextstepaction"]
> [Frissítéskezelés - nézet frissítések értékelését](./automation-update-management.md#viewing-update-assessments)

A megoldás és a használatukat oktatóanyagok hozzáadása:

* [Az oktatóanyag - frissítések kezelése a virtuális gép számára](automation-tutorial-update-management.md)

* [Az oktatóanyag - szoftver, a virtuális gép](automation-tutorial-installed-software.md)

* [Az oktatóanyag - hibáinak elhárítása a virtuális gép változások](automation-tutorial-troubleshoot-changes.md)