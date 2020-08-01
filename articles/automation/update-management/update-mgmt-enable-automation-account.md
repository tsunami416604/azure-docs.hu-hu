---
title: Azure Automation Update Management engedélyezése az Automation-fiókból
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a Update Management Automation-fiókból.
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 930861c61843c5963c83d8fa6dc1efdce20853f4
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450244"
---
# <a name="enable-update-management-from-an-automation-account"></a>Az Update Management engedélyezése Automation-fiókból

Ez a cikk azt ismerteti, hogyan használható az Automation-fiókja a virtuális gépek [Update Management](update-mgmt-overview.md) funkciójának engedélyezésére a környezetben. Az Azure-beli virtuális gépek méretének engedélyezéséhez Update Management használatával engedélyeznie kell egy meglévő virtuális gépet.

> [!NOTE]
> A Update Management engedélyezésekor csak bizonyos régiók támogatottak Log Analytics munkaterület és egy Automation-fiók összekapcsolásához. A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](../index.yml) a gépek kezeléséhez.
* Egy [virtuális gép](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="enable-update-management"></a>Az Update Management engedélyezése

1. Az Automation-fiókban válassza **a frissítés kezelése** lehetőséget az **Update Management**alatt.

2. Válassza ki az Log Analytics munkaterületet és az Automation-fiókot, és válassza az **Engedélyezés** lehetőséget Update Management engedélyezéséhez. A telepítés elvégzése akár 15 percet is igénybe vehet.

    ![Az Update Management engedélyezése](media/update-mgmt-enable-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Azure-beli virtuális gépek engedélyezése

1. Az Automation-fiókban válassza **az Update Management alatt az** **Update Management** elemet.

2. Válassza az **+ Azure-beli virtuális gépek hozzáadása** lehetőséget, és válasszon ki egy vagy több virtuális gépet a listából. A nem engedélyezhető virtuális gépek szürkén jelennek meg, és nem választhatók ki. Az Azure-beli virtuális gépek bármely régióban létezhetnek, függetlenül az Automation-fiókja helyétől.

3. Válassza az **Engedélyezés** lehetőséget, ha a kiválasztott virtuális gépeket hozzá szeretné adni a számítógépcsoport mentett kereséshez a szolgáltatáshoz.

    ![Azure-beli virtuális gépek engedélyezése](media/update-mgmt-enable-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Nem Azure-beli virtuális gépek engedélyezése

Az Azure-ban nem szereplő gépeket manuálisan kell hozzáadni.

1. Az Automation-fiókban válassza **a frissítés kezelése lehetőséget az** **Update Management**alatt.

2. Válassza a **nem Azure-beli gép hozzáadása**lehetőséget. Ez a művelet egy új böngészőablakot nyit meg, amely [útmutatást nyújt a Windows log Analytics ügynökének telepítéséhez és konfigurálásához](../../azure-monitor/platform/log-analytics-agent.md) , hogy a gép megkezdhesse a jelentéskészítést Update Management. Ha olyan gépet engedélyez, amelyet jelenleg a Operations Manager felügyel, nincs szükség új ügynökre. A munkaterület adatai hozzáadódnak az ügynökök konfigurációjához.

## <a name="enable-machines-in-the-workspace"></a>Gépek engedélyezése a munkaterületen

A munkaterülethez már jelentést küldő gépeket vagy gépeket manuálisan kell hozzáadni a Azure Automationhoz a Update Management engedélyezéséhez.

1. Az Automation-fiókban válassza **a frissítés kezelése lehetőséget az** **Update Management**alatt.

2. Válassza a **számítógépek kezelése**lehetőséget. Előfordulhat, hogy a **számítógépek kezelése** gomb szürkén jelenik meg, ha korábban az Engedélyezés lehetőséget választotta az **összes rendelkezésre álló és jövőbeli gépen**

    ![Mentett keresések](media/update-mgmt-enable-automation-account/managemachines.png)

3. Az összes rendelkezésre álló gép Update Management engedélyezéséhez válassza az Engedélyezés lehetőséget az összes **elérhető gépen** a gépek kezelése lapon. Ez a művelet letiltja a vezérlőt a számítógépek egyenkénti hozzáadásához. Ez a feladat hozzáadja a munkaterületnek jelentett számítógépek összes nevét a számítógépcsoport mentett keresési lekérdezéséhez. Ha be van jelölve, ez a művelet letiltja a **számítógépek kezelése** gombot.

4. Az összes rendelkezésre álló és jövőbeli gép funkciójának engedélyezéséhez válassza az **Engedélyezés lehetőséget az összes rendelkezésre álló és jövőbeli gépen**. Ez a lehetőség törli a mentett kereséseket és a hatókör-konfigurációkat a munkaterületről, és megnyitja a szolgáltatást a munkaterületnek jelentett összes Azure-beli és nem Azure-beli gép számára. Ha bejelöli ezt a jelölőnégyzetet, ez a művelet véglegesen letiltja a **gépek kezelése** gombot, mivel nem maradt hatókör-konfiguráció.

5. Szükség esetén ismét hozzáadhatja a hatókör-konfigurációkat a kezdeti mentett keresések újbóli hozzáadásával. További információ: a [Update Management központi telepítési hatókörének korlátozása](update-mgmt-scope-configuration.md).

6. Egy vagy több gép funkciójának engedélyezéséhez válassza az **Engedélyezés a kiválasztott gépeken** lehetőséget, majd az egyes gépek melletti **Hozzáadás** lehetőséget. Ez a feladat hozzáadja a kiválasztott számítógépneveket a szolgáltatáshoz tartozó számítógépcsoport mentett keresési lekérdezéséhez.

## <a name="next-steps"></a>További lépések

* A Update Management virtuális gépekhez való használatához lásd: [a virtuális gépek frissítéseinek és javításának kezelése](update-mgmt-manage-updates-for-vm.md).

* Az általános Update Management hibák elhárításával kapcsolatban lásd: [Update Management problémák elhárítása](../troubleshoot/update-management.md).