---
title: Azure Automation Update Management engedélyezése a runbook-ből
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a Update Management egy runbook.
services: automation
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: a5b6fe5cf83eaa18b34a00767e14e75737aa055e
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450250"
---
# <a name="enable-update-management-from-a-runbook"></a>Az Update Management engedélyezése runbookból

Ez a cikk azt ismerteti, hogyan használható a runbook a virtuális gépek [Update Management](update-mgmt-overview.md) funkciójának engedélyezésére a környezetben. Az Azure-beli virtuális gépek méretének engedélyezéséhez Update Management használatával engedélyeznie kell egy meglévő virtuális gépet. 

> [!NOTE]
> A Update Management engedélyezésekor csak bizonyos régiók támogatottak Log Analytics munkaterület és egy Automation-fiók összekapcsolásához. A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](../index.yml) a gépek kezeléséhez.
* Egy [virtuális gép](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="enable-update-management"></a>Az Update Management engedélyezése

1. Az Automation-fiókjában válassza a **Update Management** lehetőséget **Update Management**alatt.

2. Válassza ki a Log Analytics munkaterületet, majd kattintson az **Engedélyezés**gombra. Amíg a Update Management engedélyezve van, kék szalagcím jelenik meg.

    ![Az Update Management engedélyezése](media/update-mgmt-enable-runbook/update-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Válassza ki a kezelni kívánt Azure-beli virtuális gépet

Ha a Update Management engedélyezve van, hozzáadhat egy Azure-beli virtuális gépet a frissítések fogadásához.

1. Az Automation-fiókban válassza **a frissítés kezelése lehetőséget az** **Update Management**alatt.

2. Válassza az Azure-beli **virtuális gépek hozzáadása** lehetőséget a virtuális gép hozzáadásához.

3. Válassza ki a virtuális gépet a listából, és kattintson az **Engedélyezés** gombra a virtuális gép frissítésekhez való beállításához.

   ![Virtuális gép Update Managementának engedélyezése](media/update-mgmt-enable-runbook/enable-update.png)

    > [!NOTE]
    > Ha a Update Management telepítésének befejezése előtt megpróbál engedélyezni egy másik szolgáltatást, a következő üzenet jelenik meg:`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Modulok telepítése és frissítése

Frissíteni kell a legújabb Azure-modulokra, és importálnia kell az az [. OperationalInsights](/powershell/module/az.operationalinsights/?view=azps-3.7.0) modult, hogy sikeresen engedélyezze Update Management a virtuális gépek számára.

1. Az Automation-fiókban válassza a **megosztott erőforrások**alatt található **modulok** elemet.
2. Az Azure-modulok a legújabb verzióra frissítéséhez válassza az **Azure-modulok frissítése** lehetőséget.
3. Az **Igen** gombra kattintva frissítheti az összes meglévő Azure-modult a legújabb verzióra.

    ![Modulok frissítése](media/update-mgmt-enable-runbook/update-modules.png)

4. Visszatérés a **modulokhoz** a **megosztott erőforrások**területen.
5. Válassza a **Tallózás** katalógus lehetőséget a modul-gyűjtemény megnyitásához.
6. Keresse meg `Az.OperationalInsights` és importálja a modult az Automation-fiókjába.

    ![Az OperationalInsights modul importálása](media/update-mgmt-enable-runbook/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-update-management"></a>Runbook importálása a Update Management engedélyezéséhez

1. Az Automation-fiókban válassza a **runbookok** lehetőséget a **folyamat automatizálása**alatt.
2. Kattintson a **Böngészés a katalógusban** gombra.
3. Keressen a `update and change tracking` kifejezésre.
4. Válassza ki a runbook, és kattintson az **Importálás** elemre a forrás megtekintése lapon.
5. A runbook az Automation-fiókba történő importálásához kattintson **az OK** gombra.

   ![Runbook importálása a telepítőhöz](media/update-mgmt-enable-runbook/import-from-gallery.png)

6. A Runbook lapon kattintson a **Szerkesztés**, majd a **Közzététel**elemre.
7. A Runbook közzététele ablaktáblán kattintson az **Igen** gombra a Runbook közzétételéhez.

## <a name="start-the-runbook"></a>A runbook indítása

A runbook elindításához engedélyezve kell lennie Update Management egy Azure-beli virtuális gépen. Szükség van egy meglévő virtuális gépre és erőforráscsoporthoz, amelynek a funkciója engedélyezve van a paraméterek számára.

1. Nyissa meg az **enable-MultipleSolution** runbook.

   ![Több megoldás runbook](media/update-mgmt-enable-runbook/runbook-overview.png)

2. Kattintson a Start gombra, és adja meg a paraméterek értékét a következő mezőkben:

   * **VMNAME** – egy meglévő virtuális gép neve Update Managementhoz való hozzáadáshoz. Hagyja üresen ezt a mezőt az erőforráscsoporthoz tartozó összes virtuális gép hozzáadásához.
   * **VMRESOURCEGROUP** – az engedélyezni kívánt virtuális gépek erőforráscsoport neve.
   * **SUBSCRIPTIONID** – az engedélyezni kívánt új virtuális gép előfizetés-azonosítója. A munkaterület előfizetésének használatához hagyja üresen ezt a mezőt. Ha más előfizetés-azonosítót használ, adja hozzá az Automation-fiókjához tartozó futtató fiókot az előfizetés közreműködőiként.
   * **ALREADYONBOARDEDVM** – annak a virtuális gépnek a neve, amely már manuálisan engedélyezve van a frissítésekhez.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – annak az erőforráscsoportnak a neve, amelyhez a virtuális gép tartozik.
   * **Megoldástípusa** – adja meg a **frissítéseket**.

   ![Az Enable-MultipleSolution runbook paraméterei](media/update-mgmt-enable-runbook/runbook-parameters.png)

3. A runbookfeladat indításához kattintson az **OK** gombra.

4. A runbook feladat és a **feladatok** lapról fellépő hibák állapotának figyelése.

## <a name="next-steps"></a>További lépések

* A Update Management virtuális gépekhez való használatához lásd: [a virtuális gépek frissítéseinek és javításának kezelése](update-mgmt-manage-updates-for-vm.md).

* Az általános Update Management hibák elhárításával kapcsolatban lásd: [Update Management problémák elhárítása](../troubleshoot/update-management.md).
