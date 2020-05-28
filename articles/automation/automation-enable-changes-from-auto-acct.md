---
title: Azure Automation Change Tracking és leltár engedélyezése az Automation-fiókból
description: Ez a cikk azt ismerteti, hogyan engedélyezhető az Change Tracking és a leltár egy Automation-fiókból.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 31390d260f51bdc1e9858d21153aa96ea7c54405
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116654"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>A Change Tracking és az Inventory engedélyezése Automation-fiókból

Ez a cikk azt ismerteti, hogyan használható az Automation-fiókja a virtuális gépek [change Tracking és leltározási](change-tracking.md) funkciójának engedélyezésére a környezetben. Az Azure-beli virtuális gépek méretének engedélyezéséhez Change Tracking és leltár használatával engedélyeznie kell egy meglévő virtuális gépet. 

> [!NOTE]
> A Change Tracking és a leltár engedélyezésekor csak bizonyos régiók támogatottak Log Analytics munkaterület és egy Automation-fiók összekapcsolásához. A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](how-to/region-mappings.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](automation-offering-get-started.md) a gépek kezeléséhez.
* Egy [virtuális gép](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba https://portal.azure.com .

## <a name="enable-change-tracking-and-inventory"></a>A Change Tracking és az Inventory engedélyezése

1. Navigáljon az Automation-fiókjához, és válassza a **leltár** vagy a **change Tracking** elemet a **konfiguráció**felügyelete alatt.

2. Válassza ki az Log Analytics munkaterületet és Automation-fiókot, majd kattintson az **Engedélyezés** lehetőségre a Change Tracking és a leltár engedélyezéséhez. A telepítés elvégzése akár 15 percet is igénybe vehet.

    ![A Change Tracking és az Inventory engedélyezése](media/automation-enable-changes-from-auto-acct/onboardsolutions.png)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>A központi telepítés hatókörének korlátozása

A Change Tracking és a leltár a munkaterületen belüli hatókör-konfigurációval célozza meg a számítógépeket, hogy fogadják a módosításokat. További információ: a [change Tracking és a leltár központi telepítési hatókörének korlátozása](automation-scope-configurations-change-tracking.md).

## <a name="enable-azure-vms"></a>Azure-beli virtuális gépek engedélyezése

1. Az Automation-fiókban válassza a **leltár** vagy a **change Tracking** elemet a **konfiguráció**felügyelete alatt.

2. Kattintson az **+ Azure-beli virtuális gépek hozzáadása** lehetőségre, és válasszon ki egy vagy több virtuális gépet a listából. A nem engedélyezhető virtuális gépek szürkén jelennek meg, és nem választhatók ki. Az Azure-beli virtuális gépek bármely régióban létezhetnek, függetlenül az Automation-fiókja helyétől. 

3. Kattintson az **Engedélyezés** lehetőségre a kiválasztott virtuális gépek hozzáadásához a csoport mentett kereséséhez a szolgáltatásban. További információ: a [change Tracking és a leltár központi telepítési hatókörének korlátozása](automation-scope-configurations-change-tracking.md).

    ![Azure-beli virtuális gépek engedélyezése](media/automation-enable-changes-from-auto-acct/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Nem Azure-beli virtuális gépek engedélyezése

Az Azure-ban nem szereplő gépeket manuálisan kell hozzáadni. 

1. Az Automation-fiókban válassza a **leltár** vagy a **változások követése** lehetőséget a **konfiguráció**felügyelete alatt.

2. Kattintson a **nem Azure-beli gép hozzáadása**lehetőségre. Ez a művelet egy új böngészőablakot nyit meg, amely [útmutatást nyújt a Windows log Analytics ügynökének telepítésére és konfigurálására](../azure-monitor/platform/log-analytics-agent.md) , hogy a gép megkezdhesse a Change Tracking és a leltározási műveletek bejelentését. Ha olyan gépet engedélyez, amelyet jelenleg Operations Manager felügyel, nincs szükség új ügynökre, és a munkaterület adatai bekerülnek a meglévő ügynökbe.

## <a name="enable-machines-in-the-workspace"></a>Gépek engedélyezése a munkaterületen

A manuálisan telepített gépeket vagy számítógépeket, amelyeket a munkaterülethez már jelentett, a Change Tracking és a leltár engedélyezéséhez hozzá kell adni a Azure Automationhoz. 

1. Az Automation-fiókban válassza a **leltár** vagy a **change Tracking** elemet a **konfiguráció**felügyelete alatt.

2. Válassza a **számítógépek kezelése**lehetőséget. Előfordulhat, hogy a **számítógépek kezelése** gomb szürkén jelenik meg, ha korábban az Engedélyezés lehetőséget választotta az **összes rendelkezésre álló és jövőbeli gépen**

    ![Mentett keresések](media/automation-enable-changes-from-auto-acct/managemachines.png)

3. Az összes rendelkezésre álló gép Change Tracking és leltárának engedélyezéséhez válassza az Engedélyezés lehetőséget az összes **elérhető gépen** a gépek kezelése lapon. Ez a művelet letiltja a vezérlőt a számítógépek egyenkénti hozzáadásához. Ez a feladat hozzáadja a munkaterületnek jelentett számítógépek összes nevét a számítógépcsoport mentett keresési lekérdezéséhez. Ha be van jelölve, ez a művelet letiltja a **számítógépek kezelése** gombot.

4. Az összes rendelkezésre álló és jövőbeli gép funkciójának engedélyezéséhez válassza az **Engedélyezés lehetőséget az összes rendelkezésre álló és jövőbeli gépen**. Ez a lehetőség törli a mentett kereséseket és a hatókör-konfigurációkat a munkaterületről, és megnyitja a szolgáltatást a munkaterületnek jelentett összes Azure-beli és nem Azure-beli gép számára. Ha bejelöli ezt a jelölőnégyzetet, ez a művelet véglegesen letiltja a **gépek kezelése** gombot, mivel nem maradt hatókör-konfiguráció.

5. Szükség esetén ismét hozzáadhatja a hatókör-konfigurációkat a kezdeti mentett keresések újbóli hozzáadásával. További információ: a [change Tracking és a leltár hatókör-konfigurációinak használata](automation-scope-configurations-change-tracking.md).

6. Egy vagy több gép funkciójának engedélyezéséhez válassza az **Engedélyezés a kiválasztott gépeken** lehetőséget, majd az egyes gépek mellett kattintson a **Hozzáadás** elemre a funkció engedélyezéséhez. Ez a feladat hozzáadja a kiválasztott számítógépneveket a szolgáltatáshoz tartozó számítógépcsoport mentett keresési lekérdezéséhez.

## <a name="next-steps"></a>További lépések

* A szolgáltatás részleteiért lásd: a [change Tracking és a leltár kezelése](change-tracking-file-contents.md).
* A hatókör-konfigurációkkal kapcsolatos további információkért lásd: a [change Tracking és a leltár központi telepítési hatókörének korlátozása](automation-scope-configurations-change-tracking.md).
* Ha szeretné megtudni, hogyan használhatja a szolgáltatást a környezetében telepített szoftverek azonosítására, tekintse meg a [virtuális gépeken telepített szoftverek felderítését](automation-tutorial-installed-software.md)ismertető témakört.
* Ha nem szeretné integrálni az Automation-fiókját egy Log Analytics munkaterülettel a szolgáltatás engedélyezésekor, olvassa el a [munkaterület összekapcsolása az Automation-fiókból](automation-unlink-workspace-change-tracking.md)című témakört.
* Amikor befejezte a virtuális gépek változásainak telepítését, eltávolíthatja őket a [virtuális gépek eltávolítása Change Tracking és leltárból](automation-remove-vms-from-change-tracking.md)című témakörben leírtak szerint.
* A szolgáltatással kapcsolatos általános problémák elhárításához tekintse meg a [change Tracking és a leltárral kapcsolatos problémák elhárítása](troubleshoot/change-tracking.md)című témakört.