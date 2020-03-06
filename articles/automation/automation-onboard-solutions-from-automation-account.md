---
title: Ismerje meg, hogyan készíthet Update Management-, Change Tracking-és leltározási megoldásokat a Azure Automation
description: Ismerje meg, hogyan készíthet Azure-beli virtuális gépeket a Azure Automation részét képező Update Management-, Change Tracking-és leltározási megoldásokkal.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: cf82dddf281e8e6f1348884702e32330dee4781b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372954"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Update Management-, Change Tracking-és leltározási megoldások

Azure Automation megoldásokat kínál az operációs rendszer biztonsági frissítéseinek kezelésére, a változások nyomon követésére és a számítógépekre telepített termékek leltározására. A gépek bevezetésének számos módja van, a megoldás bevezetését [egy virtuális gépről](automation-onboard-solutions-from-vm.md), [több](automation-onboard-solutions-from-browse.md)gépről, az Automation-fiókjából vagy a [runbook](automation-onboard-solutions.md)-ból végezheti el. Ez a cikk ismerteti ezeket a megoldásokat az Automation-fiókjából.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba a https://portal.azure.com címen.

## <a name="enable-solutions"></a>Megoldások engedélyezése

Navigáljon az Automation-fiókjához, és válassza a **leltár** vagy a **change Tracking** elemet a **konfiguráció**felügyelete alatt.

Válassza ki az Log Analytics munkaterületet és Automation-fiókot, majd kattintson az **Engedélyezés** gombra a megoldás engedélyezéséhez. A megoldás engedélyezése akár 15 percet is igénybe vehet.

![Előkészítési leltározási megoldás](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

> [!NOTE]
> A megoldások engedélyezésekor csak bizonyos régiók esetén lehet összekapcsolni egy Log Analytics-munkaterületet és egy Automation-fiókot.
>
> A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](how-to/region-mappings.md).

A Change Tracking és az Inventory megoldással [változáskövetési](automation-vm-change-tracking.md) és [leltárkészítési](automation-vm-inventory.md) képességeket biztosíthat a virtuális gépek számára. Ebben a lépésben engedélyezzük a megoldást egy virtuális gépen.

Ha a Change Tracking és a Inventory megoldás bevezetési értesítése befejeződik, válassza az **Update Management (** **frissítés kezelése) lehetőséget.**

Az Update Management megoldás lehetővé teszi az Azure-és hibrid virtuális gépek frissítéseinek és javításának kezelését. Értékelheti az elérhető frissítések állapotát, beütemezheti a szükséges frissítések telepítését, és áttekintheti a telepítési eredményeket, hogy ellenőrizze, sikeres volt-e a frissítések alkalmazása.

A megoldás engedélyezése lapon a kiválasztott Log Analytics munkaterület megegyezik az előző lépésben használt munkaterülettel. Kattintson az **Engedélyezés** gombra a Update Management megoldás bevezetéséhez. A megoldás engedélyezése akár 15 percet is igénybe vehet.

![Előkészítési frissítési megoldás](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Hatókör-konfiguráció

Mindegyik megoldás egy hatókör-konfigurációt használ a munkaterületen a megoldást futtató számítógépek célzásához. A hatókör-konfiguráció egy vagy több mentett keresés csoportja, amely a megoldás hatókörének meghatározott számítógépekre való korlátozására szolgál. A hatókör-konfigurációk eléréséhez az Automation-fiókban a **kapcsolódó erőforrások**területen válassza a **munkaterület**lehetőséget. Ezután a munkaterület- **adatforrások**területen válassza a **hatókör-konfigurációk**elemet.

Ha a kiválasztott munkaterület még nem rendelkezik Update Management vagy Change Tracking megoldással, a következő hatókör-konfigurációk jönnek létre:

* **MicrosoftDefaultScopeConfig – változáskövetési**

* **MicrosoftDefaultScopeConfig – frissítések**

Ha a kiválasztott munkaterület már rendelkezik a megoldással, a rendszer nem telepíti újra a megoldást, és a hatókör-konfiguráció nincs hozzáadva.

## <a name="saved-searches"></a>Mentett keresések

Ha hozzáad egy számítógépet a Update Managementhoz vagy a Change Trackinghoz és a leltári megoldásokhoz, a rendszer a munkaterületen két mentett keresés egyikére adja hozzá őket. Ezek a mentett keresések azok a lekérdezések, amelyek tartalmazzák azokat a számítógépeket, amelyek ezekre a megoldásokra vannak rendelve.

Navigáljon a Log Analytics munkaterületre, és válassza a **mentett keresések** **általános**lehetőséget. A megoldások által használt két mentett keresés a következő táblázatban látható:

|Name (Név)     |Kategória  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Változáskövetési       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Frissítések        | Updates__MicrosoftDefaultComputerGroup         |

Válassza a mentett keresés lehetőséget a csoport feltöltéséhez használt lekérdezés megtekintéséhez. Az alábbi ábrán a lekérdezés és annak eredményei láthatók:

![Mentett keresések](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Azure-beli virtuális gépek előkészítése

Az Automation-fiókban válassza a **leltár** vagy a **változások követése** lehetőséget a **konfiguráció**felügyelete alatt, vagy az Update **Management**alatt kattintson a **frissítés kezelése** lehetőségre.

Kattintson az **+ Azure-beli virtuális gépek hozzáadása**lehetőségre, válasszon ki egy vagy több virtuális gépet a listából. A nem engedélyezhető virtuális gépek szürkén jelennek meg, és nem választhatók ki. Az Azure-beli virtuális gépek bármely régióban létezhetnek, függetlenül az Automation-fiókja helyétől. A **Update Management engedélyezése** lapon kattintson az **Engedélyezés**gombra. Ez a művelet hozzáadja a kiválasztott virtuális gépeket a számítógép csoport mentett kereséséhez a megoldáshoz.

![Azure-beli virtuális gépek engedélyezése](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Nem Azure-beli gép előkészítése

Az Azure-ban nem szereplő gépeket manuálisan kell hozzáadni. Az Automation-fiókban válassza a **leltár** vagy a **változások követése** lehetőséget a **konfiguráció**felügyelete alatt, vagy az Update **Management**alatt kattintson a **frissítés kezelése** lehetőségre.

Kattintson a **nem Azure-beli gép hozzáadása**lehetőségre. Ez a művelet egy új böngészőablakot nyit meg, amely [útmutatást nyújt a Microsoft monitoring Agent telepítésére és konfigurálására a gépen](../azure-monitor/platform/log-analytics-agent.md) , így a gép megkezdheti a jelentéskészítést a megoldásba. Ha olyan gépet készít elő, amelyet jelenleg System Center Operations Manager felügyel, nincs szükség új ügynökre, a munkaterület adatai bekerülnek a meglévő ügynökbe.

## <a name="onboard-machines-in-the-workspace"></a>Fedélzeti gépek a munkaterületen

Manuálisan telepített gépeket vagy számítógépeket, amelyeket a munkaterülethez már jelentettek, a megoldás engedélyezéséhez hozzá kell adni a Azure Automationhoz. Az Automation-fiókban válassza a **leltár** vagy a **változások követése** lehetőséget a **konfiguráció**felügyelete alatt, vagy az Update **Management**alatt kattintson a **frissítés kezelése** lehetőségre.

Válassza a **számítógépek kezelése**lehetőséget. Ez a művelet megnyitja a **gépek kezelése** lapot. Ezen a lapon engedélyezheti a megoldást a gépek kiválasztására, az összes rendelkezésre álló gépre, illetve a megoldás engedélyezésére minden jelenlegi gépen, és engedélyezheti az összes jövőbeli gépen. Előfordulhat, hogy a **gépek kezelése** gomb szürkén jelenik meg, ha korábban az Engedélyezés lehetőséget választotta az **összes rendelkezésre álló és jövőbeli gépen**.

![Mentett keresések](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Az összes elérhető gép

Ha engedélyezni szeretné a megoldást az összes rendelkezésre álló gépen, válassza az Engedélyezés lehetőséget az **összes elérhető gépen**. Ez a művelet letiltja a vezérlőt a számítógépek egyenkénti hozzáadásához. Ez a feladat hozzáadja a munkaterületnek jelentett számítógépek összes nevét a számítógépcsoport mentett keresési lekérdezéséhez. Ha be van jelölve, ez a művelet letiltja a **számítógépek kezelése** gombot.

### <a name="all-available-and-future-machines"></a>Az összes elérhető és jövőbeli gép

Az összes rendelkezésre álló és jövőbeli gépen a megoldás engedélyezéséhez válassza az **Engedélyezés lehetőséget az összes rendelkezésre álló és jövőbeli gépen**. Ez a lehetőség törli a mentett kereséseket és a hatókör-konfigurációkat a munkaterületről. Ez a művelet megnyitja a megoldást az összes olyan Azure-beli és nem Azure-beli gépre, amely a munkaterületre jelentett. Ha bejelöli ezt a jelölőnégyzetet, ez a művelet véglegesen letiltja a **gépek kezelése** gombot, mivel nincs bal oldali hatókör-konfiguráció.

A hatókör-konfigurációkat vissza is veheti a kezdeti mentett keresések visszaadásával. További információ: [mentett keresések](#saved-searches).

### <a name="selected-machines"></a>Kijelölt gépek

A megoldás egy vagy több gépen való engedélyezéséhez válassza az **Engedélyezés a kiválasztott gépeken** lehetőséget, majd kattintson a **Hozzáadás** elemre minden olyan gép mellett, amelyet hozzá szeretne adni a megoldáshoz. Ez a feladat hozzáadja a kiválasztott számítógépnévket a számítógép csoport mentett keresési lekérdezéséhez a megoldáshoz.

## <a name="unlink-workspace"></a>Munkaterület leválasztása

A következő megoldások Log Analytics munkaterülettől függenek:

* [Frissítéskezelés](automation-update-management.md)
* [Változáskövetés](automation-change-tracking.md)
* [Start/Stop VMs during off-hours](automation-solution-vm-management.md)

Ha úgy dönt, hogy már nem szeretné integrálni az Automation-fiókot egy Log Analytics munkaterülettel, közvetlenül a Azure Portalból is leválaszthatja a fiókját.  Mielőtt továbblépne, először el kell távolítania a korábban említett megoldásokat, ellenkező esetben a folyamat nem fog folytatódni. Tekintse át az importált konkrét megoldásról szóló cikket az eltávolításához szükséges lépések megismeréséhez.

A megoldások eltávolítása után az alábbi lépések végrehajtásával leválaszthatja az Automation-fiókját.

> [!NOTE]
> Előfordulhat, hogy néhány megoldás, például az Azure SQL-figyelési megoldás korábbi verziói automatizálási eszközöket hoztak létre, és a munkaterület leválasztása előtt is el kell távolítani őket.

1. A Azure Portal nyissa meg az Automation-fiókját, és az Automation-fiók lapon válassza a **csatolt munkaterület** lehetőséget a bal oldalon található **kapcsolódó erőforrások** szakaszban.

2. A munkaterület leválasztása lapon kattintson a **munkaterület leválasztása**elemre.

   ![Munkaterület leválasztása lap](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   A folytatáshoz meg kell kapnia egy figyelmeztetést.

3. Míg Azure Automation megpróbálja leválasztani a fiókot a Log Analytics munkaterületen, nyomon követheti a menü **értesítések** részén látható előrehaladást.

Ha a Update Management megoldást használta, érdemes lehet eltávolítani a következő elemeket, amelyekre már nincs szükség a megoldás eltávolítása után.

* Frissítési ütemtervek – minden olyan névvel rendelkezik, amely megfelel a létrehozott frissítési példányoknak.

* A megoldáshoz létrehozott hibrid feldolgozói csoportok – mindegyik neve hasonló lesz a machine1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Ha az indítási és leállítási virtuális gépeket a munkaidőn kívüli megoldás során használta, szükség esetén előfordulhat, hogy el kívánja távolítani a következő elemeket, amelyek már nem szükségesek a megoldás eltávolítása után.

* VM runbook-ütemtervek elindítása és leállítása
* VM-runbookok elindítása és leállítása
* Változók

Azt is megteheti, hogy kikapcsolja a munkaterületet az Automation-fiókjából a Log Analytics munkaterületről. A munkaterületen válassza az **Automation-fiók** lehetőséget a **kapcsolódó erőforrások**területen. Az Automation-fiók lapon válassza a **fiók megszüntetése**lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Virtuális gép eltávolítása Update Managementról:

* A Log Analytics munkaterületen távolítsa el a virtuális gépet a hatókör-konfigurációs `MicrosoftDefaultScopeConfig-Updates`mentett keresésével. A mentett keresések a munkaterület **általános** területén találhatók.
* Távolítsa el a [Microsoft monitoring agentet](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) vagy a [Linux rendszerhez készült log Analytics-ügynököt](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Következő lépések

Folytassa a megoldások oktatóanyagait, hogy megtudja, hogyan használhatja őket.

* [Oktatóanyag – a virtuális gép frissítéseinek kezelése](automation-tutorial-update-management.md)

* [Oktatóanyag – szoftverek azonosítása virtuális gépen](automation-tutorial-installed-software.md)

* [Oktatóanyag – virtuális gépek változásainak megoldása](automation-tutorial-troubleshoot-changes.md)
