---
title: Az Azure virtuális előkészíteni az Update Management, Change Tracking and Inventory megoldások
description: Ismerje meg, hogyan való előkészítése az Azure virtuális gép az Update Management, Change Tracking és Inventory megoldásainak részét képező Azure Automation.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 039e2d3c70493868ca2f79e89fc82d8970ec6865
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032398"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Az Azure virtuális gép felvétele az Update Management, Change Tracking and Inventory megoldásai

Az Azure Automation segítségével a megoldások kezelése az operációs rendszer biztonsági frissítéseit, a változások követése és a leltár a számítógépeken telepítve legyen. Gépek előkészítésének több módja is van. Segítségével készítheti elő a virtuális gépről, a megoldás [az Automation-fiókjából](automation-onboard-solutions-from-automation-account.md), [több gép tallózással](automation-onboard-solutions-from-browse.md), vagy egy [runbook](automation-onboard-solutions.md). Ez a cikk az Azure virtuális gépek ezekkel a megoldásokkal előkészítést részletezi.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="enable-the-solutions"></a>A megoldások engedélyezése

Nyissa meg egy meglévő virtuális gépet. Alatt **OPERATIONS**válassza **frissítéskezelés**, **készlet**, vagy **Change tracking**.

Ellenőrizze, hogy a megoldás engedélyezéséhez a virtuális gép csak **engedélyezése a virtuális gép** van kiválasztva. Készítse elő a megoldás több gép válassza **ebbe az előfizetésbe tartozó virtuális gépek engedélyezése**, majd válassza ki **kattintással jelölje ki a gépek engedélyezéséhez**. Megtudhatja, hogyan készítse elő több gép egyszerre, lásd: [előkészíteni az Update Management, Change Tracking és Inventory megoldásainak](automation-onboard-solutions-from-automation-account.md).

Az Azure Log Analytics-munkaterületet és Automation-fiókot, majd válassza ki és **engedélyezése** a megoldás engedélyezéséhez. A megoldás engedélyezése akár 15 percet is igénybe vehet.

![Előkészíteni az Update Management megoldás](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Nyissa meg a más megoldásokkal, és válassza ki **engedélyezése**. A Log Analytics és az automatizálási fiók legördülő listák le vannak tiltva, mivel ezek a megoldások a ugyanazon a munkaterületen, és az Automation-fiókot használja, mint az előzőekben engedélyezett megoldás.

> [!NOTE]
> **A Change tracking** és **készlet** ugyanazt a megoldást használhatják. Ha e megoldások egyikének engedélyezve van, a másik is engedélyezve van.

## <a name="scope-configuration"></a>Hatókör-konfiguráció

Minden egyes megoldás hatókör-konfigurációt használja a munkaterületen, amelyekre a számítógépeket, amelyek a megoldás beszerzése. A hatókör-konfiguráció olyan egy vagy több mentett keresések, amelyek a megoldás az adott számítógépek hatókörének korlátozása. A hatókör-konfigurációk, az Automation-fiók eléréséhez a **kapcsolódó erőforrások**válassza **munkaterület**. A munkaterület alatt **MUNKATERÜLET ADATFORRÁSAI**válassza **hatókör-konfigurációk**.

Ha a kiválasztott munkaterületen még nem rendelkezik a Frissítésfelügyeleti vagy változáskövetési megoldások, a következő hatókör-konfigurációk jönnek létre:

* **MicrosoftDefaultScopeConfig – változáskövetési**

* **MicrosoftDefaultScopeConfig-frissítések**

Ha a kijelölt munkaterület a megoldás már tartozik, a megoldás nem újratelepítése, és a hatókör-konfiguráció nem adódik.

Kattintson a három pontra (**...** ) a konfigurációkat, és válassza ki valamelyik **szerkesztése**. Az a **hatókör-konfiguráció szerkesztése** ablaktáblán válassza előbb **válassza ki a számítógépcsoportokat**. A **számítógépcsoportok** panelen látható a mentett kereséseket, a hatókör-konfiguráció létrehozásához használt.

## <a name="saved-searches"></a>Mentett keresések

Amikor számítógépet adnak az Update Management, a Change Tracking vagy a készlet megoldások, a számítógép hozzáadása egy két mentett keresések a munkaterületén. A mentett keresések olyan lekérdezések, amelyek tartalmazzák a számítógépeken, amelyekre vonatkozik a ezeket a megoldásokat.

Lépjen a munkaterülethez. A **általános**válassza **mentett keresések**. A két mentett keresések, ezek a megoldások által használt az alábbi táblázatban láthatók:

|Name (Név)     |Kategória  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Change Tracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Frissítések        | Updates__MicrosoftDefaultComputerGroup         |

Válassza ki, vagy a mentett keresések megtekintése a lekérdezést, amely a csoport feltöltésére szolgál. Az alábbi képen látható, a lekérdezés, és az eredményeket:

![Mentett keresések](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Munkaterület leválasztása

A következő megoldásokat a Log Analytics-munkaterület függnek:

* [Frissítéskezelés](automation-update-management.md)
* [Változáskövetés](automation-change-tracking.md)
* [Virtuális gépek indítása/leállítása munkaidőn kívül](automation-solution-vm-management.md)

Ha úgy dönt, hogy már nem szeretne az Automation-fiók integrálása a Log Analytics-szel, megszüntetheti a fiók közvetlenül az Azure Portalról.  Mielőtt továbblépne, először el kell távolítania a megoldások azt korábban említettük, ellenkező esetben ez a folyamat megakadályozza a folytatás. Tekintse át a cikk az adott megoldás importált távolítsa el a szükséges lépések megértéséhez.

Miután eltávolítja ezeket a megoldásokat, az Automation-fiók leválasztása a következő lépéseket végezheti.

> [!NOTE]
> Egyes megoldások, beleértve az Azure SQL-figyelési megoldás a korábbi lehet, hogy létre automation-adategységeket, és is szükség lehet a munkaterület leválasztása előtt el kell távolítani.

1. Az Azure Portalról nyissa meg az Automation-fiókját, és a az Automation-fiók oldalon válassza ki **csatolt munkaterület** a szakaszában **kapcsolódó erőforrások** a bal oldalon.

1. A leválasztás munkaterület oldalán kattintson a **munkaterület leválasztása**.

   ![Munkaterület oldalán leválasztása](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

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

Az oktatóanyagok, megtudhatja, hogyan használhatja őket a megoldások továbbra is:

* [Oktatóanyag – a virtuális gép frissítéseinek kezelése](automation-tutorial-update-management.md)
* [Oktatóanyag – virtuális gépek szoftvereinek azonosítása](automation-tutorial-installed-software.md)
* [Oktatóanyag – a virtuális gép módosításainak hibaelhárítása](automation-tutorial-troubleshoot-changes.md)
