---
title: Ismerje meg, hogyan kell előkészíteni az Update Management, Change Tracking and Inventory megoldások az Azure Automationben
description: Ismerje meg, hogyan üzembe helyezni egy Azure-beli virtuális gépek az Azure Automation részét képező az Update Management, Change Tracking and Inventory megoldásaival
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/16/2018
ms.topic: conceptual
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 0d67571024c9ace3618f7ae6172122ac48b57faa
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106721"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Előkészítése az Update Management, Change Tracking és Inventory megoldásainak

Az Azure Automation segítségével a megoldások kezelése az operációs rendszer biztonsági frissítéseit, nyomon követéséhez és szoftverleltár, a számítógépeken telepítve legyen. Számos módon gépek előkészítésének, segítségével készítheti elő a megoldás [a virtuális gépről](automation-onboard-solutions-from-vm.md), [több gép tallózással](automation-onboard-solutions-from-browse.md), az Automation-fiók vagy a [runbook](automation-onboard-solutions.md). Ez a cikk az Automation-fiók ezekkel a megoldásokkal előkészítést részletezi.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba a https://portal.azure.com címen.

## <a name="enable-solutions"></a>Megoldások engedélyezése

Keresse meg az Automation-fiókját, és válassza **készlet** vagy **Change tracking** alatt **konfigurációkezelés**.

Válassza ki a Log analytics-munkaterületet és Automation-fiók, és kattintson a **engedélyezése** a megoldás engedélyezéséhez. A megoldás engedélyezése akár 15 percet is igénybe vehet.

![Előkészítheti az Inventory megoldás](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

Ha engedélyezte a megoldásokat, csak bizonyos régiókban támogatottak egy Log Analytics-munkaterületet és Automation-fiók összekapcsolása.

Az alábbi táblázat bemutatja a támogatott leképezések:

|**Log Analytics-munkaterület régiója**|**Az Azure Automation-régió**|
|---|---|
|Délkelet-Ausztrália|Délkelet-Ausztrália|
|CanadaCentral|CanadaCentral|
|CentralIndia|CentralIndia|
|EastUS|EastUS2|
|JapanEast|JapanEast|
|SoutheastAsia|SoutheastAsia|
|WestCentralUS|WestCentralUS|
|WestEurope|WestEurope|
|UKSouth|UKSouth|
|USGovVirginia|USGovVirginia|
|EastUS2EUAP|CentralUSEUAP|

A Change Tracking és az Inventory megoldással [változáskövetési](automation-vm-change-tracking.md) és [leltárkészítési](automation-vm-inventory.md) képességeket biztosíthat a virtuális gépek számára. Ebben a lépésben engedélyezzük a megoldást egy virtuális gépen.

Ha a Change tracking és az Inventory megoldás előkészítése befejeződött az értesítés szerint, a **KONFIGURÁCIÓKEZELÉS** területen kattintson az **Update Management** lehetőségre.

Az Update Management megoldás segítségével kezelheti az Azure-beli Windows rendszerű virtuális gépek frissítéseit és javításait. Felmérheti az elérhető frissítések állapotát, ütemezheti a szükséges frissítések telepítését, és áttekintheti a telepítési eredményeket, hogy ellenőrizze, sikeres volt-e a frissítések telepítése a virtuális gépen. Ez a művelet engedélyezett a megoldást a virtuális Gépen.

Válassza ki **frissítéskezelés** alatt **az UPDATE MANAGEMENT**. A kiválasztott Log Analytics-munkaterület megegyezik az előző lépésben használt munkaterülettel. Az Update Management megoldás előkészítéséhez kattintson az **Engedélyezés** lehetőségre. A megoldás engedélyezése akár 15 percet is igénybe vehet.

![Előkészíteni az update megoldás](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Hatókör-konfiguráció

Egyes megoldások belül a munkaterülethez hatókör-konfigurációt használ, amelyekre a számítógépeket, amelyek a megoldás beszerzése. A hatókör-konfiguráció olyan egy vagy több mentett keresések, amellyel korlátozhatja a megoldást az adott számítógépek számára. A hatókör-konfigurációk, az Automation-fiók alatt eléréséhez **kapcsolódó erőforrások**válassza **munkaterület**. Majd munkaterületén **MUNKATERÜLET ADATFORRÁSAI**válassza **hatókör-konfigurációk**.

Ha a kiválasztott munkaterületen még nem rendelkezik a Frissítésfelügyeleti vagy változáskövetési megoldások, a következő hatókör-konfigurációk jönnek létre:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Ha a kijelölt munkaterület a megoldás már tartozik, a megoldás nem újból üzembe helyezte, és a hatókör-konfiguráció nem adódik hozzá.

## <a name="saved-searches"></a>Mentett keresések

A Frissítésfelügyeleti vagy a Change Tracking és Inventory megoldásainak számítógép ad hozzá, amikor azokat hozzáadják őket a két mentett keresés a munkaterületén. Ezek a mentett keresések olyan lekérdezések, amelyek tartalmazzák a számítógépeken, amelyekre vonatkozik a ezeket a megoldásokat.

Az Automation-fiókjában keresse meg és válassza **mentett keresések** alatt **általános**. A két mentett keresések, ezek a megoldások által használt az alábbi táblázatban láthatók:

|Name (Név)     |Kategória  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Change Tracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Frissítések        | Updates__MicrosoftDefaultComputerGroup         |

Válassza ki vagy mentett keresést a lekérdezés a csoport feltöltéséhez használt megtekintéséhez. Az alábbi képen látható, a lekérdezés, és az eredményeket:

![Mentett keresések](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Az Azure virtuális gépek előkészítése

Az Automation a fiókot válassza **készlet** vagy **a Change tracking** alatt **konfigurációkezelés**, vagy **frissítéskezelés** a **az UPDATE MANAGEMENT**.

Kattintson a **+ Azure-beli virtuális gépek hozzáadása**, válasszon ki egy vagy több virtuális gépet a listából. Virtuális gépek, amelyek nem lehet engedélyezni, szürkén jelennek meg lejárt, ezért nem tudja, ki kell jelölni. Az a **Update Management engedélyezése** kattintson **engedélyezése**. Ez a művelet a kijelölt virtuális gépek hozzáadása a számítógép (csoport)-beli mentett keresés a megoldáshoz.

![Az Azure virtuális gépek engedélyezése](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Nem Azure-beli gép előkészítése

Gépek Azure-ban nem kell manuálisan kell hozzáadni. Az Automation a fiókot válassza **készlet** vagy **a Change tracking** alatt **konfigurációkezelés**, vagy **frissítéskezelés** a **az UPDATE MANAGEMENT**.

Kattintson a **hozzáadása nem Azure-beli gép**. Ez a művelet egy új böngészőablakot, és megnyílik a [való telepítése és konfigurálása a Microsoft Monitoring Agent a gépen](../azure-monitor/platform/log-analytics-agent.md) , a gép megkezdheti a megoldás a jelentéskészítés. Ha Ön előkészítése a System Center Operations Manager által kezelt számítógép, egy új ügynököt nem szükséges, a munkaterület-információk írja be a rendszer a meglévő ügynököt.

## <a name="onboard-machines-in-the-workspace"></a>Előkészítheti a gépeket a munkaterületen

Manuálisan telepített gépek vagy a gép már a munkaterületre jelentő kell adni az Azure Automation-megoldás engedélyezni kell. Az Automation a fiókot válassza **készlet** vagy **a Change tracking** alatt **konfigurációkezelés**, vagy **frissítéskezelés** a **az UPDATE MANAGEMENT**.

Válassza ki **gépek kezelése**. Ez a művelet megnyílik a **gépek kezelése** lapot. Ezen a lapon válassza konfigurálásában a gépek, az összes rendelkezésre álló gépek, a megoldás engedélyezéséhez vagy a megoldás összes aktuális gépek engedélyezéséhez és összes további gépen engedélyezi azt teszi lehetővé. A **gépek kezelése** gomb előfordulhat, hogy szürkén jelennek meg, ha a beállítás korábban kiválasztott **engedélyezés minden rendelkezésre álló és jövőbeli gépen**.

![Mentett keresések](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Az összes rendelkezésre álló gépek

Az összes rendelkezésre álló gépek a megoldás engedélyezéséhez válassza **engedélyezés minden rendelkezésre álló gépen**. Ez a művelet letiltja a vezérlőelemet a gépek egyenként hozzáadása. Ez a feladat hozzáadása a a keresési lekérdezés mentett számítógépcsoport-munkaterületre jelentő gépek összes nevét. Ha kijelölve, ez a művelet letiltja a **gépek kezelése** gombra.

### <a name="all-available-and-future-machines"></a>Az összes rendelkezésre álló és jövőbeli gépen

Az összes rendelkezésre álló és jövőbeli gépen a megoldás engedélyezéséhez válassza **engedélyezés minden rendelkezésre álló és jövőbeli gépen**. Ez a lehetőség törli a mentett keresések és a hatókör-konfigurációk a munkaterületről. Ez a művelet megnyitja a megoldás az Azure és a nem Azure-a-munkaterületre jelentő gépek. Ha kijelölve, ez a művelet letiltja a **gépek kezelése** véglegesen gomb bal nincs hatókör-konfiguráció nem.

### <a name="selected-machines"></a>Kijelölt gépek

Egy vagy több gépet a megoldás engedélyezéséhez válassza **engedélyezése a kiválasztott gépeken** kattintson **hozzáadása** mellett minden gép, a megoldáshoz hozzá szeretné. Ez a feladat a keresési lekérdezés megoldás mentett számítógépcsoport-ad hozzá a kijelölt számítógépek nevével.

## <a name="unlink-workspace"></a>Munkaterület leválasztása

A következő megoldásokat a Log Analytics-munkaterület függnek:

* [Frissítéskezelés](automation-update-management.md)
* [Változáskövetés](automation-change-tracking.md)
* [Virtuális gépek indítása/leállítása munkaidőn kívül](automation-solution-vm-management.md)

Ha úgy dönt, hogy már nem szeretne az Automation-fiók integrálása a Log Analytics-szel, megszüntetheti a fiók közvetlenül az Azure Portalról.  A folytatás előtt először el kell távolítania a megoldások azt korábban említettük, ellenkező esetben ez a folyamat megakadályozza a folytatás. Tekintse át a cikk az adott megoldás importálása távolítsa el a szükséges lépések megértéséhez.

Miután eltávolítja ezeket a megoldásokat, befejezheti az Automation-fiók leválasztása a következő lépéseket.

> [!NOTE]
> Egyes megoldások, beleértve az Azure SQL-figyelési megoldás a korábbi lehet, hogy létre automation-adategységeket, és is szükség lehet a munkaterület leválasztása előtt el kell távolítani.

1. Az Azure Portalról nyissa meg az Automation-fiókját, és a az Automation-fiók oldalon válassza ki **csatolt munkaterület** feliratú szakasz alatt **kapcsolódó erőforrások** a bal oldalon.

2. A leválasztás munkaterület oldalán kattintson a **munkaterület leválasztása**.

   ![Munkaterület oldalán leválasztása](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   Kapni fog egy kérdés ellenőrzése, hogy folytatja a műveletet.

3. Miközben az Azure Automation megpróbálja a fiók leválasztása a Log Analytics-munkaterület, nyomon követheti a folyamat állapotát **értesítések** a menüből.

Az Update Management megoldás használata esetén igény szerint, előfordulhat, hogy el kívánja távolítani a következő elemek, amelyek a megoldás eltávolítása után már nem szükséges.

* Frissítés ütemezése – a neve megegyezik a létrehozott frissítéstelepítések lesz)

* A megoldás - számára létrehozott hibrid feldolgozócsoportok egyes lesznek elnevezve hasonlóan a: gép1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Ha használta a kezdő és a virtuális gépek leállítása munkaidőn kívül megoldás, igény szerint érdemes eltávolítani a következő elemek, amelyek a megoldás eltávolítása után már nem szükséges.

* Elindíthatja és leállíthatja a virtuális gép runbook ütemezése
* Virtuális gép runbookok elindítása és leállítása
* Változók

## <a name="next-steps"></a>További lépések

Az oktatóanyagok, megtudhatja, hogyan használhatja azokat a megoldásokat a továbbra is.

* [Oktatóanyag – a virtuális gép frissítéseinek kezelése](automation-tutorial-update-management.md)

* [Oktatóanyag – virtuális gépek szoftvereinek azonosítása](automation-tutorial-installed-software.md)

* [Oktatóanyag – a virtuális gép módosításainak hibaelhárítása](automation-tutorial-troubleshoot-changes.md)
