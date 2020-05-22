---
title: Azure Automation Change Tracking és leltár engedélyezése egy runbook
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a Change Tracking és a leltár egy runbook.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: a11cc7f0c3e3c47fa1101272a73659368349fa84
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749171"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Change Tracking és leltár engedélyezése runbook

Ez a cikk azt ismerteti, hogyan használható a runbook a virtuális gépek [change Tracking és leltározási](change-tracking.md) funkciójának engedélyezésére a környezetben. Az Azure-beli virtuális gépek méretének engedélyezéséhez Change Tracking és leltár használatával engedélyeznie kell egy meglévő virtuális gépet. 

> [!NOTE]
> A Change Tracking és a leltár engedélyezésekor csak bizonyos régiók támogatottak Log Analytics munkaterület és egy Automation-fiók összekapcsolásához. A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](how-to/region-mappings.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](automation-offering-get-started.md) a gépek kezeléséhez.
* Egy [virtuális gép](../virtual-machines/windows/quick-create-portal.md).

## <a name="enable-change-tracking-and-inventory"></a>A Change Tracking és az Inventory engedélyezése 

1. A Azure Portal válassza az **Automation-fiókok**lehetőséget, majd válassza ki az Automation-fiókját a listában.
1. Válassza a **leltár** elemet a **konfiguráció kezelése**területen.
1. Válasszon ki egy meglévő Log Analytics munkaterületet, vagy hozzon létre egy újat. 
1. Kattintson az **Engedélyezés** gombra.

    ![A Change Tracking és az Inventory engedélyezése](media/automation-enable-changes-from-runbook/inventory-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Válassza ki a kezelni kívánt Azure-beli virtuális gépet

Ha a Change Tracking és a leltár engedélyezve van, hozzáadhat egy Azure-beli virtuális gépet a szolgáltatás általi felügyelethez.

1. Az Automation-fiókban válassza a **változások követése** vagy **leltározás** elemet a **konfiguráció**felügyelete alatt.

2. Kattintson az Azure-beli **virtuális gépek hozzáadása** lehetőségre a virtuális gép hozzáadásához.

3. Válassza ki a virtuális gépet a listából, és kattintson az **Engedélyezés**gombra. Ez a művelet engedélyezi a virtuális gép Change Tracking és leltározását.

   ![Virtuális gép Change Trackingának és leltározásának engedélyezése](media/automation-enable-changes-from-runbook/enable-change-tracking.png)

    > [!NOTE]
    > Ha a Change Tracking és a leltár befejezése előtt megpróbál engedélyezni egy másik szolgáltatást, a következő üzenet jelenik meg:`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Modulok telepítése és frissítése

Frissíteni kell a legújabb Azure-modulokra, és importálnia kell az az [. OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) modult a virtuális gép Change Trackingának és leltározásának sikeres engedélyezéséhez.

1. Az Automation-fiókban válassza a **megosztott erőforrások**alatt található **modulok** elemet. 
2. Az Azure-modulok a legújabb verzióra frissítéséhez válassza az **Azure-modulok frissítése** lehetőséget. 
3. Az **Igen** gombra kattintva frissítheti az összes meglévő Azure-modult a legújabb verzióra.

    ![Modulok frissítése](media/automation-enable-changes-from-runbook/update-modules.png)

4. Visszatérés a **modulokhoz** a **megosztott erőforrások**területen. 
5. Válassza a **Tallózás** katalógus lehetőséget a modul-gyűjtemény megnyitásához. 
6. Keresse meg az az. OperationalInsights, és importálja ezt a modult az Automation-fiókba.

    ![Az OperationalInsights modul importálása](media/automation-enable-changes-from-runbook/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Runbook importálása a Change Tracking és a leltár engedélyezéséhez

1. Az Automation-fiókban válassza a **runbookok** lehetőséget a **folyamat automatizálása**alatt.
2. Kattintson a **Böngészés a katalógusban** gombra.
3. Keressen a `update and change tracking` kifejezésre.
4. Válassza ki a runbook, és kattintson az **Importálás** elemre a forrás megtekintése lapon. 
5. A runbook az Automation-fiókba történő importálásához kattintson **az OK** gombra.

   ![Runbook importálása a telepítőhöz](media/automation-enable-changes-from-runbook/import-from-gallery.png)

6. A Runbook lapon kattintson a **Szerkesztés**, majd a **Közzététel**elemre. 
7. A Runbook közzététele ablaktáblán kattintson az **Igen** gombra a Runbook közzétételéhez.

## <a name="start-the-runbook"></a>A runbook indítása

A runbook elindításához engedélyeznie kell az Azure-beli virtuális gép Change Tracking és leltárát. Szükség van egy meglévő virtuális gépre és erőforráscsoporthoz, amelynek a funkciója engedélyezve van a paraméterek számára.

1. Nyissa meg az **enable-MultipleSolution** runbook.

   ![Többmegoldásos runbookok](media/automation-enable-changes-from-runbook/runbook-overview.png)

1. Kattintson a Start gombra, és adja meg a paraméterek értékét a következő mezőkben:

   * **VMNAME** – egy meglévő virtuális gép neve, amelyet hozzá szeretne adni a Change Trackinghoz és a leltárhoz. Hagyja üresen ezt a mezőt az erőforráscsoporthoz tartozó összes virtuális gép hozzáadásához.
   * **VMRESOURCEGROUP** – az engedélyezni kívánt virtuális gépek erőforráscsoport neve.
   * **SUBSCRIPTIONID** – az engedélyezni kívánt új virtuális gép előfizetés-azonosítója. A munkaterület előfizetésének használatához hagyja üresen ezt a mezőt. Ha más előfizetés-azonosítót használ, adja hozzá az Automation-fiókjához tartozó futtató fiókot az előfizetés közreműködőiként.
   * **ALREADYONBOARDEDVM** – annak a virtuális gépnek a neve, amelyet már manuálisan engedélyeztek a változásokhoz.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – annak az erőforráscsoportnak a neve, amelyhez a virtuális gép tartozik.
   * **Megoldástípusa** – adja meg a **változáskövetési**.

   ![Az Enable-MultipleSolution runbook paraméterei](media/automation-enable-changes-from-runbook/runbook-parameters.png)

1. A runbookfeladat indításához kattintson az **OK** gombra.
1. A folyamat előrehaladását és az esetleges hibákat a runbookfeladat oldalán követheti.

## <a name="next-steps"></a>További lépések

* [Runbookok-ütemterv](automation-schedules.md)
* [Change Tracking és leltár kezelése](change-tracking-file-contents.md)
* [A Change Tracking és a leltár hatókör-konfigurációinak használata](automation-scope-configurations-change-tracking.md)
* [Virtuális gépek szoftvereinek azonosítása](automation-tutorial-installed-software.md)
* [Munkaterület leválasztása az Automation-fiókból a Change Tracking és a leltárhoz](automation-unlink-workspace-change-tracking.md)
* [Virtuális gépek eltávolítása Change Tracking és leltárból](automation-remove-vms-from-change-tracking.md)
* [Azure-beli virtuális gépek változásainak hibáinak megoldása](automation-tutorial-troubleshoot-changes.md)
* [A Change Tracking és a leltárral kapcsolatos problémák elhárítása](troubleshoot/change-tracking.md)
