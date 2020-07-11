---
title: Azure Automation Update Management engedélyezése az Automation-fiókból
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a Update Management Automation-fiókból.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: d756cf9705cc1ce0041c26dc3ef022c150c514c0
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186061"
---
# <a name="enable-update-management-from-an-automation-account"></a>Az Update Management engedélyezése Automation-fiókból

Ez a cikk azt ismerteti, hogyan használható az Automation-fiókja a virtuális gépek [Update Management](automation-update-management.md) funkciójának engedélyezésére a környezetben. Az Azure-beli virtuális gépek méretének engedélyezéséhez Update Management használatával engedélyeznie kell egy meglévő virtuális gépet. 

> [!NOTE]
> A Update Management engedélyezésekor csak bizonyos régiók támogatottak Log Analytics munkaterület és egy Automation-fiók összekapcsolásához. A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](how-to/region-mappings.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](./index.yml) a gépek kezeléséhez.
* Egy [virtuális gép](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba https://portal.azure.com .

## <a name="enable-update-management"></a>Az Update Management engedélyezése

1. Az Automation-fiókban válassza **a frissítés kezelése** lehetőséget az **Update Management**alatt.

2. Válassza ki az Log Analytics munkaterületet és Automation-fiókot, majd kattintson az **Engedélyezés** elemre Update Management engedélyezéséhez. A telepítés elvégzése akár 15 percet is igénybe vehet.

    ![Az Update Management engedélyezése](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Azure-beli virtuális gépek engedélyezése

1. Az Automation-fiókban válassza **az Update Management alatt az** **Update Management** elemet.

2. Kattintson az **+ Azure-beli virtuális gépek hozzáadása** lehetőségre, és válasszon ki egy vagy több virtuális gépet a listából. A nem engedélyezhető virtuális gépek szürkén jelennek meg, és nem választhatók ki. Az Azure-beli virtuális gépek bármely régióban létezhetnek, függetlenül az Automation-fiókja helyétől. 

3. Kattintson az **Engedélyezés** lehetőségre a kiválasztott virtuális gépek hozzáadásához a csoport mentett kereséséhez a szolgáltatásban.

    ![Azure-beli virtuális gépek engedélyezése](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Nem Azure-beli virtuális gépek engedélyezése

Az Azure-ban nem szereplő gépeket manuálisan kell hozzáadni. 

1. Az Automation-fiókban válassza **a frissítés kezelése lehetőséget az** **Update Management**alatt.

2. Kattintson a **nem Azure-beli gép hozzáadása**lehetőségre. Ez a művelet egy új böngészőablakot nyit meg, amely [útmutatást nyújt a Windows log Analytics ügynökének telepítéséhez és konfigurálásához](../azure-monitor/platform/log-analytics-agent.md) , hogy a gép el tudja kezdeni a Update Management műveleteket. Ha olyan gépet engedélyez, amelyet jelenleg Operations Manager felügyel, nincs szükség új ügynökre, és a munkaterület adatai bekerülnek a meglévő ügynökbe.

## <a name="enable-machines-in-the-workspace"></a>Gépek engedélyezése a munkaterületen

A munkaterülethez már jelentést küldő gépeket vagy gépeket manuálisan kell hozzáadni a Azure Automationhoz a Update Management engedélyezéséhez. 

1. Az Automation-fiókban válassza **a frissítés kezelése lehetőséget az** **Update Management**alatt.

2. Válassza a **számítógépek kezelése**lehetőséget. Előfordulhat, hogy a **számítógépek kezelése** gomb szürkén jelenik meg, ha korábban az Engedélyezés lehetőséget választotta az **összes rendelkezésre álló és jövőbeli gépen**

    ![Mentett keresések](media/automation-onboard-solutions-from-automation-account/managemachines.png)

4. Az összes rendelkezésre álló gép Update Management engedélyezéséhez válassza az Engedélyezés lehetőséget az összes **elérhető gépen** a gépek kezelése lapon. Ez a művelet letiltja a vezérlőt a számítógépek egyenkénti hozzáadásához. Ez a feladat hozzáadja a munkaterületnek jelentett számítógépek összes nevét a számítógépcsoport mentett keresési lekérdezéséhez. Ha be van jelölve, ez a művelet letiltja a **számítógépek kezelése** gombot.

5. Az összes rendelkezésre álló és jövőbeli gép funkciójának engedélyezéséhez válassza az **Engedélyezés lehetőséget az összes rendelkezésre álló és jövőbeli gépen**. Ez a lehetőség törli a mentett kereséseket és a hatókör-konfigurációkat a munkaterületről, és megnyitja a szolgáltatást a munkaterületnek jelentett összes Azure-beli és nem Azure-beli gép számára. Ha bejelöli ezt a jelölőnégyzetet, ez a művelet véglegesen letiltja a **gépek kezelése** gombot, mivel nem maradt hatókör-konfiguráció.

6. Szükség esetén ismét hozzáadhatja a hatókör-konfigurációkat a kezdeti mentett keresések újbóli hozzáadásával. További információ: a [Update Management központi telepítési hatókörének korlátozása](automation-scope-configurations-update-management.md).

7. Egy vagy több gép funkciójának engedélyezéséhez válassza az **Engedélyezés a kiválasztott gépeken** lehetőséget, majd az egyes gépek mellett kattintson a **Hozzáadás** elemre a funkció engedélyezéséhez. Ez a feladat hozzáadja a kiválasztott számítógépneveket a szolgáltatáshoz tartozó számítógépcsoport mentett keresési lekérdezéséhez.

## <a name="next-steps"></a>További lépések

* Az Update Management virtuális gépekhez való használatához lásd: [Az Azure-beli virtuális gépek frissítéseinek és javításának kezelése](automation-tutorial-update-management.md).
* Az általános Update Management hibák elhárításával kapcsolatban lásd: [Update Management problémák elhárítása](troubleshoot/update-management.md).
* A Windows Update agenttel kapcsolatos problémák elhárításához tekintse meg a [Windows Update Agent problémáinak elhárítása](troubleshoot/update-agent-issues.md)című témakört.
* A Linux Update agenttel kapcsolatos problémák elhárításához tekintse meg a [Linux frissítési ügynökkel kapcsolatos problémák elhárítása](troubleshoot/update-agent-issues-linux.md)című témakört.
